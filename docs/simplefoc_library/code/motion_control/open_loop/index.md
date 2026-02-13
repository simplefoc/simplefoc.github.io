---
layout: default
title: Open-Loop control
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /open_loop_motion_control
nav_order: 3
has_children: True
has_toc: False
parent: Motion Control
grand_parent: Writing the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---
# Open loop Motion control 

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b width60" src="extras/Images/olb.png"/>
<img class="type type-s width60 hide" src="extras/Images/ols.png"/>

This is the standard motion control approach where the position sensor is used to provide feedback to the motion control loop. The motion control loop (`motor.move()`) will calculate the target torque (current or voltage) to be applied to the motor using the underlying torque/FOC control loop (`motor.loopFOC()`) in order to achieve the desired motion. 

<span class="simple">Simple<span class="foc">FOC</span>library</span> gives you the choice of using 2 different open-loop control strategies, control strategies that do not require a position sensor
- [Velocity open-loop control](velocity_openloop)
- [Position open-loop control](angle_openloop)

<blockquote class="info"> Index search uses also uses open-loop velocity control, but has some additional parameters, see <a href="index_search_loop">index search</a></blockquote><br>


You set the open-loop modes by changing the `motor.controller` variable. If you want to control the motor angle you will set the `controller` to `MotionControlType::angle_openloop` and if you want to control motor angular velocity `MotionControlType::velocity_openloop`. 
```cpp
// MotionControlType::velocity_openloop    - velocity open-loop control
// MotionControlType::angle_openloop       - position open-loop control
motor.controller = MotionControlType::angle_openloop;
```


|Velocity control | Position/angle control |
|----------------|------------------|
| `MotionControlType::velocity_openloop` | `MotionControlType::angle_openloop` |
|[Go to Velocity control docs](velocity_openloop){: .btn .mr-2 .btn-docs} |[Go to Position/angle control docs](angle_openloop){: .btn .mr-2 .btn-docs}|


### How it works?

Choose the motor type: 

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

Choose the voltage control type: 

<a href ="javascript:show(0,'loop');" id="btn-0" class="btn btn-loop btn-primary">Position control</a>
<a href ="javascript:show(1,'loop');" id="btn-1" class="btn btn-loop">Velocity control</a>

<div class="type type-b">
<img class="loop loop-0 width60" src="extras/Images/olb_p_i.png"/>
<img class="loop loop-1 width60 hide" src="extras/Images/olb_v_i1.png"/>

</div>
<div class="type type-s hide">

<img  class="loop width60 loop-0" src="extras/Images/ols_p_i.png"/>
<img class="loop width60 loop-1 hide" src="extras/Images/ols_v_i1.png"/>

</div>

For more information about the source code implementation of the motion control strategies check the [library source code documentation](motion_control_implementation)