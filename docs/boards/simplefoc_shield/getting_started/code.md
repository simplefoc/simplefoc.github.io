---
layout: default
title: Writing the code
parent: Starting with Shield
description: "Writing the code for your SimpleFOCShield step by step: open loop, sensor, closed loop, current and voltage sensing, and the web controller."
nav_order: 3
permalink: /foc_shield_code
grand_parent: <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
toc: true
---

# Writing the code

This guide takes you from a fresh <span class="simple">Simple<span class="foc">FOC</span>Shield</span> to a motor running with FOC current control, one step at a time. Each step adds a piece of code and checks that it works before moving on, so if something goes wrong you know exactly where.

The guide is written for the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> **v4**. The code is the same for v3, except for the current sensing; the differences are shown in boxes like this one:

<blockquote class="info" markdown="1"><p class="heading">v3</p>
Differences for the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v3 are shown in these boxes.
</blockquote>

## Before you start

- The [solder pads](pads_soldering) are configured: PWM, enable, sensor and current sense pins
- VDD is set to match your microcontroller: 5V for Arduino UNO, 3.3V for STM32 Nucleo and ESP32
- The motor, power supply and sensor are [connected](foc_shield_connect_hardware)
- The <span class="simple">Simple<span class="foc">FOC</span>library</span> is [installed](installation)

