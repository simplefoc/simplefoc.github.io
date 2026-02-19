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
# Closed loop motion control 


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b width60" src="extras/Images/clb.png"/>
<img class="type type-s width60 hide" src="extras/Images/cls.png"/>

This is the standard motion control approach where the position sensor is used to provide feedback to the motion control loop. The motion control loop (`motor.move()`) will calculate the target torque (current or voltage) to be applied to the motor using the underlying torque/FOC control loop (`motor.loopFOC()`) in order to achieve the desired motion. 

<span class="simple">Simple<span class="foc">FOC</span>library</span> gives you the choice of using 3 different Closed-Loop control strategies: 
- [Torque control loop](torque_loop)
- [Velocity motion control](velocity_loop)
- [Position/angle motion control](angle_loop)


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

### How it works?

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

Choose the motion control type: 

<a href ="javascript:show('ac','loop');" id="btn-ac" class="btn btn-loop btn-primary">Position control (Cascaded)</a>
<a href ="javascript:show('an','loop');" id="btn-an" class="btn btn-loop">Position control (Non-Cascaded)</a>
<a href ="javascript:show('v','loop');" id="btn-v" class="btn btn-loop">Velocity control</a>
<a href ="javascript:show('t','loop');" id="btn-t" class="btn btn-loop">Torque control</a>

<div class="type type-b">
<img class="loop loop-ac width80" src="extras/Images/a_b_i.drawio.png"/>
<img class="loop loop-an width80 hide" src="extras/Images/an_b_i1.drawio.png"/>
<img class="loop loop-v width80 hide" src="extras/Images/v_b_i.drawio.png"/>
<img  class="loop loop-t width80 hide" src="extras/Images/t_b_i.drawio.png"/>

</div>
<div class="type type-s hide">

<img class="loop loop-ac width80" src="extras/Images/a_s_i.drawio.png"/>
<img class="loop loop-an width80 hide" src="extras/Images/an_s_i1.drawio.png"/>
<img class="loop loop-v width80 hide" src="extras/Images/v_s_i.drawio.png"/>
<img  class="loop loop-t width80 hide" src="extras/Images/t_s_i.drawio.png"/>

</div>


