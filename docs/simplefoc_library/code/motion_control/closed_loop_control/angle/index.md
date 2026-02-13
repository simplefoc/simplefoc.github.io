---
layout: default
title: Position Control 
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 3
permalink: /angle_loop
parent: Closed-Loop control
grand_parent: Motion Control
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
has_children: True
---


# Position control loop
This control loop allows you to move your motor to the desired angle in real-time. There are two different stategies for this control loop:
- **Standard cascade angle control** 
  - angle control loop is closing the loop around the velocity control loop, which is closing the loop around the torque control loop.
  - [Cascade angle control loop (Recommended)](angle_cascade_control){: .btn .mr-2 .btn-docs}
- **Non-cascade angle control** 
  - angle control loop is closing the loop directly around the torque control loop.
  - [Non-cascaded angle control loop](angle_nocascade_control){: .btn .mr-2 .btn-docs}

You can enable the angle/position control loop by setting the `controller` variable of the motor to either `MotionControlType::angle` or `MotionControlType::angle_nocascade` depending on which strategy you want to use.
```cpp
// set angle/position motion control loop
motor.controller = MotionControlType::angle; // standard cascade angle > velocity > torque control
// or
motor.controller = MotionControlType::angle_nocascade; // non-cascade angle control angle > torque control
```
You can test this algorithm by running the examples in `motion_control/position_motion_control/` folder.

## How it works

<a href ="javascript:show('y','cascade');"  class="btn btn-cascade btn-y btn-primary">Cascade Position Control</a>
<a href ="javascript:show('n','cascade');" class="btn btn-cascade btn-n"> No-cascade Position Control</a>

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>


<div class="cascade cascade-y"  markdown="1">
 <img class="width60 type type-b" src="extras/Images/a_b_i.drawio.png">
 <img class="width60 type type-s hide" src="extras/Images/a_s_i.drawio.png">

In **Cascade position control** the angle control loop is closing the loop around the velocity control loop, which is closing the loop around the torque control loop. This means that the angle controller will be setting the velocity target to the velocity controller, and then the velocity controller will be setting the torque target to the torque controller.


This angle control loop is therefore created by adding one more control loop in cascade on the [velocity control loop](velocity_loop) like showed on the figure above. The loop is closed by using additional PID controller and an optional low pass filter. The controller reads the angle <i>a</i> from the motor (filters is optionally) and determines which velocity <i>v<sub>d</sub></i> the motor should move to reach the desired angle <i>a<sub>d</sub></i> set by the user. And then the velocity controller reads the current filtered velocity from the motor <i>v<sub>f</sub></i> and sets the torque target (<i>u<sub>q</sub></i> voltage or <i>i<sub>q</sub></i> current) to the torque control loop, needed to reach the velocity <i>v<sub>d</sub></i>, set by the angle loop. 


[Go to Cascade Control (Recommended)](angle_cascade_control){: .btn .mr-2 .btn-docs}

</div>
<div class="cascade cascade-n hide" markdown="1">
<img class="width60 type type-b" src="extras/Images/an_b_i1.drawio.png">
 <img class="width60 type type-s hide" src="extras/Images/an_s_i1.drawio.png">

In **No-cascade position control** the angle control loop is closing the loop directly around the torque control loop. This means that the angle controller will be setting the torque target to the torque controller, without using the velocity controller in between.


This angle control loop is created by adding one more control loop on top of the [torque control loop](torque_control) like showed on the figure above. The loop is closed by using additional PID controller and an optional low pass filter. The controller reads the angle <i>a</i> from the motor (filters is optionally) and sets the torque target (<i>u<sub>q</sub></i> voltage or <i>i<sub>q</sub></i> current) to the torque control loop, needed to reach the desired angle <i>a<sub>d</sub></i>, set by the angle loop. 


[Go to Non-Cascade Control (Advanced)](angle_nocascade_control){: .btn .btn-docs}

</div>

## Comparison of the two strategies

Control Mode | Control Structure | Pros | Cons |
-----------------|------|---------------------|----------------|
Cascade Position Control (`angle`)| **P**(angle) <br>+ PI (velocity) | ✔️ **Safe Velocity Limits**: Inherits the velocity_limit from the inner loop, preventing runaway speeds<br>✔️ **Superior Stability**: Dual-loop damping makes it much more robust against high-inertia loads .<br>✔️ **High-Speed Precision**: Provides better tracking performance at high velocities.|❌ **Tuning Complexity**: Requires tuning two PID controllers (Velocity and Angle) plus the Low Pass Filter<br>❌ **Not always intuitive**: Conceptually hard to understand the interaction between the two loops. | 
Non-Cascade Control (`angle_nocascade`)| **PD** <br> or **PID** in special cases | ✔️ **Low Latency**: Direct path from angle to torque provides the fastest possible physical response<br>✔️ **High Stiffness**: Acts as a "Virtual Spring," reacting instantly to disturbances.<br>✔️ **Easier Setup**: Only one PID controller to tune|❌ **Speed Instability**: No internal velocity damping; can vibrate at high speeds<br>❌ **No Velocity Limit**: The motor can reach unsafe speeds instantly if the angle error is large (Runaway Risk)<br>❌ **Inertia Sensitive**: Hard to stabilize large masses without aggressive `D` gain tuning.


**Use-cases for Cascade Position Control:**
- Robotic arms with varying loads and high precision requirements
- CNC machines where stability at high speeds is critical
- Applications where the the velocity needs to be limited or variable (e.g. conveyor belts, camera gimbals, safety concerns)
- Any application where safety and reliability are a concern

[Go to Cascade Control (Recommended)](angle_cascade_control){: .btn .mr-2 .btn-docs}

**Use-cases for No-Cascade Position Control:**
- Setups where there is an external mechanical damping or low inertia load
- Application where the motor fights gravity (e.g. vertical axis) and the load is never removed (I gain is important here)
- Systems simulating a spring-damper (PD controller)

[Go to Non-Cascade Control (Advanced)](angle_nocascade_control){: .btn .btn-docs}