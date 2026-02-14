---
layout: default
title: Custom control
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /custom_control
nav_order: 4
has_children: True
has_toc: False
parent: Motion Control
grand_parent: Writing the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Custom Motion Control 

<blockquote class="info"><p class="heading">New experimental feature (not recommended for beginners)</p>
This feature is still in its early stages and we are not 100% happy with the API of the implementation. We are open to suggestions and contributions, so if you have any ideas on how to improve it, please let us know.

</blockquote>

This section is for advanced users that want to implement their own custom motion control algorithms. 

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b width60" src="extras/Images/cus_b.png"/>
<img class="type type-s width60 hide" src="extras/Images/cus_s.png"/>

As all the other standard motion control approaches, the custom motion control approach is be executed in the motion control loop (`motor.move()`). This loop will calculate the target torque (current or voltage) to be applied to the motor using the underlying torque/FOC control loop (`motor.loopFOC()`) in order to achieve the desired motion. 

To use the custom motion control loop, you need to set the `motor.controller` variable to `MotionControlType::custom`. 
```cpp
// set FOC loop to be used
motor.controller = MotionControlType::custom;
```

You can find some examples in `examples/motion_control/custom_motion_control/` folder.

This `custom` motion control requires the user to provide their own implentation of the motion control:

```cpp
// custom motion control function
float custom_motion_control(FOCMotor& motor) {
  // your code here
  
  // ex. simple proportional position control
  float error = motor->target - motor->shaft_angle;
  float target_torque = 10*error; 

  // return the target torque to be applied to the motor
  return target_torque; 
}
```

And then link this callback function to the motor instance: 
```cpp
motor.linkCustomMotionControl(custom_motion_control);
```

## Target and limits

The custom motion control uses the `motor.target` variable as the desired user defied target entry, and the user can choose to respect or ignore the velocity limits `motor.velocity_limit`. The torque limits are enforeced by the underlying torque control loop, so they will be respected regardless if the 
user chooses to use them in their custom control or not. (If the user code returns a target torque that is higher than the limits set by the user, the library is going to limit it to the set limits before applying it to the motor).

The units of the target variable is completely user defined, so it can represent any physical quantity that the user wants to control. 

For example, if the user wants to implement a torque control in Newton-meters (Nm), the user can set the `motor.target` variable to represent the target torque in Nm. 

```cpp
... 
float Kt = 0.1; // torque constant in Nm/A
// custom motion control function
float customTorqueControl(FOCMotor& motor) {
  // convert the target from Nm to Amps using the torque constant
  float target_in_Amps = motor->target / Kt;
  // return the target torque to be applied to the motor
  return target_in_Amps;
}

void setup() {
  ...
  // link the custom control method to the motor
  motor.linkCustomMotionControl(customTorqueControl);
  // set the control loop type to be used
  motor.controller = MotionControlType::custom;
  ...
}
```

## What can I do with the custom motion control?

- Implement any motion control algorithm you want, for example:
  - Position control with custom trajectory generation
  - Velocity control with custom acceleration profiles
  - Advanced control algorithms like model predictive control, sliding mode control, etc.
- Implement control schemes with multiple sensors, 
  - using different sensors for position and velocity feedback, 
  - using external sensors like IMUs
  - using secondary sensors after gearing
    etc.

## Custom control code example

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>


Here is one basic example implementing custom motion control with the voltage mode torque control. The custom motion control is a simple proportional position control, where the target torque is proportional to the error between the target angle and the current angle. The user can change the PID parameters in runtime using the commander interface. As well as the target angle or change the motion control strategy to the standard ones.

<div class="type type-b" markdown="1">

