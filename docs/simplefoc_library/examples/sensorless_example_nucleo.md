---
layout: default
title:  Sensorless FOC 
parent: Example projects
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 12
permalink: /sensorless_foc_nucleo_example
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---



# Sensorless FOC example<br>using <span class="simple">Simple<span class="foc">FOC</span>Shield</span> and Stm32 Nucleo-64
For this example of the Sensorless FOC control we are going to be using this hardware:

[Stm32 Nucleo-64](https://www.mouser.fr/ProductDetail/STMicroelectronics/NUCLEO-F446RE?qs=%2Fha2pyFaduj0LE%252BzmDN2WNd7nDNNMR7%2Fr%2FThuKnpWrd0IvwHkOHrpg%3D%3D) | [Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>](arduino_simplefoc_shield_showcase) |[GBM3506-120T](https://iflight-rc.eu/en-ch/products/ipower-gm3506-gimbal-motor-with-encoder)
--- | --- | --- 
<img src="extras/Images/nucleo.jpg" class="imgtable150"> |  <img src="extras/Images/shield_to_v13.jpg" class="imgtable150"> | <img src="extras/Images/gm3506.png" class="imgtable150"> 


This is just an example of how to use the <span class="simple">Simple<span class="foc">FOC</span>library</span> to control a motor without any sensors. You can use any other motor (BLDC, stepper or hybrid stepper) and any other driver or microcontroller. As long as the driver has enough current capacity to drive the motor and the microcontroller has enough processing power to run the FOC algorithm, you can use it. The only real constraint is that you need to be able to measure the phase current of the motor. This is because the sensorless observer uses the phase current to estimate the rotor position and velocity.


# Connecting everything together
For a bit more in depth explanation of how to connect Nucleo board and <span class="simple">Simple<span class="foc">FOC</span>Shield</span> connection please check the [connection examples](nucleo_connection).
<p><img src="extras/Images/sensorless_gimbal.jpg" class="width60"></p>

For more information about the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> check the [docs](arduino_simplefoc_shield_showcase).

## Motor
- Motor phases `a`, `b` and `c` are connected directly the motor terminal connector `TB_M1`


## SimpleFOCShield connections

<p><img src="extras/Images/shield_config_hybrid.jpg" class="img200"></p>

Signal | Pwm A | Pwm B | Pwm C | Enable
--- | - | - | - | - 
Pin | `6` | `10` | `5` | `8`


# Arduino code

First thing you need to do is include the `SimpleFOC` library:

```cpp
#include <SimpleFOC.h>
```
Make sure you have the library installed. If you still don't have it please check the [get started page](installation)

Then import the drivers library and the Sensorless observer:
```cpp
#include <SimpleFOCDrivers.h>
#include "encoders/MXLEMMING_observer/MXLEMMINGObserverSensor.h"
```

## Motor and driver
Next we need to define the BLDC motor and driver classes. The motor class is used to control the motor and the driver class is used to control the power electronics.

```cpp
// define BLDC motor
// - 11 is the number of pole pairs
// - 5.9 is the resistance of the motor [Ohms]
// - 100 is the KV rating of the motor in [RPM per Volt]
// - 0.0005 is the phase inductance [H]
// These parameters are specific to the motor you are using, please check the motor datasheet
// I'm using GBM3506-120T
BLDCMotor motor = BLDCMotor(11, 5.94, 100, 0.0005);

// define BLDC driver
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);
```

<blockquote class="warning" markdown="1">
<p class="heading">Note: Correct motor parameters are crucial! </p>
Having the correct motor parameters is crucial for the motor to work properly. 
</blockquote>

<blockquote class="info" markdown="1">
<p class="heading">KV rating</p>
Motor's KV rating is usually easy to find in the datasheets. If the datasheet does not specify the KV rating, they often specify some rated voltage and speed. In that case you can calculate the KV by dividing the motor's RPM by the voltage applied to it. You can also use the `examples/utils/calibration/find_kv_rating.ino` example to find it out. 
</blockquote>

<blockquote class="info" markdown="1">
<p class="heading">Phase resistance and inductance</p>
If you are not sure what your motor resistance and inductance are, you can use this code to measure them:

```cpp
#include <SimpleFOC.h>

// Stepper motor & BLDC driver instance
BLDCMotor motor = BLDCMotor(11);
// SimpleFOCShield
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);

// inline current sensor instance
// ACS712-05B has the resolution of 0.185mV per Amp
LowsideCurrentSense current_sense = LowsideCurrentSense(185.0f, A0, A2);


void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 20;
  driver.init();
  // link driver
  motor.linkDriver(&driver);
  // link current sense and the driver
  current_sense.linkDriver(&driver);

  // current sense init and linking
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  // initialize motor
  motor.init();

  // find the motor parameters
  motor.characteriseMotor(3.5f);


  _delay(1000);
}


void loop() {
  // do nothing
  _delay(1000);
}
```
The code output will look like this:

```
MOT: Init
MOT: Enable driver.
MOT: Measuring phase to phase resistance, keep motor still...
MOT: Estimated phase to phase resistance: 5.94
MOT: Measuring inductance, keep motor still...
MOT: Inductance measurement complete!
MOT: Measured D-inductance in mH: 0.50
MOT: Measured Q-inductance in mH: 0.59
```

So then you can use the measured values to define the motor:
```cpp
// define BLDC motor
// - 11 is the number of pole pairs
// - 5.94 is the resistance of the motor [Ohms]
// - 100 is the KV rating of the motor in [RPM per Volt]
// - 0.0005 is the phase inductance [H]
BLDCMotor motor = BLDCMotor(11, 5.94, 100, 0.0005);
``` 

</blockquote>


## Sensorless observer
Next we need to define the sensorless observer class. The observer is used to estimate the rotor position and velocity without using any sensors. The observer is based on the [MXLEMMING_observer](https://github.com/simplefoc/Arduino-FOC-drivers/tree/dev/src/encoders/MXLEMMING_observer)

```cpp
// define sensorless observer
MXLEMMINGObserverSensor observer = MXLEMMINGObserverSensor(motor);
```

And you're more or less set, the rest of the code is the same as usual. 

## Full Arduino code

The full code for the sensorless FOC example is as follows:

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>

#include "encoders/MXLEMMING_observer/MXLEMMINGObserverSensor.h"

// Stepper motor & BLDC driver instance
BLDCMotor motor = BLDCMotor(11, 5.94, 100, 0.0005);
// SimpleFOCShield
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);

// MXLEMMING observer sensor instance
MXLEMMINGObserverSensor observer = MXLEMMINGObserverSensor(motor);

// inline current sensor instance
// ACS712-05B has the resolution of 0.185mV per Amp
LowsideCurrentSense current_sense = LowsideCurrentSense(185.0f, A0, A2);

// commander communication instance
Commander command = Commander(Serial);
void doMotor(char* cmd){ command.motor(&motor, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // link the motor to the sensor
  motor.linkSensor(&observer);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 20;
  driver.init();
  // link driver
  motor.linkDriver(&driver);
  // link current sense and the driver
  current_sense.linkDriver(&driver);

  // set control loop type to be used
  motor.controller = MotionControlType::torque;
  motor.torque_controller = TorqueControlType::foc_current;

  // current sense init and linking
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  // initialize motor
  motor.init();
  // skip the sensor alignment
  motor.sensor_direction= Direction::CW;
  motor.zero_electric_angle = 0;
  motor.initFOC();


  // subscribe motor to the commander
  command.add('M', doMotor, "motor");
  
  // Run user commands to configure and the motor (find the full command list in docs.simplefoc.com)
  Serial.println("Motor ready.");

  _delay(1000);
}


void loop() {
  // iterative setting FOC phase voltage
  motor.loopFOC();

  // iterative function setting the outer loop target
  motor.move();

  // motor monitoring
  motor.monitor();

  // user communication
  command.run();
}
```


And you can interact with the motor using the commander. For example, you can set the target torque by sending the command `M0.5` to set the target torque to 0.5Amps. You can also use other commands to control the motor, like `M0` to stop the motor, `M1` to set the target torque to 1Amp, etc. You can find the full command list in the [docs](commander_interface).