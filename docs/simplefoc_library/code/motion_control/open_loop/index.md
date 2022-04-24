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
<a href ="javascript:show(1);" id="btn-1" class="btn  btn-primary">Velocity control</a>

<img style="display:none" id="0" class="gallery_img width80" src="extras/Images/opneloop_0001_Layer 0.jpg"/>
<img style="display:block" id="1" class="gallery_img  width80" src="extras/Images/opneloop_0000_Layer 2.jpg"/>

<span class="simple">Simple<span class="foc">FOC</span>library</span> gives you the choice of using 2 different open-loop control strategies, control strategies that do not require a position sensor
- [Velocity open-loop control](velocity_openloop)
- [Position open-loop control](angle_openloop)

<blockquote class="info"> Index search uses also uses open-loop position control, but has some additional parameters, see <a href="index_search_loop">index search</a></blockquote>

```cpp
// MotionControlType::velocity_openloop    - velocity open-loop control
// MotionControlType::angle_openloop       - position open-loop control
motor.controller = MotionControlType::angle_openloop;
```

For more information about the source code implementation of the motion control strategies check the [library source code documentation](motion_control_implementation)