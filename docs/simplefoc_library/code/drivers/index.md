---
layout: default
title: Driver code
nav_order: 3
parent: Writing the Code
permalink: /drivers_config
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: True
has_toc: False
---

# Driver configuration

<div class="width60">
<img src="extras/Images/drv8302.png" style="width:25%;display:inline"><img src="extras/Images/bgc_30.jpg" style="width:25%;display:inline"><img src="extras/Images/l6234.jpg" style="width:25%;display:inline"><img src="extras/Images/l298n.jpg" style="width:25%;display:inline">
</div>

Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> supports BLDC and stepper motor drivers:

- [BLDC driver <i class="fa fa-external-link"></i>](bldcdriver) (BLDC or Hybrid stepper motors)
    - **3 PWM signals** ( 3 phase ) - `BLDCDriver3PWM`
    - **6 PWM signals** ( 3 phase ) - `BLDCDriver6PWM`
- [Stepper drivers <i class="fa fa-external-link"></i>](stepperdriver) (Stepper motors)
    - **4 PWM signals** ( 2 phase )  - `StepperDriver4PWM`
    - **2 PWM signals** ( 2 phase )  - `StepperDriver2PWM`

    
The driver code is written in a way to support as many different drivers out there as possible and in a way to be fully interchangeable. 
These classes can be used as stand-alone classes and they can be used to set certain PWM value to the driver outputs, see example codes in `utils > driver_standalone_test`.


## Driver support per MCU architecture
    
MCU | 2 PWM mode | 4 PWM mode | 3 PWM mode | 6 PWM mode | pwm frequency config 
--- | --- |--- |--- |--- |--- 
Arduino AVR (8-bit) | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ (either 4kHz or 32kHz)
Arduino DUE  | ✔️ | ✔️ | ✔️ | ❌ | ✔️
stm32 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
esp32 `MCPWM` | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ 
esp32 `LEDC`| ✔️ | ✔️ | ✔️ |  ✔️ | ✔️ 
esp8266 | ✔️ | ✔️ | ✔️ | ❌ | ✔️ 
samd21/51 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ 
teensy3 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ 
teensy4 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ 
Raspberry Pi Pico | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ 
Portenta H7 | ✔️ | ✔️ | ✔️ | ❌ | ✔️ 
Renesas (UNO R4 Minima) | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
nRF52 |✔️ | ✔️ | ✔️ | ✔️ | ✔️
Arduino Nano Matter (📢NEW)  | ✔️ | ✔️ | ✔️ | ✔️ | ✔️

<blockquote class="info"> 📢 Here is a quick guide to choosing appropriate PWM pins for different MCU architectures <a href="choosing_pwm_pins">see in docs</a>.</blockquote>