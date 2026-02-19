---
layout: default
title: Motors
nav_order: 1
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /motors
parent: Supported Hardware
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: true
has_toc: false
---

# Supported motors

<div class="width60">
<img src="extras/Images/mot2.jpg" style="width:20%;display:inline"><img src="extras/Images/bigger.jpg" style="width:20%;display:inline"><img src="extras/Images/mot.jpg" style="width:20%;display:inline"><img src="extras/Images/nema17_2.jpg" style="width:20%;display:inline"><img src="extras/Images/nema17_1.jpg" style="width:20%;display:inline">
</div>

Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> supports two types of BLDC motors:

- [BLDC motors <i class="fa fa-external-link"></i>](bldc_motors) 
  - 3 phase (3 wire):
  - Gimbal and High-performance BLDC motors
- [Stepper motors <i class="fa fa-external-link"></i>](stepper_motors) 
  - 2 phase (4 wire)

## 📢 Critical: Read before powering up!
Before running any motor, you must ensure your hardware can handle the stall current. FOC can easily pull more current than a driver can handle if not limited in software.

**The Worst-Case Calculation**
Check your motor's phase resistance ($$R$$) and your power supply voltage ($$V_{DC}$$). The maximum possible current ($$I_{max}$$) is:

$$I_{max} = \frac{V_{DC}}{R}$$

**Safety Steps:**
- **Compare**: If $$I_{max}$$ exceeds your driver's rating, you must limit the voltage in software.
- **Software Limit**: Use `driver.voltage_limit` and (`motor.voltage_limit` and/or `motor.current_limit`) to stay within safe bounds. - [see motor docs](motors_config)
- **Power Supply**: If you can, use a current-limited lab bench power supply for your initial tests.
