---
layout: default
title: Motion Control
parent: Writing the Code
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /motion_control
nav_order: 6
has_children: True
has_toc: False
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# Motion control 
The main parameter of the <span class="simple">Simple<span class="foc">FOC</span>library</span> that defines the motion control architecture is 
- Motion control mode - `motor.controller`
    - [Closed-Loop control](#closed-loop-control) - with position sensor
    - [Open-Loop control](#open-loop-control) - no position sensor
    - [Custom control mode](#custom-control-mode) - user defined control loop - 📢 NEW experimental feature

All the motion control modes are completely transparent regardless if you decide to use BLDC or Stepper motors. 

## Motion control modes overview

| Mode | Variable |Type | Position sensor Required | Description | Units |
|------|-----|------|-----------------|----------| ----|
| Torque | `torque` | Closed-Loop | Yes | Direct torque/current control | Amps or Volts (depending on torque control mode) |
| Velocity | `velocity` | Closed-Loop | Yes | Speed regulation | Radians per second |
| Angle | `angle` | Closed-Loop | Yes | Position control with standard cascade | Radians |
| Angle<br> (No Cascade) | `angle_nocascade` | Closed-Loop | Yes | Position control without cascade | Radians |
| Velocity Open-Loop | `velocity_open_loop` | Open-Loop | No | Speed control without feedback | Radians per second |
| Angle Open-Loop | `angle_open_loop` | Open-Loop | No | Position control without feedback | Radians |
| Custom control | `custom` | User defined | Depends on implementation | User defined | User defined |


<blockquote class="info" markdown="1"> <p class="heading">Torque and Motion control compatibility</p> The motion control mode is independent of the torque control mode, so you can use any of the torque control modes with any of the motion control modes. For example you can use the voltage mode to control the motor position, or FOC current mode to control the motor velocity. You can also update the motion control mode in real-time. See the [torque control docs](torque_control) for more information. 

</blockquote>

<blockquote class="warning" markdown="1"> <p class="heading">Units warning</p> The units in the library are following standard motion control conventions. Ampers, Volts, Radians and Radians per second are used throughout the library. If you want to use different units, you will need to convert them accordingly. See the [units documentation](library_units). 

</blockquote>

All the target values for the motion control modes are set by changing the `target` parameter of the motor. This parameter will be interpreted differently depending on the motion control mode. 

| Motion control mode | `motor.target` Units |
|---------------------|-----------------------------|
| Torque | Amps or Volts (depending on torque control mode) |
| Velocity | Radians per second |
| Angle | Radians |
| Angle (No Cascade) | Radians | 
| Velocity Open-Loop | Radians per second |
| Angle Open-Loop | Radians |
| Custom control | User defined |

## Closed-Loop control

There are three Closed-Loop control types implemented in the <span class="simple">Simple<span class="foc">FOC</span>library</span>:
- Torque - `MotionControlType::torque`
- Velocity - `MotionControlType::velocity`
- Angle/Positon - two types: 
    - Cascade controlled: `MotionControlType::angle`
    - Non-cascade controlled: `MotionControlType::angle_nocascade`


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b width60" src="extras/Images/clb.png"/>
<img class="type type-s width60 hide" src="extras/Images/cls.png"/>

All of these control modes **require a position sensor** to be used, as they rely on the feedback from the sensor to calculate the necessary voltage or current to be applied to the motor in order to achieve the desired motion.


And they can be set by changing motor's `controller` parameter.  
```cpp
// set motion control loop to be used
// MotionControlType::torque
// MotionControlType::velocity
// MotionControlType::angle
// MotionControlType::angle_nocascade
motor.controller = MotionControlType::angle;
```
[Go to Closed-loop control docs](closed_loop_motion_control){: .btn .mr-2 .btn-docs}

## Open-Loop control


Additionally you can run the motor in the open-loop, without position sensor feedback, as well:
- velocity open-loop control - `MotionControlType::velocity_openloop`
- position open-loop control - `MotionControlType::angle_openloop`

<blockquote class="info"> Index search also uses open-loop position control, but has some additional parameters, see <a href="index_search_loop">index search</a></blockquote>

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b width60" src="extras/Images/olb.png"/>
<img class="type type-s width60 hide" src="extras/Images/ols.png"/>

These control modes do **NOT require a position sensor**, these control modes estimate the motor position and velocity based. Better said they assume that the motor can follow the target velocity or position without any load or disturbance, which is not always the case. 



And they too can be enabled by setting motor's `controller` parameter.  
```cpp
// MotionControlType::velocity_openloop    - velocity open-loop control
// MotionControlType::angle_openloop       - position open-loop control
motor.controller = MotionControlType::angle_openloop;
```

[Go to Open-loop control docs](open_loop_motion_control){: .btn .mr-2 .btn-docs}


## Custom control mode

The <span class="simple">Simple<span class="foc">FOC</span>library</span> also gives you the possibility to implement your own custom control loop. This is an experimental feature and we are not yet 100% happy with the implementation, but we wanted to give you the possibility to try it out and give us feedback. The custom control mode allows you to implement any control loop you want, and link it to the library. This way you can use the library's functions for reading sensors, controlling the motor and so on, while implementing your own control strategy.

```cpp

// MotionControlType::custom - user defined control loop
motor.controller = MotionControlType::custom;

// link your custom control function that will be called in the main control loop  motor.move() funciton.
motor.linkCustomMotionControl(&myCustomControlFunction);
```

[Go to custom control documentation](custom_control){: .btn .mr-2 .btn-docs}

## Digging deeper

For more information about the source code implementation of the motion control strategies check the [library source code documentation](motion_control_implementation)