---
layout: default
title: Position Open-Loop
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /angle_openloop
nav_order: 2
parent: Open-Loop control
grand_parent: Motion Control
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# Position open-loop control 
This control loop allows you to move your motor to the desired angle in real time without using the position sensor. This mode is enabled by:
```cpp
// set position motion control open-loop
motor.controller = MotionControlType::angle_openloop;
```
You can test this algorithm by running the examples in the `examples/motion_control/open_loop_motor_control/` folder.

## How it works?

Choose the motor type: 

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

Choose the torque control type: 

<a href ="javascript:show(0,'loop');" id="btn-0" class="btn btn-loop btn-primary">Voltage torque mode</a>
<a href ="javascript:show(1,'loop');" id="btn-1" class="btn btn-loop">Current torque mode</a>

<div class="type type-b">
<img class="loop loop-0 width50" src="extras/Images/olb_p_v.png"/>
<img class="loop loop-1 width50 hide" src="extras/Images/olb_p_i.png"/>

</div>
<div class="type type-s hide">

<img class="loop loop-0 width50" src="extras/Images/ols_p_v.png"/>
<img class="loop loop-1 width50 hide" src="extras/Images/ols_p_i.png"/>

</div>

This control algorithm is very simple. User sets the target angle it wants to achieve $$a_d$$. The algorithm only subtracts the current angle $$a_c$$ and the desired angle $$a_d$$ to find the direction it needs to move and goes in that direction with the highest velocity possible `motor.velocity_limit`(max velocity $$v_{max}$$). To set this velocity it uses the same algorithm as for [velocity open-loop control](velocity_openloop). 

In each step of the control loop, the algorithm calculates the distance from the target angle $$a_d$$ and the current angle $$a_c$$. It then calculates the next angle $$a_{c}$$ using the following formula:

$$
a_c = a_c + \max(-v_{max}d t,~ \min(v_{max}d t,~ a_d - a))
$$

<blockquote class="info">
<details markdown="1">
<summary style="cursor: pointer;"> <i class="fa fa-code"></i>A different way to write the same formula is: </summary>

A bit more verbose way to write the same formula is:

$$
a_c = a_c + \begin{cases}
     v_{max}d t, & \text{if } a_d - a_c > v_{max}d t \\
     -v_{max}d t,  & \text{if } a_d - a_c < -v_{max}d t \\
      a_d - a_c,       & \text{otherwise}
\end{cases}
$$

Or in code

```cpp
// calculate the distance from the target
d_angle = target_angle - past_angle;
// constrain the distance with maximal allowable displacement
d_angle = constrain(d_angle, -velocity_limit*d_time, velocity_limit*d_time)
// calculate the next angle
next_angle = past_angle + d_angle;
```

</details>
</blockquote>

The sample time  $$dt$$ of the algorithm is adaptively calculated upon every algorithm execution (`motor.move()` call) and it is equal to the time passed since the last execution of the algorithm (time between two `motor.move()` calls). This means that the algorithm is going to be very stable even if the `motor.move()` calls are not happening at a constant frequency. You can check sampling time of the algorithm by checking `motor.move_time.us` (representing the time between two `motor.move()` calls in microseconds) variable.

<blockquote class="info" markdown="1"> <p class="heading">Units</p>
The angles in the formula are in radians, the velocity limit is in radians per second and the time is in seconds. Make sure to use the correct units when setting the target angle and velocity limits.

[See the guide about units](library_units){: .btn .btn-docs}

</blockquote>

## Configuration and Limits

Open-loop position control uses the `motor.target` variable as the desired angle entry, while the `motor.velocity_limit` variable sets the maximum velocity to be used for the desired movemebnt. This mode also and uses torque limits based on your chosen [torque control mode](torque_control).

```cpp
// setting target velocity
motor.target = 2;   // [rad/s]

// maximal velocity of the position transition
motor.updateVelocityLimit(5);  // [rad/s]

// if in voltage control mode, you can set voltage limit
motor.updateVoltageLimit(6);  // [Volts]
// if in current control mode, you can set current limit instead of voltage limit
motor.updateCurrentLimit(0.5);  // [Amps]
```

