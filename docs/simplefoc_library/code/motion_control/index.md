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
<span class="simple">Simple<span class="foc">FOC</span>library</span> has two main parameters that define the motion control architecture to be used (and each of them can be modified in real-time):
- Motion control mode - `motor.controller`
    - [Closed-Loop control](closed_loop_motion_control) - with position sensor
    - [Open-Loop control](open_loop_motion_control) - no position sensor
- [Torque control mode](torque_control) - `motor.torque_controller`
    - Used only in the closed-loop control modes

## Units in the library

All the motor/driver/sensor parameters and control variables are defined in the following units:

Physical value | Units | Description | Conversion to other units
--- | --- | --- | ---
Position/angle | `Radians` | Motor and sensor position in radians |  2$$\pi$$(6.14) radians = 360 degrees = 1 motor rotation
Velocity | `Rad/s` | Motor and sensor velocity in radians per second |  2$$\pi$$(6.14) rad/s = 1 motor rotation per second = 60 RPM
Torque/Current | `Amps` | Motor torque or current in Amperes |  1 Nm = $$K_t$$ Amps ($$K_t$$ is the motor torque constant)


## Torque control modes
There are three torque control types implemented in the <span class="simple">Simple<span class="foc">FOC</span>library</span>:
- Voltage - `TorqueControlType::voltage`
- DC current - `TorqueControlType::dc_current`
- FOC current - `TorqueControlType::foc_current`
<blockquote class="warning"> ⚠️ This parameter is not used if the Open-Loop control is chosen.</blockquote>

And they can be set by changing the motor attribute `torque_controller`.
```cpp
// set torque mode to be used
// TorqueControlType::voltage    ( default )
// TorqueControlType::dc_current
// TorqueControlType::foc_current
motor.torque_controller = TorqueControlType::foc_current;
```

For more in depth explanations about different torque modes visit the [torque mode docs](torque_control)

## Motion control modes
<span class="simple">Simple<span class="foc">FOC</span>library</span> implements motion control for both cases:
- [Closed-Loop control](closed_loop_motion_control) - with position sensor
- [Open-Loop control](open_loop_motion_control) - no position sensor

### Closed-Loop control
There are three Closed-Loop control types implemented in the <span class="simple">Simple<span class="foc">FOC</span>library</span>:
- Torque - `MotionControlType::torque`
- Velocity - `MotionControlType::velocity`
- Angle - `MotionControlType::angle`

And they can be set by changing motor's `controller` parameter.  
```cpp
// set motion control loop to be used
// MotionControlType::torque
// MotionControlType::velocity
// MotionControlType::angle
motor.controller = MotionControlType::angle;
```
For more in depth explanations about different Closed-Loop control loops visit the [closed-loop control docs](closed_loop_motion_control)

### Open-Loop control
Additionally you can run the motor in the open-loop, without position sensor feedback, as well:
- velocity open-loop control - `MotionControlType::velocity_openloop`
- position open-loop control - `MotionControlType::angle_openloop`

<blockquote class="info"> Index search uses also uses open-loop position control, but has some additional parameters, see <a href="index_search_loop">index search</a></blockquote>

And they too can be enabled by setting motor's `controller` parameter.  
```cpp
// MotionControlType::velocity_openloop    - velocity open-loop control
// MotionControlType::angle_openloop       - position open-loop control
motor.controller = MotionControlType::angle_openloop;
```
For more in depth explanations about different Closed-Loop control loops visit the [open-loop control docs](open_loop_motion_control)

For more information about the source code implementation of the motion control strategies check the [library source code documentation](motion_control_implementation)