---
layout: default
title: Torque/FOC Control
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /torque_control
nav_order: 6
parent: Writing the Code
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: True
has_toc: False
toc: true
---

# Torque/FOC control loop

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> Hybrid Stepper motors</a>

<img class="type type-b  width40" src="extras/Images/torque_control/torque_concept.png"/>
<img class="type type-s hide width40" src="extras/Images/torque_control/torque_concept_s.png"/>
<img class="type type-h hide width40" src="extras/Images/torque_control/torque_concept_hs.png"/>

FOC or torque control is the lowest level control loop of the <span class="simple">Simple<span class="foc">FOC</span>library</span>. It is the base of all the other control loops and it is responsible for controlling the torque of the motor. This control loop is implemented in the `motor.loopFOC()` function of the motor class and it is executed at the highest frequency $$f_{TC}$$ possible  (usually well above 1kHz, depending on the MCU performance).

This control loop allows the user to set the target torque (voltage or current) determines the optimal phase voltages to be applied to the motor in order to achieve it. 

## Torque control modes
There are four torque control types implemented in the <span class="simple">Simple<span class="foc">FOC</span>library</span>:
- [Voltage mode](#voltage-mode---voltage) - `voltage`
- [Estimated current mode](#estimated-current-mode---estimated_current) - `estimated_current`
- [DC current mode](#dc-current-mode---dc_current) - `dc_current`
- [FOC current mode](#foc-current-mode---foc_current) - `foc_current`

And they can be set by changing the motor attribute `torque_controller`.
```cpp
// set torque mode to be used
// TorqueControlType::voltage    ( default )
// TorqueControlType::estimated_current
// TorqueControlType::dc_current
// TorqueControlType::foc_current
motor.torque_controller = TorqueControlType::foc_current;
```

Here is the quick comparison of the block diagrams of the different torque control types:

Choose the motor type: 

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> Hybrid Stepper motors</a>

Choose the voltage control type: 

<a href ="javascript:show(0,'loop');" id="btn-0" class="btn btn-loop">FOC current mode</a>
<a href ="javascript:show(1,'loop');" id="btn-1" class="btn btn-loop">DC current mode</a>
<a href ="javascript:show(2,'loop');" id="btn-2" class="btn btn-loop">Estimated current</a>
<a href ="javascript:show(3,'loop');" id="btn-3" class="btn btn-loop">Voltage mode</a>
<a href ="javascript:show(4,'loop');" id="btn-4" class="btn btn-loop btn-primary">Conceptual</a>

<div class="type type-b">
<img class="loop loop-0 hide" src="extras/Images/torque_control/foc2_bc.png"/>
<img class="loop loop-1 hide " src="extras/Images/torque_control/dc1_bc.png"/>
<img class="loop loop-2 hide " src="extras/Images/torque_control/ec0_bc.png"/>
<img class="loop loop-3  hide" src="extras/Images/torque_control/vc_b1c.png"/>
<img class="loop loop-4 " src="extras/Images/torque_control/torque_concept_1.png"/>
</div>
<div class="type type-s hide">

<img class="loop loop-0 hide" src="extras/Images/torque_control/foc2_sc.png"/>
<img class="loop loop-1 hide " src="extras/Images/torque_control/dc1_sc.png"/>
<img class="loop loop-2 hide " src="extras/Images/torque_control/ec0_sc.png"/>
<img class="loop loop-3  hide" src="extras/Images/torque_control/vc_sc.png"/>
<img class="loop loop-4 " src="extras/Images/torque_control/torque_concept_sc.png"/>
</div>

<div class="type type-h hide">

<img class="loop loop-0 hide" src="extras/Images/torque_control/foc2_hc.png"/>
<img class="loop loop-1 hide " src="extras/Images/torque_control/dc1_hc.png"/>
<img class="loop loop-2 hide " src="extras/Images/torque_control/ec0_hc.png"/>
<img class="loop loop-3  hide" src="extras/Images/torque_control/vc_hc.png"/>
<img class="loop loop-4 " src="extras/Images/torque_control/torque_concept_hsc.png"/>
</div>

## Which torque control type should I use?

The choice of the torque control type depends on the specific application, the hardware available and the performance requirements. 

In short: **Voltage mode** is the universal starting point. **Estimated current** is the high-performance upgrade for those without sensors. **FOC current** is the gold standard for true torque control. **DC current** is for special cases or low-power MCUs with current sensing, but it is generally not recommended.


**RULE OF THUMB:**
- **Gimbal Motors** (High Resistance $$R>5\Omega$$):
  - Low speed/Low-power MCU $$\to$$ **Voltage mode**.
  - High speed $$\to$$ **Estimated current** (if parameters known) or **FOC current** (if current sensing available).
- **Drone Motors** (Low Resistance $$R<1\Omega$$):
  - Always prefer **FOC current** (if current sensing available)
  - Otherwise use **Estimated current** (if you can get hold of motor parameters)
  - ⚠️ Warning: Use **Voltage mode** only when no other choice and with very low limits to avoid frying your hardware.
- **Stepper Motors** (Moderate Resistance $$R \approx 2\Omega$$):
  - Low-speed applications or when simplicity is key: **Voltage mode** 
  - For better performance at higher speeds, **Estimated current** (if you have the motor parameters).
  - If current sensing available all speeds, **FOC current** is ideal.
- **DC Current Mode** (Special Cases):
  - Use if you have current sensors but a slow MCU (e.g., Atmega328).
  - Use if your sensor only measures total current magnitude.


| Torque Mode | Current control | High Speed Performance | Low Speed Performance | MCU performance Required | Requirements | Best For...| 
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
Voltage | ❌ None | ❌ Poor (BEMF limits torque) | ✔️ Excellent (Very smooth) | Low | None | Beginners, Gimbal motors, and low-speed testing.
Estimated Current | ⚠️ Estimated (Model-based) | ✔️ Good (BEMF + lag compensation) | ✔️ Excellent (Smooth & Quiet) | Low / Moderate | Phase resistance ($$R$$), KV rating ($$KV$$) and inductance ($$L_q, L_d$$) | High-speed motors without current sensors (Drone motors).
DC Current | ✔️ Precise (Magnitude) | ⚠️ Moderate (Phase lag) | ⚠️ Moderate (Commutation noise) | Moderate | Current Sensing hardware | Low-performance MCUs where current limiting is required.
FOC Current | ✔️ Precise (Vector) | ✔️ Excellent (Optimal timing) | ✔️ Excellent (True torque) | High | Current Sensing hardware | Professional apps, high-dynamic robots, and true torque control.


<blockquote class="info"> 

<details markdown="1">
<summary style="cursor: pointer;">📊 Click here for a bit about the torque control types</summary>

Torque control type | Pros | Cons
----- | ----- | ------
Voltage  | ✔️ Very simple and fast <br>✔️ Good performance with any MCU <br> ✔️ Very smooth on low speeds<br> ✔️ No current sense needed   | ❌ Not optimal on high speeds <br> ❌ Cannot control true current draw <br> ❌ Torque is approximated (low error on low speeds)
Estimated current | ✔️ More efficient than voltage mode on higher speeds <br> ✔️ Can achieve full speed of the motor <br> ✔️ Much better backdrivability (close to the true torque control) <br> ✔️ Can control and limit estimated current draw <br> ✔️ No current sense needed  | ❌ Needs motor parameters to be set <br> ❌ Sensitive to wrong motor parameters (the difference between the estimated and real current can become big if the motor parameters are not set correctly)
DC current  | ✔️ Can control true current draw <br> ✔️ Suitable for low performance MCUs <br> ✔️ Current limiting  | ❌ More complex to execute than both voltage and estimated current modes (slower) <br> ❌ Can achieve lower speeds than both voltage and estimated current modes <br>❌ Torque is approximated (high error on high speeds) <br> ❌ Needs current sensing
FOC current  | ✔️ True torque control (any velocity) <br> ✔️ Can control true current draw <br> ✔️ Very efficient on higher velocities <br> ✔️ Current limiting | ❌ The most complex to execute (slowest) <br> ❌ Not suitable for low-performing MCUs (can become unstable) <br> ❌ Needs current sensing

</details>
</blockquote>


### Voltage mode - `voltage`
Torque control through voltage is the most basic torque control type, and it provides you an abstraction of the BLDC/Stepper motor so that you can control it as a DC motor. It is based on the principle that the current is directly proportional to the voltage (it neglects the current dynamics) and therefore does not need any current sensing hardware. For more info about this approach, visit our [digging deeper section](voltage_torque_control). 

**Configuration parameters**
- No algorithm parameters 
- Voltage limit is set by `motor.voltage_limit` parameter and it is recommended to be set to a lower value than the maximum voltage the driver can provide in order to avoid overheating of the motor and driver.

[Set up voltage mode](voltage_torque_mode){: .btn .btn-docs}

### Estimated current mode - `estimated_current`

This mode uses the motor's physical parameters: Phase Resistance ($$R$$), KV rating ($$KV$$) and axis inductance ($$L_q, L_d$$) to estimate the current draw without physical sensors. It is significantly more efficient than raw voltage mode because it accounts for back-EMF and phase lag, ensuring the torque remains consistent even as the motor speeds up. It is the recommended mode when current sensing hardware is not available, especially for higher-KV drone motors.

**Configuration parameters**
- Motor parameters: Phase Resistance ($$R$$), KV rating ($$KV$$) and axis inductance ($$L_q, L_d$$) need to be set for the motor. 
- Current limit is set by `motor.current_limit` parameter and it is recommended to be set to a lower value than the maximum current the driver can provide in order to avoid overheating of the motor and driver.
- Low pass filter time constant `LPF_velocity.Tf` if velocity feedback in noisy.

[Set up estimated current mode](estimated_current_mode){: .btn .btn-docs}

### DC current mode - `dc_current`
DC current control mode enables you to control the current of the BLDC/Stepper motor as if it was a DC motor. Current sensing is used to obtain an overall magnitude of the current the motor is drawing and its direction, and the assumption is that the torque is proportional to the overall current. The benefit of this approach is that the true current set to the BLDC/Stepper motor can be controlled very precisely it is a bit faster and more stable to execute for less-performant microcontrollers (such as Atmega328 family).

**Configuration parameters**
- Current sensing hardware is required to be able to use this mode.
- Current limit is set by `motor.current_limit` parameter and it is recommended to be set to a lower value than the maximum current the driver can provide in order to avoid overheating of the motor and driver.
- PID controller parameters of `PID_current_q` need to be tuned for the specific motor and setup. 
- Low pass filter time constant `LPF_current_q.Tf` needs to be tuned for the specific motor and setup as well, especially if the current feedback is noisy.

[Set up DC current mode](dc_current_torque_mode){: .btn .btn-docs}

### FOC current mode - `foc_current`
FOC current control is the only true torque control approach. It controls two components of the current vector `q` and `d`. The torque is assumed to be proportional to the `q` current component and the `d` component of the current is controlled to remain equal to 0.

**Configuration parameters**
- Current sensing hardware is required to be able to use this mode.
- Current limit is set by `motor.current_limit` parameter and it is recommended to be set to a lower value than the maximum current the driver can provide in order to avoid overheating of the motor and driver.
- PID controller parameters of `PID_current_q` and `PID_current_d` need to be tuned for the specific motor and setup. 
- Low pass filter time constant `LPF_current_q.Tf` and `LPF_current_d.Tf` needs to be tuned for the specific motor and setup as well, especially if the current feedback is noisy.

[Set up FOC current mode](foc_current_torque_mode){: .btn .btn-docs}



## Related topics

[See here for a dive deep into the torque theory.](voltage_torque_control){: .btn .btn-docs}
[Go here for the implementation details.](torque_control_implementation){: .btn .btn-docs}