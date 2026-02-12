---
layout: default
title: Current/Torque control
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /torque_loop
nav_order: 1
parent: Closed-Loop control
grand_parent: Motion Control
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: True
has_toc: False
toc: true
---

# Torque control loop

This motion control mode is enabled setting the `controller` parameter to:
```cpp
// torque control loop
motor.controller = MotionControlType::torque;
```

In this mode the user can control the motor torque directly by setting the `motor.target` variable to the desired torque value. The torque control loop will then calculate the necessary voltage or current to be applied to the motor in order to achieve the desired torque.

Depending on the torque control mode used, the input to the torque control loop will be either the target voltage <i>u<sub>q</sub></i> or the target current <i>i<sub>q</sub></i>. See more about the different torque control modes in the [torque control documentation](torque_control).


You can find some examples in the `motion_control/torque_control/` library folder.

## How it works

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

If the [voltage mode](voltage_torque_mode) is used, the input in the control loop will be the target voltage <i>u<sub>q</sub></i>. 
<div class="type type-b">
<a name="foc_image"></a><img class="width50" src="extras/Images/torque_control_v.png">
</div>
<div class="type type-s hide">
<img class="width50" src="extras/Images/torque_control_stepper_v.png">
</div>
And if one of the current based torque control modes ([estimated current](estimated_current_mode), [DC current](dc_current_torque_mode) or [FOC current](foc_current_torque_mode)) is used, the input in the control loop will be the target current <i>i<sub>q</sub></i>.
<div class="type type-b">
<a name="foc_image"></a><img class="width50" src="extras/Images/torque_control_i.png">
</div>
<div class="type type-s hide">
<img class="width50" src="extras/Images/torque_control_stepper_i.png">
</div>

## Control parameters

This motion control mode does not have any additional parameters on its own. It relies entirely on the underlying torque control mode. See the [torque control documentation](torque_control) for more information about the different torque control modes and their parameters.

The only real parameter of the torque control motion control is the limit enforeced on the target variable `motor.target ` and the actual voltage or current applied to the motor. This can be set by either limiting the voltage or the current, depending on the torque control mode used. For example if using voltage mode you can limit the voltage by setting

```cpp
motor.updateVoltageLimit(2.0); // set the voltage limit to 2 V
// or less preferred way
motor.voltage_limit = 2.0; // limit the voltage to 2 V
```
And if using current mode you can limit the current by setting

```cpp
motor.updateCurrentLimit(0.5); // set the current limit to 0.5 A
// or less preferred way
motor.current_limit = 0.5; // limit the current to 0.5 A
```
## Feed-forward terms

<span class="simple">Simple<span class="foc">FOC</span>library</span> also allows you to add feed-forward terms to the torque control loop. There are two feed-forward terms that can be added to the torque control loop:

| Feed-forward term | Variable | Description | Used in Modes |
|-------------------|-------------|-------------|---------------|
| Current feed-forward | `motor.feed_forward_current` | This term adds a constant current to the torque control loop. <br> For example, it can be used to compensate for friction or other constant loads on the motor. | Current mode |
| Voltage feed-forward | `motor.feed_forward_voltage` | This term adds a constant voltage to the torque control loop. <br> For example, it can be used to compensate for back-EMF or other voltage drops in the system. | Voltage mode <br> Current mode - see [torque control documentation](torque_control) |

Both feed-forward terms have q and d components, but only the q component is actually used for torque generation. 

To set the feed-forward terms, you modify these variables directly in runtime:
```cpp
// set current feed-forward term
motor.feed_forward_current.q = 0.1; // add 0.1 A to the q-axis current
// set voltage feed-forward term
motor.feed_forward_voltage.q = 0.1; // add 0.1 V to the q-axis voltage
```
One can also set the d-axis feed-forward terms in the same way
```cpp
// set current feed-forward term
motor.feed_forward_current.d = 0.1; // add 0.1 A to the d-axis current
// set voltage feed-forward term
motor.feed_forward_voltage.d = 0.1; // add 0.1 V to the d-axis voltage
```

## Units
The units of the `motor.target` variable in torque control mode depend on the torque control mode

| Type | Torque control mode | `motor.target` Units |
|------|---------------------|-----------------------------|
| Voltage mode| `voltage` | Volts |
| Current mode| `estimated_current`<br> `dc_current`<br> `foc_current` | Amps |

Both voltage and current do not represent the actual torque of the motor. The actual torque can be calculated by multiplying the current by the motor's torque constant.

$$\tau = K_t \cdot i_q$$

If using voltage mode, there is no simple way to corelate it with the actual torque, except for slow speeds where the back-EMF is negligible and the motor current can be calculated as $$i_q = \frac{u_q}{R}$$ ($$R$$ is the motor phase resistance), making the torque proportional to the voltage:

$$\tau \propto u_q$$


Find more information about the units used in the library in the [units documentation](library_units).


## Torque control behavior

There two distinct behaviours of the motor when using torque control mode, depending on the torque control mode used:
- If using voltage mode
- If using current mode

### Voltage mode

In voltage mode, the motor will behave as if it is being controlled by a voltage source. You can imagine it as if you have a DC motor and you are controlling the voltage applied to it. The higher the voltage, the higher the torque, but also the higher the speed the motor can reach. When the motor is static, the torque is proportional to the voltage. However, as the motor starts to move, the back-EMF (electromotive force) generated by the motor will oppose the applied voltage, making the actual torque decrease as the speed increases. This means that at higher speeds, the motor will not be able to reach the same torque as at lower speeds.

<blockquote class="info" markdown="1"> <p class="heading">RULE OF THUMB</p> 
For voltage mode remember: the higher the voltage, the higher the speed the motor can reach. Or in other words voltage is proportional to speed. 

</blockquote>

### Current mode

In current mode, the motor will behave as if it is being controlled by a current source. The torque will be directly proportional to the current, regardless of the speed of the motor, and ideally the motor is able to maintain the same torque at all speeds. This is because the current control loop will adjust the voltage applied to the motor in order to maintain the desired current, even as the back-EMF changes with speed.

In current mode, the motor will be able to reach high velocities even with very low current target. The current is not proportional to the speed, but to the acceleration of the motor. This means that if you set a low current target, the motor will accelerate slowly, but it can still reach high speeds if given enough time. Conversely, if you set a high current target, the motor will accelerate quickly and reach high speeds faster. 


<blockquote class="info" markdown="1"> <p class="heading">RULE OF THUMB</p> 
For current mode remember: the higher the current, the faster the motor will accelerate. Or in other words current is proportional to acceleration. 

</blockquote>

## Project examples
Here is one project example which uses torque control and describes the full hardware + software setup needed.

<div class="image_icon width30">
    <a href="simplefoc_pendulum">
        <img src="extras/Images/foc_pendulum.jpg" >
        <i class="fa fa-external-link-square fa-2x"></i>
    </a>
</div>

Find more projects in the [example projects](examples) section.