The code is written for a BLDC motor; using a stepper motor needs only a few changes, see [using a stepper motor](#using-a-stepper-motor).

The code in this guide uses the [suggested single-board pinout](pads_soldering_v4#single-board). If you soldered different pads, change the pin numbers in the code:

<div class="tight_table" markdown="1">

Signal | PWM A | PWM B | PWM C | Enable | Enc. A / Hall U | Enc. B / Hall V | Enc. I / Hall W | Current 1 | Current 2 | Voltage
--- | - | - | - | - | - | - | - | - | - | -
Pin | `6` | `10` | `5` | `8` | `3` | `2` | `4` | `A0` | `A2` | `A3`

</div>

<blockquote class="info" markdown="1"><p class="heading">Arduino UNO or a bigger board?</p>
The Arduino UNO can run steps 1 to 4: open loop, sensor and closed-loop voltage control. FOC current control (step 5) and the full web controller interface (step 7) don't fit in its 32kB of flash, so for those use a board such as an STM32 Nucleo-64 or an ESP32. The [full code](#full-code) section has a version for each.
</blockquote>

## Choose your sensor

The Shield supports encoders, SPI and I2C magnetic sensors, and Hall sensors. Pick yours, and all the code on this page will follow your choice:

<a href="javascript:show('enc','sns');" class="btn btn-sns btn-enc btn-primary">Encoder</a>
<a href="javascript:show('spi','sns');" class="btn btn-sns btn-spi">SPI magnetic sensor</a>
<a href="javascript:show('i2c','sns');" class="btn btn-sns btn-i2c">I2C magnetic sensor</a>
<a href="javascript:show('hall','sns');" class="btn btn-sns btn-hall">Hall sensors</a>

<div class="sns sns-enc" markdown="1">

The encoder is connected to the `P_ENC` header: channels A and B on pins `3` and `2`, the interrupt pins of the Arduino UNO. Set the pulses per revolution (PPR) of your encoder, 2048 here.

```cpp
// encoder on P_ENC: A, B, pulses per revolution
Encoder sensor = Encoder(3, 2, 2048);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }
```

</div>
<div class="sns sns-spi hide" markdown="1">

The sensor is connected to the 6-pin SPI JST connector, with chip select on pin `4`. Change `AS5047_SPI` to your sensor (`AS5048_SPI`, `AS5147_SPI`, `MA730_SPI`, ...), see [magnetic sensors](magnetic_sensor_spi).

```cpp
// SPI magnetic sensor on the JST connector: chip select on pin 4
MagneticSensorSPI sensor = MagneticSensorSPI(AS5047_SPI, 4);
```

</div>
<div class="sns sns-i2c hide" markdown="1">

The sensor is connected to the QWIIC/STEMMA QT connector. Change `AS5600_I2C` to your sensor (`AS5048_I2C`, `MT6701_I2C`, ...), see [magnetic sensors](magnetic_sensor_i2c). Many STM32 boards need the [I2C pull-ups](pads_soldering_v4#i2c-pull-up-resistors) enabled.

```cpp
// I2C magnetic sensor on the QWIIC/STEMMA connector
MagneticSensorI2C sensor = MagneticSensorI2C(AS5600_I2C);
```

</div>
<div class="sns sns-hall hide" markdown="1">

The Hall sensors are connected to the `P_ENC` header: U, V and W on pins `3`, `2` and `4`. Set the pole pairs of your motor, 7 here. Most Hall sensors need the [pull-ups](pads_soldering_v4#encoderhall-sensor-pull-up-resistors) enabled. On the Arduino UNO, pin `4` has no hardware interrupt, see the `hall_sensor_software_interrupts_example` in the library examples.

```cpp
// Hall sensors on P_ENC: U, V, W, pole pairs
HallSensor sensor = HallSensor(3, 2, 4, 7);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }
void doC() { sensor.handleC(); }
```

</div>

## Step 1. Test the driver in open loop

First check that the driver and the motor work, without the sensor. This code spins the motor in open loop at one turn per second:

```cpp
#include <SimpleFOC.h>

BLDCMotor motor = BLDCMotor(7);                      // pole pairs
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8); // PWM A, B, C, enable

Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  driver.voltage_power_supply = 12; // your supply voltage
  driver.init();
  motor.linkDriver(&driver);

  motor.voltage_limit = 2; // keep it low: open loop has no current control
  motor.controller = MotionControlType::velocity_openloop;
  motor.init();

  motor.target = 6.28; // rad/s, one turn per second
  command.add('T', doTarget, "target velocity");
}

void loop() {
  motor.loopFOC();
  motor.move();
  command.run();
}
```

Upload it and open the serial monitor at 115200 baud. The motor should spin smoothly. Send `T-6.28` to reverse, or `T0` to stop.

- **The motor doesn't move**: check the enable pin. The driver stays off until the enable pin is high, so it must be soldered and match the code.
- **The motor vibrates but doesn't turn**: one phase is not connected, or a PWM pin in the code doesn't match the solder pads.
- **The motor gets hot**: lower `motor.voltage_limit`. In open loop the motor draws current all the time, even without load. Stepper motors have a low resistance, so start with a `voltage_limit` of about 1V for them.

[Read more about the open-loop test](test_motor_driver){: .btn .btn-docs}

## Step 2. Test the sensor

Next check that the sensor reads the position correctly:

<a href="javascript:show('enc','sns');" class="btn btn-sns btn-enc btn-primary">Encoder</a>
<a href="javascript:show('spi','sns');" class="btn btn-sns btn-spi">SPI magnetic sensor</a>
<a href="javascript:show('i2c','sns');" class="btn btn-sns btn-i2c">I2C magnetic sensor</a>
<a href="javascript:show('hall','sns');" class="btn btn-sns btn-hall">Hall sensors</a>

<div class="sns sns-enc" markdown="1">

```cpp
#include <SimpleFOC.h>

// encoder on P_ENC: A, B, pulses per revolution
Encoder sensor = Encoder(3, 2, 2048);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }

void setup() {
  Serial.begin(115200);
  sensor.init();
  sensor.enableInterrupts(doA, doB);
  Serial.println("Sensor ready");
}

void loop() {
  sensor.update();
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
  delay(10);
}
```

</div>
<div class="sns sns-spi hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// SPI magnetic sensor on the JST connector: chip select on pin 4
MagneticSensorSPI sensor = MagneticSensorSPI(AS5047_SPI, 4);

void setup() {
  Serial.begin(115200);
  sensor.init();
  Serial.println("Sensor ready");
}

void loop() {
  sensor.update();
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
  delay(10);
}
```

</div>
<div class="sns sns-i2c hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// I2C magnetic sensor on the QWIIC/STEMMA connector
MagneticSensorI2C sensor = MagneticSensorI2C(AS5600_I2C);

void setup() {
  Serial.begin(115200);
  sensor.init();
  Serial.println("Sensor ready");
}

void loop() {
  sensor.update();
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
  delay(10);
}
```

</div>
<div class="sns sns-hall hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// Hall sensors on P_ENC: U, V, W, pole pairs
HallSensor sensor = HallSensor(3, 2, 4, 7);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }
void doC() { sensor.handleC(); }

void setup() {
  Serial.begin(115200);
  sensor.init();
  sensor.enableInterrupts(doA, doB, doC);
  Serial.println("Sensor ready");
}

void loop() {
  sensor.update();
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
  delay(10);
}
```

</div>

Turn the motor by hand. The angle (in radians) should change smoothly, by 6.28 per full turn, and the velocity should be close to zero at rest.

- **The angle doesn't change**: wrong pins or wrong sensor type in the code, or the sensor is not powered (check VDD).
- **The angle jumps or is noisy**: for encoders and Hall sensors, enable the [pull-ups](pads_soldering_v4#encoderhall-sensor-pull-up-resistors); for magnetic sensors, check the magnet distance and centring.
- **The angle changes less than 6.28 per turn**: wrong PPR for the encoder, or wrong pole pairs for the Hall sensors.

[Read more about testing the sensor](test_sensor){: .btn .btn-docs}

## Step 3. Close the loop

Now put the two together. Link the sensor to the motor, switch to torque control with voltage, and call `initFOC()` to align the sensor with the motor:

```cpp
// in setup(), after the sensor and driver init
motor.linkSensor(&sensor);

motor.torque_controller = TorqueControlType::voltage;
motor.controller = MotionControlType::torque;
motor.voltage_limit = 6; // Volts

motor.init();
motor.initFOC(); // aligns the sensor, the motor moves a bit

// in loop()
motor.loopFOC();
motor.move();
```

During `initFOC()` the motor moves slightly in both directions, and the debug output on the serial monitor shows the result of the alignment. Then set a target voltage, e.g. `T2`: the motor should accelerate smoothly and reach a speed that depends on the voltage. A negative target reverses the direction.

- **`initFOC()` fails** with a "pole pairs" or "no movement" message: wrong pole pairs, or the sensor doesn't work (go back to step 2).
- **The motor spins well in one direction but not the other, or vibrates**: try a lower `motor.voltage_limit` for the alignment, and check that the sensor is fixed firmly to the motor.

Once this works, you can try velocity or angle control by changing `motor.controller`, see [motion control](motion_control).

[Read more about closing the loop](test_closedloop){: .btn .btn-docs}

## Step 4. Current sensing and FOC current control

With current sensing, the motor's torque is controlled directly as a current, which makes the control much more precise and protects the motor and the driver. The v4 has inline current sensing with INA240A2 amplifiers (gain 50) and 5mΩ shunt resistors:

```cpp
// shunt resistor [Ω], amplifier gain, current sense pins (phase A, B, C)
InlineCurrentSense current_sense = InlineCurrentSense(0.005f, 50.0f, A2, _NC, A0);

// in setup(), after driver.init() and before motor.init()
current_sense.linkDriver(&driver);
current_sense.init();
motor.linkCurrentSense(&current_sense);

motor.torque_controller = TorqueControlType::foc_current;
motor.current_limit = 2; // Amps
```

The target is now in Amps: `T0.5` applies 0.5A. During `initFOC()`, the library also checks which current sense pin belongs to which phase and the sign of the measurement, and corrects them automatically if needed.

<blockquote class="info" markdown="1"><p class="heading">v3</p>
The v3 has ACS712 Hall current sensors with 185mV/A: `InlineCurrentSense current_sense = InlineCurrentSense(185.0f, A0, A2);`
</blockquote>

<blockquote class="info" markdown="1"><p class="heading">Tip: use the low-side current sensing class if your MCU supports it</p>
The Shield has inline current sensing, but its amplifiers measure the current all the time, so it can also be read with the `LowsideCurrentSense` class. Just change the class name, the arguments stay the same:

```cpp
LowsideCurrentSense current_sense = LowsideCurrentSense(0.005f, 50.0f, A2, _NC, A0);
```

`LowsideCurrentSense` samples the currents in hardware, synchronised with the PWM, instead of reading them with `analogRead()` in every `loopFOC()` call. This makes the FOC loop much faster and the measurements less noisy. It is supported on STM32 (F1, F4, G4, L4, F7, H7), ESP32 with MCPWM and SAMD21, but not on the Arduino UNO, see the [supported MCUs](low_side_current_sense).
</blockquote>

### Tune the current controller automatically

The current controller needs PI gains that match the motor. The library can measure the motor resistance and inductance and calculate them for you. Add this after `motor.initFOC()`:

```cpp
motor.tuneCurrentController(300.0f); // current loop bandwidth in Hz
```

If the motor resistance and inductance are not set, it first measures them with `characteriseMotor()`. The gains are printed to the serial monitor; write them down and set them in your code (`motor.PID_current_q.P`, `motor.PID_current_q.I`, and the same for `PID_current_d`) so you don't need to measure on every startup. Keep the bandwidth well below the rate at which `loopFOC()` runs, about 5-10% of it. For a fixed, known loop rate, run the FOC loop from a timer, see the [real-time loop guide](real_time_loop).

[Read more about current control](foc_current){: .btn .btn-docs} [Tuning the current loop](tuning_current_loop){: .btn .btn-docs}

## Step 5. Power supply voltage sensing <small>(v4 only)</small>

If you soldered the [voltage sensing pad](pads_soldering_v4#power-supply-voltage-sensing), the board measures the supply voltage divided by 11. Use it to set the driver's supply voltage automatically, so the voltages the library applies are correct even when the supply changes, e.g. with a battery:

```cpp
// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  // ADC reference voltage / ADC resolution * divider ratio
  return analogRead(A3) * 3.3f / 1023.0f * 11.0f;
}

// in setup(), before driver.init()
float vbus = readSupplyVoltage();
// fall back to 12V if the voltage sensing pad is not soldered
driver.voltage_power_supply = vbus > 6 ? vbus : 12;
```

Use `5.0f` instead of `3.3f` on 5V boards such as the Arduino UNO. Most Arduino cores return 10-bit values (0-1023) by default; if you call `analogReadResolution(12)`, use 4095.

You can also read the voltage in `loop()`, for example to stop the motor when a battery runs low. Don't read it on every loop, as `analogRead()` is slow. With `InlineCurrentSense`, `analogRead()` is fine. If you use `LowsideCurrentSense` (see the tip in step 4), `analogRead()` can interfere with the current measurements on some MCUs, see [regular ADC reads during FOC](regular_adc_read).

## Step 6. Control the motor from your computer

Add the [Commander interface](commander_interface) with the full motor commands, and enable monitoring. Then you can change the target, the control mode and the PID gains at runtime, and the code works directly with the [<span class="simple">Simple<span class="foc">FOC</span></span> web controller](webcontroller):

```cpp
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

// in setup()
motor.useMonitoring(Serial);
motor.monitor_start_char = 'M'; // must match the commander ID for the web controller
motor.monitor_end_char = 'M';
motor.monitor_downsample = 0;   // disabled at start, enable from the web controller
command.add('M', doMotor, "motor");

// in loop()
motor.monitor();
command.run();
```

Open the [web controller](webcontroller), connect to the serial port at 115200 baud, and you can plot the angle, velocity and currents, and tune the motor with sliders. In the serial monitor, the commands start with `M`: `M0.5` sets the target, `MC1` switches to velocity control, `MVP0.2` sets the velocity P gain, etc.

<blockquote class="info" markdown="1">
On the Arduino UNO, the full motor commands don't fit in the flash. Use a simple target command instead, as in step 1: `command.scalar(&motor.target, cmd)`.
</blockquote>

## Full code

Here is everything put together. Choose your sensor:

### STM32 Nucleo, ESP32 and other larger boards

FOC current control, voltage sensing, and the full Commander interface for the web controller. Set VDD to 3.3V.

<a href="javascript:show('enc','sns');" class="btn btn-sns btn-enc btn-primary">Encoder</a>
<a href="javascript:show('spi','sns');" class="btn btn-sns btn-spi">SPI magnetic sensor</a>
<a href="javascript:show('i2c','sns');" class="btn btn-sns btn-i2c">I2C magnetic sensor</a>
<a href="javascript:show('hall','sns');" class="btn btn-sns btn-hall">Hall sensors</a>

<div class="sns sns-enc" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor: pole pairs
BLDCMotor motor = BLDCMotor(7);
// driver: PWM A, B, C, enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);
// current sense: 5mΩ shunts, INA240 gain 50
InlineCurrentSense current_sense = InlineCurrentSense(0.005f, 50.0f, A2, _NC, A0);

// encoder on P_ENC: A, B, pulses per revolution
Encoder sensor = Encoder(3, 2, 2048);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 3.3f / 1023.0f * 11.0f;
}

// commander interface, compatible with the web controller
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  sensor.enableInterrupts(doA, doB);
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // current sensing
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  // FOC current torque control
  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.current_limit = 2;   // Amps

  // monitoring for the web controller
  motor.useMonitoring(Serial);
  motor.monitor_start_char = 'M';
  motor.monitor_end_char = 'M';
  motor.monitor_downsample = 0; // disabled at start, enable from the web controller

  motor.init();
  motor.initFOC();

  command.add('M', doMotor, "motor");
  Serial.println(F("Motor ready. Set the target with M<value>, e.g. M0.5"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>
<div class="sns sns-spi hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor: pole pairs
BLDCMotor motor = BLDCMotor(7);
// driver: PWM A, B, C, enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);
// current sense: 5mΩ shunts, INA240 gain 50
InlineCurrentSense current_sense = InlineCurrentSense(0.005f, 50.0f, A2, _NC, A0);

// SPI magnetic sensor on the JST connector: chip select on pin 4
MagneticSensorSPI sensor = MagneticSensorSPI(AS5047_SPI, 4);

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 3.3f / 1023.0f * 11.0f;
}

// commander interface, compatible with the web controller
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // current sensing
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  // FOC current torque control
  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.current_limit = 2;   // Amps

  // monitoring for the web controller
  motor.useMonitoring(Serial);
  motor.monitor_start_char = 'M';
  motor.monitor_end_char = 'M';
  motor.monitor_downsample = 0; // disabled at start, enable from the web controller

  motor.init();
  motor.initFOC();

  command.add('M', doMotor, "motor");
  Serial.println(F("Motor ready. Set the target with M<value>, e.g. M0.5"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>
<div class="sns sns-i2c hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor: pole pairs
BLDCMotor motor = BLDCMotor(7);
// driver: PWM A, B, C, enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);
// current sense: 5mΩ shunts, INA240 gain 50
InlineCurrentSense current_sense = InlineCurrentSense(0.005f, 50.0f, A2, _NC, A0);

// I2C magnetic sensor on the QWIIC/STEMMA connector
MagneticSensorI2C sensor = MagneticSensorI2C(AS5600_I2C);

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 3.3f / 1023.0f * 11.0f;
}

// commander interface, compatible with the web controller
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // current sensing
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  // FOC current torque control
  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.current_limit = 2;   // Amps

  // monitoring for the web controller
  motor.useMonitoring(Serial);
  motor.monitor_start_char = 'M';
  motor.monitor_end_char = 'M';
  motor.monitor_downsample = 0; // disabled at start, enable from the web controller

  motor.init();
  motor.initFOC();

  command.add('M', doMotor, "motor");
  Serial.println(F("Motor ready. Set the target with M<value>, e.g. M0.5"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>
<div class="sns sns-hall hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor: pole pairs
BLDCMotor motor = BLDCMotor(7);
// driver: PWM A, B, C, enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);
// current sense: 5mΩ shunts, INA240 gain 50
InlineCurrentSense current_sense = InlineCurrentSense(0.005f, 50.0f, A2, _NC, A0);

// Hall sensors on P_ENC: U, V, W, pole pairs
HallSensor sensor = HallSensor(3, 2, 4, 7);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }
void doC() { sensor.handleC(); }

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 3.3f / 1023.0f * 11.0f;
}

// commander interface, compatible with the web controller
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  sensor.enableInterrupts(doA, doB, doC);
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // current sensing
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  // FOC current torque control
  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.current_limit = 2;   // Amps

  // monitoring for the web controller
  motor.useMonitoring(Serial);
  motor.monitor_start_char = 'M';
  motor.monitor_end_char = 'M';
  motor.monitor_downsample = 0; // disabled at start, enable from the web controller

  motor.init();
  motor.initFOC();

  command.add('M', doMotor, "motor");
  Serial.println(F("Motor ready. Set the target with M<value>, e.g. M0.5"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>

Send `M0.5` to apply 0.5A, or connect with the [web controller](webcontroller).

### Arduino UNO

Voltage control and a simple target command, to fit in the flash. Set VDD to 5V.

<a href="javascript:show('enc','sns');" class="btn btn-sns btn-enc btn-primary">Encoder</a>
<a href="javascript:show('spi','sns');" class="btn btn-sns btn-spi">SPI magnetic sensor</a>
<a href="javascript:show('i2c','sns');" class="btn btn-sns btn-i2c">I2C magnetic sensor</a>
<a href="javascript:show('hall','sns');" class="btn btn-sns btn-hall">Hall sensors</a>

<div class="sns sns-enc" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor: pole pairs
BLDCMotor motor = BLDCMotor(7);
// driver: PWM A, B, C, enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);

// encoder on P_ENC: A, B, pulses per revolution
Encoder sensor = Encoder(3, 2, 2048);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 5.0f / 1023.0f * 11.0f;
}

// commander: only the target, the full motor commands do not fit in the UNO flash
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  sensor.enableInterrupts(doA, doB);
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // voltage torque control (no current sensing on the UNO)
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;
  motor.voltage_limit = 6;   // Volts

  motor.init();
  motor.initFOC();

  command.add('T', doTarget, "target");
  Serial.println(F("Motor ready. Set the target with T<value>, e.g. T2"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  command.run();
}
```

</div>
<div class="sns sns-spi hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor: pole pairs
BLDCMotor motor = BLDCMotor(7);
// driver: PWM A, B, C, enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);

// SPI magnetic sensor on the JST connector: chip select on pin 4
MagneticSensorSPI sensor = MagneticSensorSPI(AS5047_SPI, 4);

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 5.0f / 1023.0f * 11.0f;
}

// commander: only the target, the full motor commands do not fit in the UNO flash
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // voltage torque control (no current sensing on the UNO)
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;
  motor.voltage_limit = 6;   // Volts

  motor.init();
  motor.initFOC();

  command.add('T', doTarget, "target");
  Serial.println(F("Motor ready. Set the target with T<value>, e.g. T2"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  command.run();
}
```

</div>
<div class="sns sns-i2c hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor: pole pairs
BLDCMotor motor = BLDCMotor(7);
// driver: PWM A, B, C, enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);

// I2C magnetic sensor on the QWIIC/STEMMA connector
MagneticSensorI2C sensor = MagneticSensorI2C(AS5600_I2C);

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 5.0f / 1023.0f * 11.0f;
}

// commander: only the target, the full motor commands do not fit in the UNO flash
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // voltage torque control (no current sensing on the UNO)
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;
  motor.voltage_limit = 6;   // Volts

  motor.init();
  motor.initFOC();

  command.add('T', doTarget, "target");
  Serial.println(F("Motor ready. Set the target with T<value>, e.g. T2"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  command.run();
}
```

</div>
<div class="sns sns-hall hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor: pole pairs
BLDCMotor motor = BLDCMotor(7);
// driver: PWM A, B, C, enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);

// Hall sensors on P_ENC: U, V, W, pole pairs
HallSensor sensor = HallSensor(3, 2, 4, 7);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }
void doC() { sensor.handleC(); }

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 5.0f / 1023.0f * 11.0f;
}