```cpp
#include <Arduino.h>
#include <SimpleFOC.h>

// BLDC motor & driver instance
BLDCMotor motor = BLDCMotor(7);
BLDCDriver3PWM driver = BLDCDriver3PWM(D6, D10, D5, D8);

// encoder instance
MagneticSensorSPI sensor = MagneticSensorSPI(AS5048_SPI, D4);

// commander communication instance
Commander command = Commander(Serial);
// void doMotion(char* cmd){ command.motion(&motor, cmd); }
void doMotor(char* cmd){ command.motor(&motor, cmd); }


// custom PID controller instance for the custom control method
// P controller with gain of 1.0f, no integral or derivative gain
PIDController custom_PID = PIDController(1.0f, 0, 0);
// custom motion control method
float positionPControl(FOCMotor* motor){
  // simple proportional position control
  float error = motor->target - motor->shaft_angle;
  // set the PID output limit to the motor voltage limit
  custom_PID.limit = motor->voltage_limit; 
  return custom_PID(error); // return current command based on the error
}

// optional add the PID to command to be able to tune it in runtime
void doPID(char* cmd){ command.pid(&custom_PID, cmd); }

void setup() {
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // initialize sensor hardware
  sensor.init();
  motor.linkSensor(&sensor);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 20;
  driver.init();
  // link driver
  motor.linkDriver(&driver);

  // set the custom control method
  motor.linkCustomMotionControl(positionPControl); 
  // set control loop type to be used
  motor.controller = MotionControlType::custom;
  // set the torque control type to voltage control 
  motor.torque_controller = TorqueControlType::voltage; 

  // comment out if not needed
  motor.useMonitoring(Serial);
  motor.monitor_downsample = 0; // disable intially
  motor.monitor_variables = _MON_TARGET | _MON_VEL | _MON_ANGLE; // monitor target velocity and angle

  // subscribe motor to the commander
  //command.add('T', doMotion, "motion control"); // a bit less resouce intensive
  command.add('M', doMotor, "motor");
  command.add('C', doPID, "custom PID");

  // initialise motor
  motor.init();
  // align encoder and start FOC
  motor.initFOC();

  _delay(1000);
}

void loop() {
  // iterative setting FOC phase voltage
  motor.loopFOC();

  // iterative function setting the outter loop target
  motor.move();

  // // motor monitoring
  motor.monitor();

  // user communication
  command.run();
}
```

</div>

<div class="type type-s hide" markdown="1">

```cpp
#include <Arduino.h>
#include <SimpleFOC.h>

// Stepper motor & driver instance
HybridStepperMotor motor = HybridStepperMotor(50);
BLDCDriver3PWM driver = BLDCDriver3PWM(D6, D10, D5, D8);

// encoder instance
MagneticSensorSPI sensor = MagneticSensorSPI(AS5048_SPI, D4);

// commander communication instance
Commander command = Commander(Serial);
// void doMotion(char* cmd){ command.motion(&motor, cmd); }
void doMotor(char* cmd){ command.motor(&motor, cmd); }


// custom PID controller instance for the custom control method
// P controller with gain of 1.0f, no integral or derivative gain
PIDController custom_PID = PIDController(1.0f, 0, 0);
// custom motion control method
float positionPControl(FOCMotor* motor){
  // simple proportional position control
  float error = motor->target - motor->shaft_angle;
  // set the PID output limit to the motor voltage limit
  custom_PID.limit = motor->voltage_limit; 
  return custom_PID(error); // return current command based on the error
}

// optional add the PID to command to be able to tune it in runtime
void doPID(char* cmd){ command.pid(&custom_PID, cmd); }

void setup() {
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // initialize sensor hardware
  sensor.init();
  motor.linkSensor(&sensor);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 20;
  driver.init();
  // link driver
  motor.linkDriver(&driver);

  // set the custom control method
  motor.linkCustomMotionControl(positionPControl); 
  // set control loop type to be used
  motor.controller = MotionControlType::custom;
  // set the torque control type to voltage control 
  motor.torque_controller = TorqueControlType::voltage; 

  // comment out if not needed
  motor.useMonitoring(Serial);
  motor.monitor_downsample = 0; // disable intially
  motor.monitor_variables = _MON_TARGET | _MON_VEL | _MON_ANGLE; // monitor target velocity and angle

  // subscribe motor to the commander
  //command.add('T', doMotion, "motion control"); // a bit less resouce intensive
  command.add('M', doMotor, "motor");
  command.add('C', doPID, "custom PID");

  // initialise motor
  motor.init();
  // align encoder and start FOC
  motor.initFOC();

  _delay(1000);
}

void loop() {
  // iterative setting FOC phase voltage
  motor.loopFOC();

  // iterative function setting the outter loop target
  motor.move();

  // // motor monitoring
  motor.monitor();

  // user communication
  command.run();
}
```
</div>