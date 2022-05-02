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


<script type="text/javascript">
    function show(id){
        Array.from(document.getElementsByClassName('gallery_img')).forEach(
        function(e){e.style.display = "none";});
        document.getElementById(id).style.display = "block";
        Array.from(document.getElementsByClassName("btn-primary")).forEach(
        function(e){e.classList.remove("btn-primary");});
        document.getElementById("btn-"+id).classList.add("btn-primary");
    }
</script>

<a href ="javascript:show(0);" id="btn-0" class="btn">Position control</a>
<a href ="javascript:show(1);" id="btn-1" class="btn">Velocity control</a>
<a href ="javascript:show(2);" id="btn-2" class="btn  btn-primary">Torque control</a>

<img style="display:none" id="0" class="gallery_img" src="extras/Images/closedloop_0000_Layer 3.jpg"/>
<img style="display:none" id="1" class="gallery_img" src="extras/Images/closedloop_0001_Layer 2.jpg"/>
<img style="display:block" id="2"  class="gallery_img" src="extras/Images/closedloop_0002_Layer 1.jpg"/>

<span class="simple">Simple<span class="foc">FOC</span>library</span> gives you the choice of using 3 different Closed-Loop control strategies: 
- [Torque control loop](torque_control)
- [Velocity motion control](velocity_loop)
- [Position/angle motion control](angle_loop)

You set it by changing the `motor.controller` variable. If you want to control the motor angle you will set the `controller` to `MotionControlType::angle`, if you seek the torque of the BLDC motor either through voltage or the current use `MotionControlType::torque`, if you wish to control motor angular velocity `MotionControlType::velocity`. 

```cpp
// set FOC loop to be used
// MotionControlType::torque
// MotionControlType::velocity
// MotionControlType::angle
motor.controller = MotionControlType::angle;
```

For more information about the source code implementation of the motion control strategies check the [library source code documentation](motion_control_implementation)