// commander: only the target, the full motor commands do not fit in the UNO flash
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  sensor.enableInterrupts(doA, doB, doC);
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // voltage torque control (no current sensing on the UNO)
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;
  motor.voltage_limit = 6;   // Volts

  motor.init();
  motor.initFOC();

  command.add('T', doTarget, "target");
  Serial.println(F("Motor ready. Set the target with T<value>, e.g. T2"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  command.run();
}
```

</div>

Send `T2` to apply 2V. To switch to velocity or angle control, change `motor.controller`, see [motion control](motion_control).

All these examples are compiled for an Arduino UNO and an STM32 Nucleo-F411RE.

## Using a stepper motor

The Shield can also drive a 2-phase stepper motor, such as a NEMA 17, using the `HybridStepperMotor` class. A stepper has two coils, A and B, and the Shield has three outputs: each coil is connected between its own output and a third, common output, and the library drives the common output so that both coils get the full voltage range.

### Wiring

Connect the stepper wires in the order `A+`, `A-`/`B-`, `B+`: one wire of each coil to the outer terminals, and the other wire of both coils together to the middle terminal:

Shield motor terminal | Stepper wires
--- | ---
`A` | `A+`
`B` | `A-` and `B-` together (common)
`C` | `B+`

The library uses the **third PWM pin** of the driver as the common phase. With the common on terminal B, give the driver the PWM pins in the order A, C, B:

```cpp
// PWM A, PWM C, PWM B (common), enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 5, 10, 8);
```

It doesn't matter which wire of a coil is `+` or `-`, as long as both coils share the common terminal. If you're not sure which wires belong to the same coil, measure with a multimeter: the two wires of a coil have a few Ω between them, and wires of different coils are not connected.

### Code changes

Only the motor class, the order of the driver pins and the current sense pins change, everything else in this guide stays the same:

```cpp
// pole pairs = steps per revolution / 4 (50 for a 200-step NEMA 17)
HybridStepperMotor motor = HybridStepperMotor(50);
// PWM A, PWM C, PWM B (common), enable
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 5, 10, 8);
// current sensing on coil A (A2) and coil B (A0), the common is not measured
InlineCurrentSense current_sense = InlineCurrentSense(0.005f, 50.0f, A2, A0);
```

Steppers have a much lower phase resistance than gimbal motors, typically 1-3Ω, so always limit the current. Set `motor.current_limit` to the rated current of the stepper with current sensing, or `motor.voltage_limit` to about the rated current times the phase resistance without it. The current sensing and `initFOC()` alignment work the same as for a BLDC motor.

### Full code: stepper motor

FOC current control with the full Commander interface, for STM32 Nucleo, ESP32 and other larger boards:

<a href="javascript:show('enc','sns');" class="btn btn-sns btn-enc btn-primary">Encoder</a>
<a href="javascript:show('spi','sns');" class="btn btn-sns btn-spi">SPI magnetic sensor</a>
<a href="javascript:show('i2c','sns');" class="btn btn-sns btn-i2c">I2C magnetic sensor</a>
<a href="javascript:show('hall','sns');" class="btn btn-sns btn-hall">Hall sensors</a>

<div class="sns sns-enc" markdown="1">

```cpp
#include <SimpleFOC.h>

