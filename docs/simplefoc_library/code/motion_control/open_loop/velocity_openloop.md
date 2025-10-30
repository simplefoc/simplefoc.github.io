---
layout: default
title: Velocity Open-Loop
parent: Motion Control
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /velocity_openloop
nav_order: 1
parent: Open-Loop control
grand_parent: Motion Control
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---


# Velocity open-loop control 
This control loop allows you to spin your BLDC motor with desired velocity without using position sensor. This mode is enabled by:
```cpp
// set velocity control open-loop mode
motor.controller = MotionControlType::velocity_openloop;
```

Choose the motor type: 

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

Choose the voltage control type: 

<a href ="javascript:show(0,'loop');" id="btn-0" class="btn btn-loop btn-primary">Voltage limiting</a>
<a href ="javascript:show(1,'loop');" id="btn-1" class="btn btn-loop">Current limiting</a>
<a href ="javascript:show(2,'loop');" id="btn-2" class="btn btn-loop">Current limiting with Back-EMF compensation</a>

<div class="type type-b">
<img class="loop loop-0 width60" src="extras/Images/open_loop_velocity (3).png"/>
<img class="loop loop-1 width60 hide" src="extras/Images/open_loop_velocity (2).png"/>
<img class="loop loop-2 width60 hide" src="extras/Images/open_loop_velocity (1).png"/>

</div>
<div class="type type-s hide">

<img  class="loop width60 loop-0" src="extras/Images/open_loop_vel_steppe3.jpg"/>
<img class="loop width60 loop-1 hide" src="extras/Images/open_loop_vel_steppe2.jpg"/>
<img class="loop width60 loop-2 hide" src="extras/Images/open_loop_vel_steppe1.jpg"/>

</div>

You can test this algorithm by running the examples in `motion_control/openloop_motor_control/` folder.

This control algorithm is very simple. User can set the target velocity it wants to achieve $$v_d$$, the algorithm is going to integrate it in time to find out what is the angle it needs to set to the motor $$a_c$$ in order to achieve it. Then the maximal allowed voltage `motor.voltage_limit` is going to be applied in the direction of the $$a_c$$ using `SinePWM` or `SpaceVectorPWM` modulation.

This is the simplified version of the calculation of the next angle to set to the motor: 

$$
a_c = a_c + v_d\Delta t;
$$

You need to know the target_velocity $$v_d$$, sample time $$\Delta t$$ and past value of the angle $$a$$ you have set to the motor.

## Configuration
There are only three main parameters of the velocity open-loop control
```cpp
// choose FOC modulation (optional) - SinePWM or SpaceVectorPWM
motor.foc_modulation = FOCModulationType::SinePWM;

// limiting voltage 
motor.voltage_limit = 3;   // Volts
// or current  - if phase resistance provided
motor.current_limit = 0.5 // Amps
```

The velocity open-loop control will (if not provided phase resistance) set the voltage to the motor equal to the `motor.voltage_limit` ($$U_{limit}$$)

$$
U_{limit}  \quad [Volts]
$$

This is very inefficient, as for different motors with different phase resistances the same voltage values can produce wildly different currents.
For gimbal motor, you can run it in the open loop with the voltage limits of 5-10 Volts and it will reach the currents of 0.5-2 amps as it has the phase resistance from 5-15 Ohms. For drone motors, the voltage limits should stay very low, under 1 volt. Because they have phase resistance of 0.05 to 0.2 Ohms.

### Current limiting approaches

We suggest you to provide the motor class with the `phase_resistance` $$R$$ value and set the `motor.current_limit` $$I_{limit}$$ instead the voltage limit. This current might be surpassed but at least you will know an approximate current your motor is drawing. You can calculate the current the motor is going to be producing by checking the motor resistance `phase_resistance` and evaluating:

$$
U_{limit} = I_{limit}\cdot R  \quad  [ Amps ]
$$

The best way to use this control strategy would be to provide both phase resistance value and KV rating of your motor. The the library would be able to calculate he back-EMF voltage and much more precisely estimate the consumed current. And with the current and the back-EMF current the library can set much more appropriate voltage to the motor.

$$
U_{limit} = I_{limit}\cdot R + \frac{v_d}{KV}  \quad  [ Amps ]
$$

### Changing limits in real-time

Also, you can change the voltage/current limit in real-time if you need this kind of behavior in your application.

## Velocity open-loop control example

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

Here is one basic example of the velocity open-loop control with the complete configuration. The program will set the target velocity of `2 RAD/s` and maintain it, and the user can change the target velocity using serial terminal.

<div class="type type-b" markdown="1">

```cpp
// Open loop motor control example
#include <SimpleFOC.h>

// BLDC motor & driver instance
// BLDCMotor( pp number , phase resistance, KV rating)
BLDCMotor motor = BLDCMotor(11 , 12.5, 100); 
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimitCurrent(char* cmd) { command.scalar(&motor.current_limit, cmd); }

void setup() {

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor current (provided resistance)
  motor.current_limit = 0.5;   // [Amps]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  motor.init();
  motor.initFOC();

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('C', doLimitCurrent, "current limit");

  Serial.begin(115200);
  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {
  motor.loopFOC();
  // open loop velocity movement
  // using motor.current_limit and motor.velocity_limit
  motor.move();

  // user communication
  command.run();
}

```

</div>

<div class="type type-s hide" markdown="1">

```cpp
// Open loop motor control example
#include <SimpleFOC.h>

// Stepper motor & driver instance
// StepperMotor( pp number , phase resistance)
StepperMotor motor = StepperMotor(50 , 1.5); 
StepperDriver2PWM driver = StepperDriver2PWM(9, 5, 6, 8);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimitCurrent(char* cmd) { command.scalar(&motor.current_limit, cmd); }

void setup() {

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor current (provided resistance)
  motor.current_limit = 0.5;   // [Amps]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  motor.init();
  motor.initFOC();

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('C', doLimitCurrent, "current limit");

  Serial.begin(115200);
  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {
  motor.loopFOC();
  // open loop velocity movement
  // using motor.current_limit and motor.velocity_limit
  motor.move();

  // user communication
  command.run();
}

```

</div>