---
layout: default
title: Closed-Loop control
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /closed_loop_motion_control
nav_order: 2
has_children: True
has_toc: False
parent: Motion Control
grand_parent: Writing the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---
# Motion control 


Choose the motor type: 

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

Choose the voltage control type: 

<a href ="javascript:show(0,'loop');" id="btn-0" class="btn btn-loop btn-primary">Position control</a>
<a href ="javascript:show(1,'loop');" id="btn-1" class="btn btn-loop">Velocity control</a>
<a href ="javascript:show(2,'loop');" id="btn-2" class="btn btn-loop">Torque control</a>

<div class="type type-b">
<img class="loop loop-0 width80" src="extras/Images/closedloop_0000_Layer 3.jpg"/>
<img class="loop loop-1 width80 hide" src="extras/Images/closedloop_0001_Layer 2.jpg"/>
<img  class="loop loop-2 width80 hide" src="extras/Images/closedloop_0002_Layer 1.jpg"/>

</div>
<div class="type type-s hide">

<img id="4" class="loop width80 loop-0" src="extras/Images/closed_loop_stepper3.jpg"/>
<img id="5" class="loop width80 loop-1 hide" src="extras/Images/closed_loop_stepper2.jpg"/>
<img id="6" class="loop width80 loop-2 hide" src="extras/Images/closed_loop_stepper1.jpg"/>

</div>


<span class="simple">Simple<span class="foc">FOC</span>library</span> gives you the choice of using 3 different Closed-Loop control strategies: 
- [Torque control loop](torque_loop)
- [Velocity motion control](velocity_loop)
- [Position/angle motion control](angle_loop)

You set it by changing the `motor.controller` variable. If you want to control the motor angle you will set the `controller` to `MotionControlType::angle` or `MotionControlType::angle_nocascade`, if you want to control the torque of the BLDC or Stepper motors either through voltage or the current use `MotionControlType::torque`, if you wish to control motor angular velocity `MotionControlType::velocity`. 

```cpp
// set FOC loop to be used
// MotionControlType::torque
// MotionControlType::velocity
// MotionControlType::angle
// MotionControlType::angle_nocascade
motor.controller = MotionControlType::angle;
```

| Torque control | Velocity control | Position/angle control |
|----------------|------------------|-----------------------|
| `MotionControlType::torque` | `MotionControlType::velocity` | `MotionControlType::angle` or<br> `MotionControlType::angle_nocascade` |
|[Go to Torque control docs](torque_loop){: .btn .mr-2 .btn-docs} | [Go to Velocity control docs](velocity_loop){: .btn .mr-2 .btn-docs} |[Go to Position/angle control docs](angle_loop){: .btn .mr-2 .btn-docs}|