// stepper motor: pole pairs = steps per revolution / 4 (50 for a 200-step NEMA 17)
HybridStepperMotor motor = HybridStepperMotor(50);
// driver: PWM A, PWM C, PWM B, enable - terminal B is the common wire of the stepper
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 5, 10, 8);
// current sense: 5mΩ shunts, INA240 gain 50, measuring coil A (A2) and coil B (A0)
InlineCurrentSense current_sense = InlineCurrentSense(0.005f, 50.0f, A2, A0);

// encoder on P_ENC: A, B, pulses per revolution
Encoder sensor = Encoder(3, 2, 2048);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 3.3f / 1023.0f * 11.0f;
}

// commander interface, compatible with the web controller
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  sensor.enableInterrupts(doA, doB);
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // current sensing
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  // FOC current torque control
  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.current_limit = 1;   // Amps, set to the rated current of your stepper

  // monitoring for the web controller
  motor.useMonitoring(Serial);
  motor.monitor_start_char = 'M';
  motor.monitor_end_char = 'M';
  motor.monitor_downsample = 0; // disabled at start, enable from the web controller

  motor.init();
  motor.initFOC();

  command.add('M', doMotor, "motor");
  Serial.println(F("Motor ready. Set the target with M<value>, e.g. M0.5"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>
<div class="sns sns-spi hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// stepper motor: pole pairs = steps per revolution / 4 (50 for a 200-step NEMA 17)
HybridStepperMotor motor = HybridStepperMotor(50);
// driver: PWM A, PWM C, PWM B, enable - terminal B is the common wire of the stepper
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 5, 10, 8);
// current sense: 5mΩ shunts, INA240 gain 50, measuring coil A (A2) and coil B (A0)
InlineCurrentSense current_sense = InlineCurrentSense(0.005f, 50.0f, A2, A0);

// SPI magnetic sensor on the JST connector: chip select on pin 4
MagneticSensorSPI sensor = MagneticSensorSPI(AS5047_SPI, 4);

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 3.3f / 1023.0f * 11.0f;
}

