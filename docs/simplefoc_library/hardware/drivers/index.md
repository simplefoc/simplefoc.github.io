---
layout: default
title: Drivers
parent: Supported Hardware
nav_order: 2
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /drivers
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: true
has_toc: false
---

# Supported driver boards

<div class="width60">
<img src="extras/Images/drv8302.png" style="width:25%;display:inline"><img src="extras/Images/bgc_30.jpg" style="width:25%;display:inline"><img src="extras/Images/l6234.jpg" style="width:25%;display:inline"><img src="extras/Images/l298n.jpg" style="width:25%;display:inline">
</div>

Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> has a goal to support as many BLDC and stepper motor drivers as possible. Till this moment there are two kinds of motor drivers supported by this library:

- [BLDC motor driver <i class="fa fa-external-link"></i>](bldc_drivers)
    - **3 PWM signals** ( 3 phase )
    - **6 PWM signals** ( 3 phase )
    - gimbal motor drivers or high-performance boards
- [Stepper drivers <i class="fa fa-external-link"></i>](stepper_drivers)
    - **4 PWM signals** ( 2 phase )
    - **2 PWM signals + 2 direction signals** ( 2 phase )
    - Stepper drivers or double DC motor drivers

## Choosing the Appropriate Driver

Selecting the right hardware is a balance of motor type, power requirements, and the level of control precision you need.

### 1. Motor Type
- **BLDC Motors**: Requires a [BLDC driver](bldc_drivers). Ensure the current and voltage ratings match your motor's specs.
    - <blockquote class="warning" markdown="1">⚠️ **No Drone ESCs**
    Traditional drone ESCs are not suitable for FOC because they typically use a fixed commutation scheme and lack the necessary control interfaces.
    </blockquote>
- **Stepper Motors**:
    - **Dedicated [Stepper Driver](stepper_drivers)**: Best for 2-phase motors within standard current ranges (ex.NEMA17)
    - **Hybrid Mode**: You can actually use any [BLDC driver](bldc_drivers) to run a stepper motor. - [See example](stepper_control_shield)
    - <blockquote class="warning" markdown="1">⚠️ **No Step/Dir Drivers**
    Traditional "EasyDrivers" or A4988s (in step/dir mode) cannot be used for FOC because they do not allow direct phase voltage control.
    </blockquote>
    
### 2. Current Requirements
- **Low Current (< 5A)**: Integrated driver ICs are cost-effective and easy to use. 
    - Examples: L6234, DRV8313, DRV8316.
- **High Current (> 5A)**: Requires discrete MOSFET-based designs with robust thermal management (heatsinks/fans). 
    - Examples: DRV8302, VESC-based hardware.
- **Rule of Thumb**: Always select a driver with a current rating at least 20-30% higher than your motor's expected continuous current.


## 📢 Critical: Read before powering up!
Before running any motor, you must ensure your hardware can handle the stall current. FOC can easily pull more current than a driver can handle if not limited in software.

**The Worst-Case Calculation**
Check your motor's phase resistance ($$R$$) and your power supply voltage ($$V_{DC}$$). The maximum possible current ($$I_{max}$$) is:

$$I_{max} = \frac{V_{DC}}{R}$$

**Safety Steps:**
- **Compare**: If $$I_{max}$$ exceeds your driver's rating, you must limit the voltage in software.
- **Software Limit**: Use `driver.voltage_limit` and (`motor.voltage_limit` and/or `motor.current_limit`) to stay within safe bounds. - [see motor docs](motors_config)
- **Power Supply**: If you can, use a current-limited lab bench power supply for your initial tests.
