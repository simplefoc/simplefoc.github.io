---
layout: default
title: Open-Loop Efficiency Considerations
nav_order: 8
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /openloop_efficiency
parent: Practical guides
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---


# Open-loop efficiency considerations


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b width60" src="extras/Images/olb.png"/>
<img class="type type-s width60 hide" src="extras/Images/ols.png"/>


Open-loop motion is a simple but inefficient control strategy. However its efficiency and characteristics depend a lot on the [torque control mode](torque_control) you choose to use. 

## Voltage control mode

[See more about voltage control mode](voltage_torque_mode){: .btn .btn-docs}

When using the voltage control mode, the velocity open-loop control will set the voltage to the motor equal to the `motor.voltage_limit` ($$U_{limit}$$)

$$
U_{limit}  \quad [Volts]
$$

There are a couple big reasons why this is inefficient. 
- It does not take in consideration the phase resistance of the motor: the same voltage can produce very different currents on different motors, making some motors heat much more than others.
  - Gimbal motors have phase resistance from 5 to 15 Ohms, so the voltage limits of 5-10 Volts can produce currents of 0.5-2 Amps, which is a good range for these motors.
  - Drone motors have phase resistance from 0.05 to 0.2 Ohms, so the voltage limits of 5-10 Volts can produce currents of >25 Amps, which is way too much for these motors and it can damage them or the driver. For drone motors, the voltage limits should stay very low, under 1 Volt.

$$ I = \frac{U_{limit}}{R_{phase}} \quad [Amps]$$

<blockquote class="info" markdown="1"> <p class="heading">RULE OF THUMB</p>

- For gimbal motors, use voltage limits of 3-10 Volts. 
- For drone motors, keep voltage limits under 1 Volt.

</blockquote>
- It does not take in consideration the back-EMF voltage that is produced by the motor when it is spinning. 
  - The faster the motor is spinning, the higher the back-EMF voltage is and the less torque the motor is going to produce for the same voltage limit. This means that the motor is going to be very weak at higher velocities, and it is not going to be able to reach the target velocity if the load is too high.
  - Ideally, the user should increase the voltage limit as the velocity increases in order to compensate for the back-EMF voltage, but this is dangerous and it can easily damage the motor or the driver. Much better option is using [estimated current mode](#estimated-current-mode).

$$ I = \frac{U_{limit} - K_{BEMF}\cdot v}{R_{phase}} \quad [Amps]$$


<blockquote class="info" markdown="1"> <p class="heading">RULE OF THUMB</p>

- For gimbal motors and low velocity applications, voltage control mode is ok.
- For everything else use [estimated current mode](#estimated-current-mode)

</blockquote>

## Estimated current mode

[See more about current control mode](estimated_current_mode){: .btn .btn-docs}

When using the estimated current control mode, the velocity open-loop control will set the current to the motor equal to the `motor.current_limit` ($$I_{limit}$$). This is more efficient as it takes into account the phase resistance of the motor and it is going to apply the voltage that is needed to achieve the target current. If the user provides the KV rating and the axis inductance (optional), this control mode will compensate for the back-EMF voltage as well, making it much more efficient both at high and low velocities. 


| Parameter | Too high  | Too low | 
|-----------|-----------|---------|
| Phase resistance | Can cause the motor to heat up a lot and it can damage the motor or the driver. | The motor can be very weak and it can fail to reach the target velocity. |
| KV rating | Can cause the motor to be very strong at lower velocities, which can cause it to heat up a lot and it can damage the motor or the driver. | The motor can be very weak at higher velocities because the library is going to underestimate the back-EMF voltage and it is not going to apply enough voltage to the motor. |

If motor parameters are not too complicated to find this control mode is recommended over voltage control mode for most applications, especially for drone motors and high velocity applications.

If motor parameters are not known or if current sensing available, the recommended option is to use [FOC current mode](#foc-current-mode) which measures the current directly and does not estimate it based on the motor parameters, making it more robust and efficient control strategy.

## FOC current mode

[See more about FOC current mode](foc_current_torque_mode){: .btn .btn-docs}

When using the FOC and DC current control modes, the velocity open-loop control will set the current to the motor equal to the `motor.current_limit` ($$I_{limit}$$) and it is going to apply the voltage that is needed to achieve the target current. This is the most efficient control mode as it measures the current directly and it does not estimate it based on the motor parameters, making it more robust and efficient control strategy. It is recommended for all applications where current sensing is available, even if the motor parameters are not known.


## Which Torque mode should I use?

**RULE OF THUMB**

1. If you have current sensing available, use **FOC current** control mode, it is the most efficient and robust control mode.
2. If you don't have current sensing available, use **estimated current** control mode if you know the motor parameters, it is much more efficient than voltage control mode.
3. If you don't have current sensing available and you don't know the motor parameters, use **voltage control** mode, but be careful with the voltage limits



Torque Mode |  Best For... | Requirements | Limits
--- | --- | --- | ---
Voltage Control | **Beginners & Gimbal motors.** Best for low-speed testing where you don't know the motor parameters yet. | **None** | Voltage `motor.voltage_limit` 
Estimated Current | **Drone & High-KV motors.** Much safer for low-resistance motors; provides back-EMF compensation. | ***Phase resistance*** ($$R$$) and ***KV rating*** ($$KV$$) | Current `motor.current_limit`
FOC Current | **High Performance.** Most efficient and robust mode; handles variable loads and speeds perfectly. | **Current Sensing** hardware. | Current `motor.current_limit`