// commander interface, compatible with the web controller
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // current sensing
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  // FOC current torque control
  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.current_limit = 1;   // Amps, set to the rated current of your stepper

  // monitoring for the web controller
  motor.useMonitoring(Serial);
  motor.monitor_start_char = 'M';
  motor.monitor_end_char = 'M';
  motor.monitor_downsample = 0; // disabled at start, enable from the web controller

  motor.init();
  motor.initFOC();

  command.add('M', doMotor, "motor");
  Serial.println(F("Motor ready. Set the target with M<value>, e.g. M0.5"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>
<div class="sns sns-i2c hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// stepper motor: pole pairs = steps per revolution / 4 (50 for a 200-step NEMA 17)
HybridStepperMotor motor = HybridStepperMotor(50);
// driver: PWM A, PWM C, PWM B, enable - terminal B is the common wire of the stepper
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 5, 10, 8);
// current sense: 5mΩ shunts, INA240 gain 50, measuring coil A (A2) and coil B (A0)
InlineCurrentSense current_sense = InlineCurrentSense(0.005f, 50.0f, A2, A0);

// I2C magnetic sensor on the QWIIC/STEMMA connector
MagneticSensorI2C sensor = MagneticSensorI2C(AS5600_I2C);

// supply voltage sensing: VCC/11 on A3
float readSupplyVoltage() {
  return analogRead(A3) * 3.3f / 1023.0f * 11.0f;
}

// commander interface, compatible with the web controller
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // sensor
  sensor.init();
  motor.linkSensor(&sensor);

  // driver
  float vbus = readSupplyVoltage();
  // fall back to 12V if the voltage sensing pad is not soldered
  driver.voltage_power_supply = vbus > 6 ? vbus : 12;
  driver.init();
  motor.linkDriver(&driver);

  // current sensing
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  // FOC current torque control
  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.current_limit = 1;   // Amps, set to the rated current of your stepper

  // monitoring for the web controller
  motor.useMonitoring(Serial);
  motor.monitor_start_char = 'M';
  motor.monitor_end_char = 'M';
  motor.monitor_downsample = 0; // disabled at start, enable from the web controller

  motor.init();
  motor.initFOC();

  command.add('M', doMotor, "motor");
  Serial.println(F("Motor ready. Set the target with M<value>, e.g. M0.5"));
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>
<div class="sns sns-hall hide" markdown="1">

Stepper motors don't have Hall sensors. Use an encoder or a magnetic sensor.

</div>

For a complete stepper project with the Shield, see the [stepper control example](stepper_control_shield).

## Going further

### Two motors

Two stacked Shields run two motors from one microcontroller. Configure the solder pads of each board with different pins, see the [stacking pinouts](pads_soldering_v4#stacking-with-stm32-nucleo), and create two of everything:

```cpp
BLDCMotor motor1 = BLDCMotor(7);
BLDCDriver3PWM driver1 = BLDCDriver3PWM(6, 10, 5, 8);
InlineCurrentSense current_sense1 = InlineCurrentSense(0.005f, 50.0f, A2, _NC, A0);

BLDCMotor motor2 = BLDCMotor(7);
BLDCDriver3PWM driver2 = BLDCDriver3PWM(13, 9, 3, 7);
InlineCurrentSense current_sense2 = InlineCurrentSense(0.005f, 50.0f, A3, _NC, A1);

void loop() {
  motor1.loopFOC();
  motor2.loopFOC();
  motor1.move();
  motor2.move();
}
```

Two motors with current control need a fast microcontroller: both FOC loops share the processor, so each runs at half the rate. Running them from a timer keeps the rate fixed, see the [real-time loop guide](real_time_loop).

### More to explore

- [Sensorless control](drivers_sensorless): run the motor without a position sensor, using the current sensing
- [Settings storage](drivers_settings): save the sensor calibration and tuned gains, and skip the alignment on startup
- [Motion control](motion_control): velocity and angle control, and how to tune them
- [Getting started guide](example_from_scratch): the library's step-by-step guide, with more detail on each step
- [Example projects](examples) built with the <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
