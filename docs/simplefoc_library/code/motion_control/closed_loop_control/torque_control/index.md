---
layout: default
title: Torque Control
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /torque_control
nav_order: 1
parent: Closed-Loop control
grand_parent: Motion Control
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: True
has_toc: False
---

# Torque control loop


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

<a href ="javascript:show(0);" id="btn-0" class="btn">FOC current mode</a>
<a href ="javascript:show(1);" id="btn-1" class="btn">DC current mode</a>
<a href ="javascript:show(2);" id="btn-2" class="btn">Voltage mode - estimated current</a>
<a href ="javascript:show(3);" id="btn-3" class="btn btn-primary">Voltage mode</a>

<img style="display:none" id="0" class="gallery_img" src="extras/Images/torque_modes_0000.jpg"/>
<img style="display:none" id="1" class="gallery_img" src="extras/Images/torque_modes_0001.jpg"/>
<img style="display:none" id="2" class="gallery_img" src="extras/Images/torque_modes_0002.jpg"/>
<img style="display:block" id="3"  class="gallery_img" src="extras/Images/torque_modes_0003.jpg"/>

<span class="simple">Simple<span class="foc">FOC</span>library</span> gives you the choice of using 3 different torque control strategies:
- [Voltage mode](voltage_torque_mode) - `voltage`
- [DC current mode](dc_current_torque_mode) - `dc_current`
- [FOC current mode](foc_current_torque_mode) - `foc_current`

In short **voltage control mode** is the simplest approximation of the motor torque control and it so basic that can be run on any motor+driver+mcu combination out there. **DC current mode** is teh next step of the motor's torque approximation which is much more exact than the voltage mode but requires current sensing and much stronger microcontroller. **FOC current mode** is controls motor's true torque and it is not an approximation, it also requires current sensor and even more processing power than the DC current mode. See in depth explanations in [torque mode docs](torque_control). 

This motion control mode is enabled setting the `controller` parameter to:
```cpp
// torque control loop
motor.controller = MotionControlType::torque;
```

If the voltage control mode is used and if the user does not provide the phase resistance parameter to the motor, the input to the torque control loop will be the target voltage <i>U<sub>q</sub></i>:

<a name="foc_image"></a><img src="extras/Images/torque_loop_v.png">

And if one of the current based torque control modes (DC current or FOC current) is used, the input in the control loop will be the target current <i>i<sub>q</sub></i>. The same is true in the voltage mode if the user provides the phase resistance value to the motor class. 

<a name="foc_image"></a><img src="extras/Images/torque_loop_i.png">

The torque control loop is used as a base for all other motion control loops.  For more info about the content of the blue boxes check the [torque mode docs](torque_control).

## Configuration parameters
Depending on the torque control type you wish to use there are different parameters that you need to consider. 
- [Voltage mode](voltage_torque_mode)  - the simplest one - no parameters except maybe `motor.phase_resistance`
- [DC current mode](dc_current_torque_mode) - 1xPID controller + 1xLPF
- [FOC current mode](foc_current_torque_mode) - 2xPID controller + 2xLPF filters 

For more information about the source code implementation of the motion control strategies check the [library source code documentation](motion_control_implementation)


## Comparison

Torque control type | PROS | CONS
----- | ----- | ------
Voltage  | ✔️ Very simple and fast <br>✔️ Good performance with any MCU <br> ✔️ Very smooth on low speeds<br> ✔️ No current sense needed   | ❌ Not optimal on high speeds <br> ❌ Cannot control true current draw <br> ❌ Torque is approximated (low error on low speeds)
DC current  | ✔️ Can control true current draw <br> ✔️ Suitable for low performance MUCs <br> ✔️ Current limiting  | ❌ More complex to execute  (slower) <br> ❌ Can achieve lower speeds than voltage mode <br>❌ Torque is approximated (low error on low speeds) <br> ❌ Needs current sensing
FOC current  | ✔️ True torque control (any velocity) <br> ✔️ Can control true current draw <br> ✔️ Very efficient on higher velocities <br> ✔️ Current limiting | ❌ The most complex to execute (slowest) <br> ❌ Not suitable for low-performing MCUs (can become unstable) <br> ❌ Needs current sensing

### Voltage mode - `voltage`
Torque control through voltage is the most basic torque control type, and it provides you an abstraction of the BLDC motor so that you can control it as a DC motor. It is based on the principle that the current is directly proportional to the voltage (it neglects the current dynamics) and therefore does not need any current sensing hardware.  For more info about this approach, visit our [digging deeper section](voltage_torque_control). **This torque control approach will be able to work on any BLDC driver board, regardless if it has current sensing or not.**

### DC current mode - `dc_current`
DC current control mode enables you to control the current of the BLDC motor as if it was a DC motor. Current sensing is used to obtain a overall magnitude of the current the motor is drawing and its direction, and the assumption is that the torque is proportional to the overall current. The benefit of this approach is that the true current set to the BLDC motor can be controlled very precisely it is a bit faster and more stable to execute for less-performant microcontrollers (such as Atmega328 family).

### FOC current mode - `foc_current`
FOC current control is the only true torque control approach. It controls two components of the current vector `q` and `d`. The torque is assumed to be proportional to the `q` current component and the `d` component of the current is controlled to remain equal to 0.

## Project examples
Here is one very cool project example which uses torque control and describes the full hardware + software setup needed.

<div class="image_icon width30">
    <a href="simplefoc_pendulum">
        <img src="extras/Images/foc_pendulum.jpg" >
        <i class="fa fa-external-link-square fa-2x"></i>
    </a>
</div>

Find more projects in the [example projects](examples) section.