## Efficiency Considerations

Open-loop motion is a simple but inefficient control strategy. However its efficiency and characteristics depend a lot on the [torque control mode](torque_control) you choose to use. 

**Which torque mode should I use**

Torque Mode |  Best For... | Requirements | Limits
--- | --- | --- | ---
Voltage Control | **Beginners & Gimbal motors.** Best for low-speed testing where you don't know the motor parameters yet. | **None** | Voltage `motor.voltage_limit` 
Estimated Current | **Drone & High-KV motors.** Much safer for low-resistance motors; provides back-EMF compensation. | ***Phase resistance*** ($$R$$) and ***KV rating*** ($$KV$$) | Current `motor.current_limit`
FOC Current | **High Performance.** Most efficient and robust mode; handles variable loads and speeds perfectly. | **Current Sensing** hardware. | Current `motor.current_limit`

[Read more about open-loop efficiency considerations](openloop_efficiency){: .btn .btn-docs}
[Read more about torque control modes](torque_control){: .btn .btn-docs}

## Position open-loop control example


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

Here is one basic example of the position open-loop control with the complete configuration. This code uses [estimated current mode](estimated_current_mode) for torque control. The program will set the target position of `0 RAD` and maintain it, and the user can change the target position using serial terminal.


<div class="type type-b" markdown="1">

```cpp
// Open loop motor control example
#include <SimpleFOC.h>

// BLDC motor & driver instance
// BLDCMotor(pole pairs, phase resistance, KV rating)
BLDCMotor motor = BLDCMotor(11, 7, 90.0);
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doCurrLimit(char* cmd) { command.scalar(&motor.current_limit, cmd); }
void doLimitVelocity(char* cmd) { command.scalar(&motor.velocity_limit, cmd); }

void setup() {

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link the motor and the driver
  motor.linkDriver(&driver);

  // open loop control config
  motor.controller = MotionControlType::angle_openloop;
  // torque control mode
  motor.torque_controller = TorqueControlType::estimated_current;

  // limiting motor movements
  motor.updateCurrentLimit(0.5);  // [Amps]
  motor.updateVelocityLimit(10); // [rad/s] cca 100rpm

  // init motor hardware
  motor.init();
  motor.initFOC();

  // add target command T
  command.add('T', doTarget, "target angle");
  command.add('L', doCurrLimit, "current limit");
  command.add('V', doLimitVelocity, "velocity limit");


  Serial.begin(115200);
  Serial.println("Motor ready!");
  Serial.println("Set target position [rad]");
  _delay(1000);
}

void loop() {
  // torque control loop
  motor.loopFOC(); 
  // open  loop angle movements
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
// StepperMotor(steps per revolution, phase resistance, KV rating)
StepperMotor motor = StepperMotor(50, 12.5, 100);
StepperDriver2PWM driver = StepperDriver2PWM(9, 5, 6, 8);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doCurrLimit(char* cmd) { command.scalar(&motor.current_limit, cmd); }
void doLimitVelocity(char* cmd) { command.scalar(&motor.velocity_limit, cmd); }

void setup() {

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link the motor and the driver
  motor.linkDriver(&driver);

  // open loop control config
  motor.controller = MotionControlType::angle_openloop;
  // torque control mode
  motor.torque_controller = TorqueControlType::estimated_current;

  // limiting motor movements
  motor.updateCurrentLimit(0.5);  // [Amps]
  motor.updateVelocityLimit(10); // [rad/s] cca 100rpm

  // init motor hardware
  motor.init();
  motor.initFOC();

  // add target command T
  command.add('T', doTarget, "target angle");
  command.add('L', doCurrLimit, "current limit");
  command.add('V', doLimitVelocity, "velocity limit");


  Serial.begin(115200);
  Serial.println("Motor ready!");
  Serial.println("Set target position [rad]");
  _delay(1000);
}

void loop() {
  // torque control loop
  motor.loopFOC(); 
  // open  loop angle movements
  motor.move();
  
  // user communication
  command.run();
}
```
</div>