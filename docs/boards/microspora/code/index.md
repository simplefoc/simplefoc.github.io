---
layout: default
title: Microspora Code
parent: <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>
grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "Writing SimpleFOC firmware for the Microspora board, step by step."
nav_order: 2
permalink: /microspora_code
has_children: true
has_toc: false
toc: true
---

# Writing the code for <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>

Before starting, make sure you have [set up the toolchain](microspora_setup) and [connected the hardware](microspora_connection).

Everything the board needs is on board: the DRV8316 driver, the MT6701 magnetic sensor, current sensing and supply voltage sensing. The code below brings them up one at a time, so if something doesn't work you know exactly which part to look at.

- [Step 1. Testing the sensor](#step-1-testing-the-sensor)
- [Step 2. Testing the driver](#step-2-testing-the-driver)
- [Step 3. Open-loop motor test](#step-3-open-loop-motor-test)
- [Step 4. Closed-loop control](#step-4-closed-loop-control)
- [Step 5. FOC current control](#step-5-foc-current-control)
- [Step 6. Running FOC from a timer](#step-6-running-foc-from-a-timer-interrupt)
- [Complete example](#complete-example)

[Toolchain and flashing](microspora_setup){: .btn .btn-docs} [Pinout reference](microspora_pinout){: .btn .btn-docs} [CAN communication](microspora_can){: .btn .btn-docs}

## Step 1. Testing the sensor

The MT6701 sensor is connected to **SPI1** using SSI mode. It needs its own `SPIClass` instance because the default `SPI` pins are not the ones used on this board.

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>
#include "encoders/mt6701/MagneticSensorMT6701SSI.h"

// MT6701 on SPI1: MOSI (not connected), MISO, SCK
SPIClass SPI_1(PA7, PA6, PA5);
SPISettings mt6701_settings(10000000, MT6701_BITORDER, SPI_MODE2);
MagneticSensorMT6701SSI sensor(PA4, mt6701_settings); // CS = PA4

void setup() {
  Serial.begin(250000);
  _delay(3000);
  sensor.init(&SPI_1);
}

void loop() {
  sensor.update();
  Serial.println(sensor.getAngle());
  _delay(10);
}
```

Turn the motor by hand. The angle should change smoothly and increase by about 6.28 (2π) per turn. Make sure there is a 6mm diametrically magnetized magnet on the motor shaft, centred under the sensor with a 1-2mm gap, see [magnet and mounting](microspora_connection#magnet-and-mounting).

[Read more about testing the sensor](test_sensor){: .btn .btn-docs}

## Step 2. Testing the driver

The DRV8316 is driven with 6 PWM signals and configured over **SPI3**. The SPI3 pins need the `_ALT1` variants, so again a separate `SPIClass` instance is used.

```cpp
#include "drivers/drv8316/drv8316.h"

// DRV8316 on SPI3: MOSI, MISO, SCK
SPIClass SPI_3(PB5_ALT1, PB4_ALT1, PC10);
DRV8316Driver6PWM driver(PA10, PB15, PA9, PB14, PA8, PB13, PC4, false); // 6 PWM pins, CS = PC4

void setup() {
  // ...
  driver.voltage_power_supply = 12;
  driver.pwm_frequency = 25000;
  driver.init(&SPI_3);
  driver.setPWMMode(PWM6_Mode);
  driver.setBuckVoltage(VB_5V);   // onboard 5V buck output
  driver.setOCPMode(NoAction);
}
```

If the driver initialises without reporting a fault, it is ready.

### Measuring the supply voltage

The supply voltage is measured on **PB11** through a 4.7k / 47k divider (scaling factor 11). Use it to set `voltage_power_supply` automatically and to refuse to start on a missing or too low supply:

```cpp
float readSupply() {
  analogReadResolution(12);
  return analogRead(PB11) * 3.3f / 4095.0f * 11.0f;
}

void setup() {
  // ...
  float voltage = readSupply();
  while (voltage < 8.0f) {
    Serial.println("Supply voltage too low!");
    _delay(1000);
    voltage = readSupply();
  }
  driver.voltage_power_supply = voltage;
}
```

## Step 3. Open-loop motor test

Now add a motor and run it in open loop. This checks the driver and the motor wiring without involving the sensor.

For a BLDC motor:
```cpp
BLDCMotor motor = BLDCMotor(pole_pairs);
```

For a stepper motor in [hybrid stepper mode](hybrid_stepper_theory), wired as described in the [hardware guide](microspora_connection):
```cpp
HybridStepperMotor motor = HybridStepperMotor(pole_pairs); // steps per revolution / 4, e.g. 50 for a 200-step NEMA17
```

<blockquote class="warning" markdown="1"><p class="heading">Stepper phase order</p>
The common stepper phase is connected to the middle terminal (PHB), and `HybridStepperMotor` expects the common phase to be the **3rd** driver phase. So for steppers, swap the B and C pins in the driver constructor:

```cpp
DRV8316Driver6PWM driver(PA10, PB15, PA8, PB13, PA9, PB14, PC4, false); // A, C, B (common)
```
</blockquote>

```cpp
void setup() {
  // ... driver init
  motor.linkDriver(&driver);
  motor.voltage_limit = 2;   // start low!
  motor.controller = MotionControlType::velocity_openloop;
  motor.init();
}

void loop() {
  motor.loopFOC();
  motor.move(5); // 5 rad/s
}
```

[Read more about the open-loop test](test_motor_driver){: .btn .btn-docs}

## Step 4. Closed-loop control

Link the sensor to the motor and call `initFOC()`. This aligns the sensor with the motor's electrical angle.

```cpp
void setup() {
  // ... sensor and driver init
  motor.linkSensor(&sensor);
  motor.linkDriver(&driver);
  motor.voltage_limit = 2;
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::velocity;
  motor.init();
  motor.initFOC();
}

void loop() {
  motor.loopFOC();
  motor.move(5);
}
```

[Read more about closing the loop](test_closedloop){: .btn .btn-docs}

## Step 5. FOC current control

The DRV8316 has built-in current sense amplifiers on all three phases, connected to **PA0, PA1, PA2**. The amplifier gain is set over SPI and **must match** the gain given to `LowsideCurrentSense` (in mV/A).

| `setCurrentSenseGain()` | `LowsideCurrentSense` gain | Approx. measurable range |
| --- | --- | --- |
| `Gain_0V15` | `150.0f` | ±11A |
| `Gain_0V3` | `300.0f` | ±5.5A |
| `Gain_0V6` | `600.0f` | ±2.7A |
| `Gain_1V2` | `1200.0f` | ±1.4A |

Pick the most sensitive gain that still covers the peak current of your motor.

For steppers, only the two motor phases are measured, in the same order as the driver: `LowsideCurrentSense current_sense(300.0f, PA0, PA2);`

```cpp
LowsideCurrentSense current_sense(300.0f, PA0, PA1, PA2);

void setup() {
  // ... driver init
  driver.setCurrentSenseGain(Gain_0V3); // must match the 300 mV/A above

  current_sense.linkDriver(&driver);
  current_sense.init();
  current_sense.skip_align = true; // the phase order is fixed on the board
  motor.linkCurrentSense(&current_sense);

  motor.torque_controller = TorqueControlType::foc_current;
  // ... motor.init(), motor.initFOC()
}
```

[Read more about current control](foc_current){: .btn .btn-docs}

## Step 6. Running FOC from a timer interrupt

For the most consistent performance, run `loopFOC()` and `move()` from a hardware timer at a fixed rate, and keep communication and monitoring in `loop()`.

```cpp
void setup() {
  // ... everything above
  HardwareTimer* timer = new HardwareTimer(TIM6);
  timer->setOverflow(10000, HERTZ_FORMAT); // 10kHz
  timer->attachInterrupt([]() {
    motor.loopFOC();
    motor.move();
  });
  timer->resume();
}

void loop() {
  motor.monitor();
  command.run();
}
```

<blockquote class="warning" markdown="1"><p class="heading">Timer choice</p>
Don't use TIM1 (used for the motor PWM) or TIM8 (used by the 5-pin header in encoder, PWM input and step/dir modes). The basic timers TIM6 and TIM7 are good choices.
</blockquote>

## Complete example

Here is everything above put together: FOC current control in torque mode, running at 10kHz from a timer, with the [Commander interface](commander_interface) on USB serial so you can change the target and tune the motor at runtime.

Before uploading, set `POLE_PAIRS` for your motor and adjust the voltage and current limits.

<a href="javascript:show('bldc','ms');" class="btn btn-ms btn-bldc btn-primary">BLDC motor</a>
<a href="javascript:show('stepper','ms');" class="btn btn-ms btn-stepper">Stepper motor</a>

<div class="ms ms-bldc" markdown="1">

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>
#include "drivers/drv8316/drv8316.h"
#include "encoders/mt6701/MagneticSensorMT6701SSI.h"

#define POLE_PAIRS 7

// SPI buses
SPIClass SPI_1(PA7, PA6, PA5);            // MT6701 sensor: MOSI (not connected), MISO, SCK
SPIClass SPI_3(PB5_ALT1, PB4_ALT1, PC10); // DRV8316 driver: MOSI, MISO, SCK

// MT6701 magnetic sensor
SPISettings mt6701_settings(10000000, MT6701_BITORDER, SPI_MODE2);
MagneticSensorMT6701SSI sensor(PA4, mt6701_settings);

// DRV8316 driver, 6 PWM
DRV8316Driver6PWM driver(PA10, PB15, PA9, PB14, PA8, PB13, PC4, false);

// current sensing, gain must match driver.setCurrentSenseGain() below
LowsideCurrentSense current_sense(300.0f, PA0, PA1, PA2);

// motor
BLDCMotor motor = BLDCMotor(POLE_PAIRS);

// commander interface
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

float readSupply() {
  analogReadResolution(12);
  return analogRead(PB11) * 3.3f / 4095.0f * 11.0f; // 4.7k / 47k divider
}

void setup() {
  Serial.begin(250000);
  _delay(3000); // give the USB serial time to connect
  SimpleFOCDebug::enable(&Serial);

  pinMode(PC6, OUTPUT);
  digitalWrite(PC6, HIGH); // LED on during initialisation

  // wait for a valid supply voltage
  float voltage = readSupply();
  while (voltage < 8.0f) {
    Serial.println("Supply voltage too low!");
    _delay(1000);
    voltage = readSupply();
  }
  Serial.print("Supply voltage: ");
  Serial.println(voltage);

  // sensor
  sensor.init(&SPI_1);
  motor.linkSensor(&sensor);

  // driver
  driver.voltage_power_supply = voltage;
  driver.pwm_frequency = 25000;
  driver.dead_zone = 0.001f;
  driver.init(&SPI_3);
  driver.setPWMMode(PWM6_Mode);
  driver.setBuckVoltage(VB_5V);
  driver.setOCPMode(NoAction);
  driver.setSlew(Slew_200Vus);
  driver.setCurrentSenseGain(Gain_0V3); // 300 mV/A
  motor.linkDriver(&driver);

  // current sense
  current_sense.linkDriver(&driver);
  current_sense.init();
  current_sense.skip_align = true;
  motor.linkCurrentSense(&current_sense);

  // control config
  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.voltage_limit = 6;   // [V]
  motor.current_limit = 2;   // [A]
  motor.velocity_limit = 50; // [rad/s]

  // monitoring (start/end chars match the commander ID 'M' for the webcontroller)
  motor.useMonitoring(Serial);
  motor.monitor_start_char = 'M';
  motor.monitor_end_char = 'M';
  motor.monitor_downsample = 0; // disable monitoring 

  if (!motor.init()) {
    Serial.println("Motor init failed!");
    return;
  }
  if (!motor.initFOC()) {
    Serial.println("FOC init failed!");
    return;
  }

  motor.target = 0; // [A]
  command.add('M', doMotor, "motor");
  Serial.println("Motor ready. Set the target with: M0.5");

  digitalWrite(PC6, LOW); // LED off when ready

  // run FOC at 10kHz from a hardware timer
  HardwareTimer* timer = new HardwareTimer(TIM6);
  timer->setOverflow(10000, HERTZ_FORMAT);
  timer->attachInterrupt([]() {
    motor.loopFOC();
    motor.move();
  });
  timer->resume();
}

void loop() {
  motor.monitor();
  command.run();
}
```

</div>

<div class="ms ms-stepper hide" markdown="1">

The stepper is wired in [hybrid stepper mode](hybrid_stepper_theory) with the common phase on the middle terminal (PHB), see the [hardware guide](microspora_connection). That is why the driver phases are given in the order A, C, B and only phases A and C are measured.

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>
#include "drivers/drv8316/drv8316.h"
#include "encoders/mt6701/MagneticSensorMT6701SSI.h"

#define POLE_PAIRS 50 // steps per revolution / 4, e.g. 50 for a 200-step NEMA17

// SPI buses
SPIClass SPI_1(PA7, PA6, PA5);            // MT6701 sensor: MOSI (not connected), MISO, SCK
SPIClass SPI_3(PB5_ALT1, PB4_ALT1, PC10); // DRV8316 driver: MOSI, MISO, SCK

// MT6701 magnetic sensor
SPISettings mt6701_settings(10000000, MT6701_BITORDER, SPI_MODE2);
MagneticSensorMT6701SSI sensor(PA4, mt6701_settings);

// DRV8316 driver, 6 PWM
// the common stepper phase is on the middle terminal (PHB), so it must be the 3rd phase
DRV8316Driver6PWM driver(PA10, PB15, PA8, PB13, PA9, PB14, PC4, false); // A, C, B (common)

// current sensing, gain must match driver.setCurrentSenseGain() below
// only the two stepper phases are measured (A and C), in the same order as the driver
LowsideCurrentSense current_sense(300.0f, PA0, PA2);

// motor
HybridStepperMotor motor = HybridStepperMotor(POLE_PAIRS);

// commander interface
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

float readSupply() {
  analogReadResolution(12);
  return analogRead(PB11) * 3.3f / 4095.0f * 11.0f; // 4.7k / 47k divider
}

void setup() {
  Serial.begin(250000);
  _delay(3000); // give the USB serial time to connect
  SimpleFOCDebug::enable(&Serial);

  pinMode(PC6, OUTPUT);
  digitalWrite(PC6, HIGH); // LED on during initialisation

  // wait for a valid supply voltage
  float voltage = readSupply();
  while (voltage < 8.0f) {
    Serial.println("Supply voltage too low!");
    _delay(1000);
    voltage = readSupply();
  }
  Serial.print("Supply voltage: ");
  Serial.println(voltage);

  // sensor
  sensor.init(&SPI_1);
  motor.linkSensor(&sensor);

  // driver
  driver.voltage_power_supply = voltage;
  driver.pwm_frequency = 25000;
  driver.dead_zone = 0.001f;
  driver.init(&SPI_3);
  driver.setPWMMode(PWM6_Mode);
  driver.setBuckVoltage(VB_5V);
  driver.setOCPMode(NoAction);
  driver.setSlew(Slew_200Vus);
  driver.setCurrentSenseGain(Gain_0V3); // 300 mV/A
  motor.linkDriver(&driver);

  // current sense
  current_sense.linkDriver(&driver);
  current_sense.init();
  current_sense.skip_align = true;
  motor.linkCurrentSense(&current_sense);

  // control config
  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.voltage_limit = 6;   // [V]
  motor.current_limit = 2;   // [A]
  motor.velocity_limit = 50; // [rad/s]

  // monitoring (start/end chars match the commander ID 'M' for the webcontroller)
  motor.useMonitoring(Serial);
  motor.monitor_start_char = 'M';
  motor.monitor_end_char = 'M';
  motor.monitor_downsample = 0; // disable monitoring 

  if (!motor.init()) {
    Serial.println("Motor init failed!");
    return;
  }
  if (!motor.initFOC()) {
    Serial.println("FOC init failed!");
    return;
  }

  motor.target = 0; // [A]
  command.add('M', doMotor, "motor");
  Serial.println("Motor ready. Set the target with: M0.5");

  digitalWrite(PC6, LOW); // LED off when ready

  // run FOC at 10kHz from a hardware timer
  HardwareTimer* timer = new HardwareTimer(TIM6);
  timer->setOverflow(10000, HERTZ_FORMAT);
  timer->attachInterrupt([]() {
    motor.loopFOC();
    motor.move();
  });
  timer->resume();
}

void loop() {
  motor.monitor();
  command.run();
}
```

</div>

Open the serial monitor at 250000 baud and send `M0.5` to apply 0.5A of torque current. See the [Commander interface](commander_interface) docs for all the commands, e.g. changing the motion control mode or tuning the PID controllers.

### Using the web controller

The example is ready for the [SimpleFOC web controller](webcontroller) as is: open [webcontroller.simplefoc.com](https://webcontroller.simplefoc.com) in a Chromium-based browser, click **Connect**, select the board's serial port at 250000 baud, and you can control, tune and plot the motor in real time without writing any extra code.

## Going further

### Measuring the motor parameters and auto-tuning the current loop

Since the board has current sensing, the library can measure the motor phase resistance and inductance for you, and then use them to tune the current controller automatically. Add this after `motor.initFOC()`:

```cpp
motor.characteriseMotor(3.0f);       // measure R and L, applying 3V
motor.tuneCurrentController(500.0f); // tune the current PID for a 500Hz bandwidth
```

The measured values are printed to the serial monitor. Once you have them, you can set them directly (`motor.phase_resistance`, `motor.axis_inductance.d` and `motor.axis_inductance.q`) and skip the measurement on every startup.

[Motor characterisation](motor_characterisation){: .btn .btn-docs} [Tuning the current loop](tuning_current_loop){: .btn .btn-docs}

### Calibrating the sensor

If the magnet is not perfectly centred above the MT6701, the measured angle will have a small periodic error that shows up as torque ripple. The [SimpleFOCDrivers](drivers_library) library includes a `CalibratedSensor` that measures this error once and compensates for it with a lookup table:

```cpp
#include "encoders/calibrated/CalibratedSensor.h"

CalibratedSensor sensor_calibrated = CalibratedSensor(sensor);

void setup() {
  // ... sensor, driver and motor.init() as before
  sensor_calibrated.voltage_calibration = 2.0f; // voltage applied during calibration
  sensor_calibrated.calibrate(motor);
  motor.linkSensor(&sensor_calibrated); // use the calibrated sensor from now on
  motor.initFOC();
}
```

[CalibratedSensor documentation](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/calibrated){: .btn .btn-docs}

### CAN and the reference firmware

To control the board over CAN as well, add the code from the [CAN communication](microspora_can) page.

The [Microspora firmware repository](https://github.com/simplefoc/microspora_simplefoc_firmware) contains the reference firmware with CAN, motor characterisation and sensor calibration.

[Get the reference firmware on GitHub](https://github.com/simplefoc/microspora_simplefoc_firmware){: .btn .btn-primary}
