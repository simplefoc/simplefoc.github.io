---
layout: default
title: Estimated Current Mode
parent: Torque/FOC Control
grand_parent: Writing the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /estimated_current_mode
nav_order: 2
toc: true
---


# Estimated Current Mode
This torque control approach allows you to run BLDC and Stepper motors like a simple DC motor: you set the target current $$i_q$$ and the FOC algorithm computes the phase voltages $$u_a$$, $$u_b$$, and $$u_c$$. This mode is enabled by:
```cpp
// estimated current torque control mode
motor.torque_controller = TorqueControlType::estimated_current;
```

This approach is model based and requires some motor parameters to work. Depending on the parameters you provide, the library will be able to estimate the current in the motor and compensate for the back‑EMF voltage, which allows for accurate torque control over a wide speed range.
There are four levels of estimated current control, each depending on the motor parameters provided by the user:
- [Pure voltage control](#pure-voltage-control) **(Beginer)**  - no motor parameters needed 
- [Estimated current control](#voltage-control-with-current-estimation) - phase resistance $$R$$ required
- [Estimated current control with Back-EMF compensation](#voltage-control-with-current-estimation-and-back-emf-compensation) **(Recommended)** - required phase resistance $$R$$ and $$KV$$ rating of the motor 
- [Estimated current control with Back-EMF and lag compensation](#voltage-control-with-current-estimation-and-back-emf-compensation) **(Advanced)**- required phase resistance $$R$$, inductance $$L$$ and $$KV$$ rating of the motor 
 
## Comparison of Torque Control Methods

Block diagrams of the four torque control techniques based on voltage control can be represented as:

Choose the motor type: 

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> Hybrid Stepper motors</a>

Choose the voltage control type: 

<a href ="javascript:show('v','loop');" id="btn-v" class="btn btn-loop btn-primary">Voltage control</a>
<a href ="javascript:show('2','loop');" id="btn-2" class="btn btn-loop"> + Phase resistance</a>
<a href ="javascript:show('1','loop');" id="btn-1" class="btn btn-loop"> + KV rating</a>
<a href ="javascript:show('0','loop');" id="btn-0" class="btn btn-loop"> + Phase inductance</a>

<div class="type type-b">
<img class="loop loop-v width70" src="extras/Images/torque_control/vc_b1c.png"/>
<img class="loop loop-0 width70 hide" src="extras/Images/torque_control/ec0_b.png"/>
<img  class="loop loop-1 width70 hide" src="extras/Images/torque_control/ec1_b.png"/>
<img  class="loop loop-2 width70 hide" src="extras/Images/torque_control/ec2_b.png"/>

</div>
<div class="type type-s hide">
<img class="loop loop-v width70" src="extras/Images/torque_control/vc_sc.png"/>
<img class="loop loop-0 width70 hide" src="extras/Images/torque_control/ec0_s.png"/>
<img  class="loop loop-1 width70 hide" src="extras/Images/torque_control/ec1_s.png"/>
<img  class="loop loop-2 width70 hide" src="extras/Images/torque_control/ec2_s.png"/>

</div>
<div class="type type-h hide">
<img class="loop loop-v width70" src="extras/Images/torque_control/vc_hc.png"/>
<img class="loop loop-0 width70 hide" src="extras/Images/torque_control/ec0_h.png"/>
<img  class="loop loop-1 width70 hide" src="extras/Images/torque_control/ec1_h.png"/>
<img  class="loop loop-2 width70 hide" src="extras/Images/torque_control/ec2_h.png"/>
</div>

| Method | Motor Parameters | Control Law | Benefit / Speed Range |
|--------|------------------|-------------|---------------------|
| **Pure Voltage** | None | $$u_q = U_{\text{user}}$$ | Entry Level: Simple DC-like control. Torque fades quickly as speed increases. |
| **+ Phase Resistance** |$$R$$ | $$u_q = i_q R$$ | Current Limiting: Safe torque control at stall/low speeds. Still speed-limited. |
| **+ Back-EMF** | $$R, KV$$ | $$u_q = i_q R + U_{\text{bemf}}$$ | Performance: Maintains constant torque over a wide speed range. |
| **+ Inductance** | $$R, KV, L$$ | $$u_q = i_q R + U_{\text{bemf}}$$, $$u_d = -i_q L v n_{pp}$$ | Maximum Speed: Compensates for phase lag. Reaches motor's physical top speed. |

### Motor Parameters

To use estimated current torque mode, you need to specify motor parameters:

- **Phase Resistance** ($$R$$): Electrical resistance between motor phases (in Ohms). Determines the voltage-to-current conversion at standstill. Typical values: Gimbal motors 3-10 Ω, Drone motors 0.05-1 Ω and Stepper motors 1-5 Ω depending on motor size.

[See a guide to manual mesuring phase resistance](phase_resistance){: .btn .btn-docs}

- **KV Rating** (in rpm/V): Motor speed per volt under no load. Higher KV = faster motor. Example: a 100 KV motor spins at 100 rpm per 1 Volt applied. If unknown, measure it: apply 1V in voltage mode and convert the observed speed ($$v$$ in rad/s) to rpm: $$KV = v \cdot 30/\pi$$.

[See a guide to measuring KV rating with SimpleFOC](kv_rating_measure){: .btn .btn-docs}

- **Phase Inductance** ($$L$$, in Henry): Inductive reactance that stores energy in the magnetic field. Determines the d-axis voltage needed to prevent lag at high speeds. Typical values: 0.1–10 mH. Usually, larger motors often have higher inductance. **This parameter has the least impact on the performance of the estimated current control, and it is only needed for high speed applications.** 

[See a guide to testing and tuning the inductance value](motor_params_test){: .btn .btn-docs}


### 💡 Phase resistance + KV are the "Sweet Spot"

If you start searching for motor parameters, focus on Phase Resistance ($$R$$) and KV Rating. Both are relatively easy to obtain (either from a datasheet, a simple multimeter check, or by using our measurement guides) and they provide a huge boost in performance compared to pure voltage mode.

While Inductance ($$L$$) is significantly more difficult to measure, it is also the least critical parameter, improving mostly the performance at very high speeds. 

For the vast majority of applications, the combination of $$R$$ and $$KV$$ is the true sweet spot for high-performance torque control.


### Setting Motor Parameters
You can specify the motor parameters either through the constructor, for example:

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> HybridStepper motors</a>

<div class="type type-b"  markdown="1">

```cpp
// BLDCMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V], q-axis inductance [H], d-axis inductance [H])
BLDCMotor motor = BLDCMotor( 11, 2.5, 120, 0.01, 0.01 );
``` 
</div>
<div class="type type-s hide"  markdown="1">  

```cpp
// StepperMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V], q-axis inductance [H], d-axis inductance [H])
StepperMotor motor = StepperMotor( 50, 1.5, 20, 0.01, 0.01 );
```
</div>
<div class="type type-h hide"  markdown="1">  

```cpp
// HybridStepperMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V], q-axis inductance [H], d-axis inductance [H])
HybridStepperMotor motor = HybridStepperMotor( 50, 1.0 , 20, 0.01, 0.01);
```
</div>
 
You can choose to set only some of the parameters (only pole pairs number is mandatory), for example just the phase resistance, or only phase resistance and KV rating, and the library will be able to estimate the current and compensate for the back-EMF voltage to a certain degree, but the performance of the torque control will be better if you provide all the parameters.

Alternatively, you can set the parameters after the motor object is created (even change them on the fly), for example:
```cpp
motor.phase_resistance = 1.5; // ex. 1.5 Ohms
motor.KV_rating = 120; // ex. 120 rpm/V
motor.axis_inductance.q = 0.01; // ex. 10 mH
motor.axis_inductance.d = 0.01; // ex. 10 mH
```

## Pure voltage control


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> HybridStepper motors</a>

<div class="type type-b">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/vc_b.png">
</div>
<div class="type type-s hide">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/vc_s.png">
</div>
<div class="type type-h hide">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/vc_h.png">
</div>


The voltage control algorithm reads the angle $$a$$ from the position sensor, gets the target $$u_q$$ from the user, and uses the FOC algorithm to set $$u_a$$, $$u_b$$, and $$u_c$$. The FOC algorithm ensures the magnetic force is aligned at a <i>90‑degree</i> offset from the rotor field, which yields maximal torque (commutation).

The assumption of pure voltage control is that the torque generated (proportional to current $$\tau = K_t i_q$$) is roughly proportional to the voltage $$u_q$$ at low speed. Maximal torque corresponds to the maximal $$u_q$$, limited by the available supply voltage. Minimal torque is at $$u_q=0$$.
The ideal torque generated by the motor would be proportional to the applied voltage $$u_q$$:

$$
\tau =  K_t\,i_q = \frac{K_t}{R}\,u_q
$$

But the real torque of the motor will be affected by the back‑EMF voltage generated by the motor rotation, which reduces the effective voltage and current. Therefore, at higher speeds, the torque will drop as the back‑EMF increases, leading to a maximum speed limit.

$$
\tau = K_t \cdot \left( \frac{u_q}{R} - \frac{K_e}{R} \cdot v \right)
$$


### Expected Behavior
- **At $$u_q = 0$$**: Motor does not move; high resistance (more than disconnected motor).
- **At $$u_q > 0$$ or $$u_q < 0$$**: Motor rotates; velocity proportional to $$u_q$$ (DC-like behavior).
   - At low speeds, torque is proportional to $$u_q$$.
   - Changing sign of $$u_q$$ reverses direction.


[See the API docs of the voltage torque mode](voltage_torque_mode){: .btn .btn-docs} [Read a bit more about the theory of voltage control](voltage_torque_control#voltage-mode-theory){: .btn .btn-docs}

<blockquote class="warning" markdown="1">
<p class="heading">⚠️ Practical limitations</p> 

**No current limiting**: This torque control approach is the fastest and simplest to set up, but it does not limit the current in any way.

**Be careful**: Setting a high voltage $$u_q$$ can result in a very high current $$i_q$$, which can damage your motor and driver. Always set your voltage $$u_q$$ to a safe value and adjust it to your motor and driver capabilities. 

[See more about the practical limitations of pure voltage control](voltage_torque_mode#configuration-and-torque-limits){: .btn .btn-docs}

</blockquote>


## Voltage control with current estimation

Block diagram of this torque control strategy is as follows


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> HybridStepper motors</a>

<div class="type type-b">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/ec2_b.png">
</div>
<div class="type type-s hide">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/ec2_s.png">
</div>
<div class="type type-h hide">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/ec2_h.png">
</div>

If the user provides the phase resistance $$R$$, when using `estimated current` mode, the library automatically calculates the appropriate voltage $$u_q$$ for the desired current $$i_q$$.

$$
u_q = i_q R 
$$

The ideal torque would then be proportional to the desired current $$i_q$$:

$$
\tau = K_t i_q 
$$

The real torque of the motor will be affected by the back‑EMF voltage generated by the motor rotation, which is not compesated in this method (as well as in the pure voltage control method), which reduces the effective voltage and current.

$$
\tau = K_t \cdot \left( i_q - \frac{K_e}{R} \cdot v \right)
$$

Therefore, appart from allowing the user to set the desired current $$i_q$$ directly, rather than the voltage $$u_q$$, this strategy suffers from all the same problems as the pure voltage control. 
The main issue being that, at higher speeds, the torque will drop as the back‑EMF increases, breaking the proportionality between the desired current $$i_q$$ and the torque $$\tau$$.

[Read a bit more about the estimated current control](voltage_torque_control#estimated-current-mode-theory){: .btn .btn-docs}

### Expected Behavior
- **At $$i_q = 0$$**: Motor does not move; has resistance (more than disconnected motor).
- **At $$i_q > 0$$ and $$i_q < 0$$**: Motor rotates; velocity proportional to $$i_q$$.
   - At low speeds, torque is proportional to $$i_q$$.
   - Changing sign of $$i_q$$ reverses direction.


<blockquote class="warning" markdown="1">

**If the motor does not behave as expected**, check the troubleshooting guide to make sure the motor parameters are set correctly.

[See the troubleshooting guide for motor parameters](motor_params_test){: .btn .btn-docs} 
</blockquote>


### Parameter configuration

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> HybridStepper motors</a>

This mode will be automatically enabled if you set the `torque_controller` to `estimated_current` and provide **only** the phase resistance parameter to the motor. You can specify the phase resistance either through the constructor, for example:

<div class="type type-b" markdown="1">

```cpp
// BLDCMotor(pole pair number, phase resistance)
BLDCMotor motor = BLDCMotor( 11, 2.5 );
```

</div>
<div class="type type-s hide"  markdown="1">

```cpp
// StepperMotor(pole pair number, phase resistance)
StepperMotor motor = StepperMotor( 50, 1.0 );
```
</div>


<div class="type type-h hide"  markdown="1">

```cpp
// HybridStepperMotor(pole pair number, phase resistance)
HybridStepperMotor motor = HybridStepperMotor( 50, 1.0 );
```
</div>

Or by setting the parameter:
```cpp
motor.phase_resistance = 1.5; // ex. 1.5 Ohms
```

## Voltage control with current estimation and Back-EMF compensation

Block diagram of this torque control strategy is as follows


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> HybridStepper motors</a>

<div class="type type-b">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/ec1_b.png">
</div>
<div class="type type-s hide">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/ec1_s.png">
</div>
<div class="type type-h hide">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/ec1_h.png">
</div>

If the user provides the phase resistance $$R$$ and the motor's $$KV$$ rating, the library automatically calculates the appropriate voltage $$u_q$$ for the desired current $$i_q$$ while compensating for the back‑EMF voltage generated by the motor rotation

$$
u_q = i_q R + U_{bemf}= i_q R + K_e\,v \quad \text{where} \quad K_e = \frac{30}{\pi }\frac{1}{k_{factor} KV}, \quad k_{factor} = \begin{cases} \sqrt{3} & \text{BLDC motors} \\ \sqrt{2} & \text{Stepper motors} \end{cases}
$$

As the back‑EMF voltage is compensated, the torque generated by the motor will be proportional to the desired current $$i_q$$ over a wide speed range, as long as the motor parameters are correct (enough 😄).

$$
\tau = K_t i_q 
$$

Therefore this is the minimal configuration for the estimated current control mode that allows for accurate torque control over a wide speed range, as the back‑EMF compensation allows to maintain the proportionality between the desired current $$i_q$$ and the generated torque $$\tau$$ even at higher speeds. And it is one that is suggested for most applications if current sensing is not available, as it provides a good balance between performance and ease of configuration. 

[Read a bit more about the estimated current control](voltage_torque_control#estimated-current-mode-theory){: .btn .btn-docs}

### Expected Behavior
- **At $$i_q = 0$$**: Motor has very low resistance; feels almost disconnected - much less resistantance than in voltage mode.
- **At $$i_q > 0$$ or $$i_q < 0$$**: Motor accelerates; reaching maximum velocity
   - Changing sign of $$i_q$$ reverses direction.
   - Higher $$i_q$$ values result in faster acceleration.
   - Motor reaches the same maximum velocity regardless of $$i_q$$.

<blockquote class="warning" markdown="1">

**If the motor does not behave as expected**, check the troubleshooting guide to make sure the motor parameters are set correctly.

[See the troubleshooting guide for motor parameters](motor_params_test){: .btn .btn-docs} 
</blockquote>


### Parameter configuration

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> HybridStepper motors</a>

This mode will be automatically enabled if you set the `torque_controller` to `estimated_current` and provide the phase resistance parameter and KV rating (but not the inductance) to the motor. You can specify the phase resistance either through the constructor, for example:


<div class="type type-b"  markdown="1">

```cpp
// BLDCMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V])
BLDCMotor motor = BLDCMotor( 11, 2.5, 120 );
```

</div>
<div class="type type-s hide"  markdown="1">

```cpp
// StepperMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V])
StepperMotor motor = StepperMotor( 50, 1.5, 20 );
```

</div>


<div class="type type-h hide"  markdown="1">

```cpp
// HybridStepperMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V])
HybridStepperMotor motor = HybridStepperMotor( 50, 1.0 , 20);
```
</div>

Or by setting the parameter:

```cpp
motor.phase_resistance = 1.5; // ex. 1.5 Ohms
motor.KV_rating = 120; // ex. 120 rpm/V
```

## Voltage control using current estimation with Back-EMF and lag compensation

Block diagram of this torque control strategy is as follows

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> HybridStepper motors</a>

<div class="type type-b">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/ec0_b.png">
</div>
<div class="type type-s hide">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/ec0_s.png">
</div>
<div class="type type-h hide">
<a name="foc_image"></a><img class="width60" src="extras/Images/torque_control/ec0_h.png">
</div>

As in the previous method, if the user provides the phase resistance $$R$$ and the $$KV$$ rating, the library automatically calculates the appropriate voltage $$u_q$$ for the desired current $$i_q$$ while compensating for the back‑EMF voltage generated by the motor rotation

$$
u_q = i_q R + U_{bemf}= i_q R + \frac{v}{KV}
$$

Additionally, if the user sets the q-axis inductance value $$L_q$$, the library will be able to compensate for the lag of the torque vector by calculating an appropriate d-axis voltage $$u_d$$

$$
u_d = -i_q L_q v n_{pp}
$$

where $$n_{pp}$$ is the number of motor's pole pairs. By compensating the lag of the torque vector due to the motor rotation velocity $$v$$, the motor will be able to spin with higher max velocity. Therefore the lag compensation will have the most effect if application requires going to the maximal motor velocity.

[Read a bit more about the estimated current control](voltage_torque_control#estimated-current-mode-theory){: .btn .btn-docs}


### Expected Behavior
- **At $$i_q = 0$$**: Motor has very low resistance; feels almost disconnected.
- **At $$i_q > 0$$ or $$i_q < 0$$**: Motor accelerates; reaches maximum velocity regardless of load.
  - Changing sign of $$i_q$$ reverses direction.
  - Higher $$i_q$$ values result in faster acceleration.
  - Motor reaches the same maximum velocity regardless $$i_q$$.
- **Max speeds**: Higher than in previous methods, especially with load, due to lag compensation.

<blockquote class="warning" markdown="1">

**If the motor does not behave as expected**, check the troubleshooting guide to make sure the motor parameters are set correctly.

[See the troubleshooting guide for motor parameters](motor_params_test){: .btn .btn-docs} 
</blockquote>

### Parameter configuration

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> HybridStepper motors</a>

This mode will be automatically enabled if you set the `torque_controller` to `estimated_current` and provide the phase resistance parameter, KV rating and the inductance to the motor. You can specify the phase resistance either through the constructor, for example:

<div class="type type-b"  markdown="1">


```cpp
// BLDCMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V], q-axis inductance [H], d-axis inductance [H])
BLDCMotor motor = BLDCMotor( 11, 2.5, 120, 0.01, 0.01 );
```

</div>
<div class="type type-s hide"  markdown="1">

```cpp
// StepperMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V], q-axis inductance [H], d-axis inductance [H])
StepperMotor motor = StepperMotor( 50, 1.5, 20, 0.01, 0.01 );
```

</div>

<div class="type type-h hide"  markdown="1">

```cpp
// HybridStepperMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V], q-axis inductance [H], d-axis inductance [H])
HybridStepperMotor motor = HybridStepperMotor( 50, 1.5, 20, 0.01, 0.01 );
```
</div>

Or by setting the parameter:

```cpp
motor.phase_resistance = 1.5; // ex. 1.5 Ohms
motor.KV_rating = 120; // ex. 120 rpm/V
motor.axis_inductance.q = 0.01; // ex. 0.01 H
```

For more info about the theory of the torque control check the [Digging deeper](digging_deeper) section or go directly to [torque control theory](voltage_torque_control).

## Targets, Limits and Feed-forward

The estimated current torque control mode allows you to set the target current $$i_q$$ directly, which is proportional to the torque generated by the motor. The current is specified in Amperes and is communicated tot he torque control loop from the motion control loop through the `current_sp` variable of the motor object, for example:

```cpp
motor.current_sp = 0.5; // set target current to 0.5 A
```
This varaible is set internally in the `FOCMotor` object and should not be modified by the user. If one wants to control the torque of the motor, they should use the motion control mode `torque` and set the `target` variable.

```cpp
motor.torque_controller = TorqueControlType::estimated_current;
motor.controller = MotionControlType::torque;
motor.target = 0.5; // set target torque to 0.5 Amp
```

This current contol mode aloows the user to specify the hard limits for the d and q-axis currents, which will be applied by the torque control loop. For example, if you want to limit the current to 1 A, you can set:

```cpp
motor.current_limit = 1.0; // set current limit to 1 A
```

as well as for the d and q-axis voltages to prevent the controller from applying too high voltages to the motor:

```cpp
motor.voltage_limit = 6.0; // set voltage limit to 6 V
```

The prefered way of setting these values is using the setter functions
```cpp
motor.updateCurrentLimit(1.0); // set current limit to 1 A
motor.updateVoltageLimit(6.0); // set voltage limit to 6 V
```

The library additionally allows the user to specify a feed-forward voltage and current terms for both d and q-axis, which will be added to the control law. This is an advanced feature that can be used to improve the performance of the torque control in some applications, for example to compensate for the gravity load in a robotic arm. The feed-forward terms can be set as follows:

```cpp
motor.feed_forward_voltage.q = 0.5; // set q-axis voltage feed-forward to 0.5 V
motor.feed_forward_voltage.d = 0.2; // set d-axis voltage feed-forward to 0.2 V

motor.feed_forward_current.q = 0.1; // set q-axis current feed-forward to 0.1 A
motor.feed_forward_current.d = 0.05; // set d-axis current feed-forward to 0.05 A
```

## Torque control example code
A simple example of the voltage based torque control and setting the target **current** by serial command interface. 

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> Hybrid Stepper motors</a>

<div class="type type-b" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor & driver instance
// BLDCMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V], q-axis inductance [H], d-axis inductance [H])
BLDCMotor motor = BLDCMotor(11, 2.5, 90, 0.001, 0.001);
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// encoder instance
Encoder encoder = Encoder(2, 3, 500);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link driver
  motor.linkDriver(&driver);

  // set the torque control type
  motor.torque_controller = TorqueControlType::estimated_current;
  // set motion control loop to be used
  motor.controller = MotionControlType::torque;

  // use monitoring with serial 
  Serial.begin(115200);
  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  motor.init();
  // align sensor and start FOC
  motor.initFOC();

  // add target command M
  command.add('M', doMotor, "motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target current using serial terminal:"));
  _delay(1000);
}

void loop() {

  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // user communication
  command.run();
}
```


</div>

<div class="type type-s hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// Stepper motor & driver instance
// StepperMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V], q-axis inductance [H], d-axis inductance [H])
StepperMotor motor = StepperMotor(50, 1.0, 40, 0.001, 0.001);
StepperDriver2PWM driver = StepperDriver2PWM(9, 5, 6, 8);

// encoder instance
Encoder encoder = Encoder(2, 3, 500);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link driver
  motor.linkDriver(&driver);

  // set the torque control type
  motor.phase_resistance = 1.5; // 1.5 Ohms
  motor.torque_controller = TorqueControlType::estimated_current;
  // set motion control loop to be used
  motor.controller = MotionControlType::torque;

  // use monitoring with serial 
  Serial.begin(115200);
  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  motor.init();
  // align sensor and start FOC
  motor.initFOC();

  // add target command M
  command.add('M', doMotor, "motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target current using serial terminal:"));
  _delay(1000);
}

void loop() {

  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // user communication
  command.run();
}
```


</div>


<div class="type type-h hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// Stepper motor & driver instance
// HybridStepperMotor(pole pair number, phase resistance [Ohms], KV rating [rpm/V], q-axis inductance [H], d-axis inductance [H])
HybridStepperMotor motor = HybridStepperMotor(50, 1.0, 40, 0.001, 0.001);
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// encoder instance
Encoder encoder = Encoder(2, 3, 500);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link driver
  motor.linkDriver(&driver);

  // set the torque control type
  motor.torque_controller = TorqueControlType::estimated_current;
  // set motion control loop to be used
  motor.controller = MotionControlType::torque;

  // use monitoring with serial 
  Serial.begin(115200);
  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  motor.init();
  // align sensor and start FOC
  motor.initFOC();

  // add target command M
  command.add('M', doMotor, "motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target current using serial terminal:"));
  _delay(1000);
}

void loop() {

  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // user communication
  command.run();
}
```


</div>
