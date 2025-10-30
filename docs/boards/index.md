---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "SimpleFOC boards"
nav_order: 2
permalink: /boards
has_children: true
has_toc: false
toc: true
---

# <span class="simple">Simple<span class="foc">FOC</span> Boards</span>

One of the goals of the  <span class="simple">Simple<span class="foc">FOC</span>project</span> is to develop low-cost easy to use BLDC driver boards compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>and completely open source! Therefore, <span class="simple">Simple<span class="foc">FOC</span></span> team members have developed a set of boards, designed specifically for ease of use, to help you kickstart your FOC journey. In addition to being easy to use, the goal of these boards is serve as a reference design for the community to build upon. And finally, even though some of these boards are available in our [shop](https://www.simplefoc.com/shop), our docs provide a lot of documentation and step-by-step guides on how to fabricate the boards yourself.


There two main formats of the official drivers boards developed by the <span class="simple">Simple<span class="foc">FOC</span></span> team:

- <span class="simple">Shield</span> form factor: These boards are designed to be compatible with the Arduino ecosystem and are intended to be used with the <span class="simple">Simple<span class="foc">FOC</span>library</span> and the Arduino IDE. They are designed to be easy to use and are intended for low to mid power applications.
   - <span class="simple">Simple<span class="foc">FOC</span>Shield</span> - <small>[Find out more](arduino_simplefoc_shield_showcase)</small> 
   - <span class="simple">Simple<span class="foc">FOC</span> <b>Power</b>Shield</span> - <small>⚠️<i>( development abandoned )</i></small> - <small>[Find out more](#simplefoc-powershield-v02-️-development-abandoned-)</small>
   - 📢**NEW**: <span class="simple">Simple<span class="foc">FOC</span><b>Drive</b></span>  - <small>[Find out more](boards#simplefoc-drive-v10---find-out-more)</small>

- <span class="simple">Mini</span> form factor: These boards are designed to be small, low-cost, and easy to use. They are intended for low power applications and are designed to be compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>.
   - <span class="simple">Simple<span class="foc">FOC</span>Mini</span> - <small>[Find out more](simplefocmini)</small> 
   - <span class="simple">Simple<span class="foc">FOC</span> <b>Step</b>Mini</span>  - <small> [Find out more](#simplefoc-stepmini-v10---see-on-github)


In addition to the official boards, there are many other boards compatible with <span class="simple">Simple<span class="foc">FOC</span>library</span>  that you can explore, see the [docs](supported_hardware). Additionally, some other cool hardware designs have been proposed by the community. Check out our [community forum](https://community.simplefoc.com/) for more info.



## Boards in the <span class="simple">Shield</span> form factor

These boards are designed to be compatible with the Arduino UNO R3 headers, enabling an easy to start experience with the <span class="simple">Simple<span class="foc">FOC</span>library</span> and the Arduino IDE. The boards can be used with any board with the standard Arduino headers, such as the Arduino MEGA, STM32 Nucleo boards, Adafruit Metro, ESP32 D1 R3, Arduino UNO R4 and many others. This format enables users to easily exchange the microcontrollers and find the best solution for their application. The boards are fully open-source and the fabrication files are available in the respective repositories, as well as detailed guides on how to fabricate the boards yourself. The boards are additionally available in the [shop](https://www.simplefoc.com/shop) for those who prefer to buy them, as well as on other mainstream platforms like Aliexpress and Ebay (no relation to the <span class="simple">Simple<span class="foc">FOC</span>project</span>).


### <span class="simple">Simple<span class="foc">FOC</span>Shield</span> <small>v3.2</small> - <small>[Find out more](arduino_simplefoc_shield_showcase)</small>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/arduino-simplefocshield)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/arduino-simplefocshield?color=blue)

This is an open-source low-cost Brushless DC (BLDC) motor driver board intended primarily for low-power FOC applications up to 5Amps. The board is fully compatible with the Arduino UNO and all the boards with the standard Arduino headers.
The <span class="simple">Simple<span class="foc">FOC</span>Shield</span>, in combination with  the <span class="simple">Simple<span class="foc">FOC</span>library</span> provides *user-friendly* way to control BLDC motors both in hardware and software.  

<div class="width40">
<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/master/images/top.png"/>
</div>


### Features
{: .no_toc }
- **Plug & play**: In combination with Arduino *Simple**FOC**library* - [github](https://github.com/simplefoc/Arduino-FOC)
- **Low-cost**: Price of 15-30€ - [Check the pricing](https://www.simplefoc.com/shop) 
- **In-line current sensing**: Up to 5Amps bidirectional
   - ACS712 hall current sensor
- **Integrated 8V regulator**: 
   - Enable/disable by soldering pads
- **Absolute max ratings** - Designed for Gimbal motors with the internal resistance >10 Ωs. 
   - Max current: 3A, 
   - Max input voltage: 35V
- **Stackable**: running 2 motors in the same time
- **Encoder/Hall sensors interface**: Integrated 3.3kΩ pullups (configurable)
- **I2C interface**: Integrated 4.7kΩ pullups (configurable)
- **Configurable pinout**: Hardware configuration - soldering connections
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo boards...
- **Open Source**: 
   - Fully designed in **EasyEDA**: [EasyEDA project](https://oshwlab.com/the.skuric/simplefocshield_copy_copy) 🎉
   - Fully available fabrication files - [how to make it yourself](https://docs.simplefoc.com/arduino_simplefoc_shield_fabrication)

<blockquote class="info">
📢<b>NEWS</b>: <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v3.2 is now out!
</blockquote>



### <span class="simple">Simple<span class="foc">FOC</span> <b>Power</b>Shield</span> <small>v0.2</small> <small>⚠️<i>( development abandoned )</i></small>

A powerful arduino shield for running BLDC motors using the FOC algorithm. This board is based on the [BTN8982](https://www.infineon.com/dgdl/Infineon-BTN8982TA-DS-v01_00-EN.pdf?fileId=db3a30433fa9412f013fbe32289b7c17) half bridges and can support currents up to 30 Amps continuos and 50Amps peak. Making it a board that can run virtually any BLDC motor.


<div class="width40">
<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOC-PowerShield/main/images/top.png"/>
</div>

<blockquote class="warning" markdown="1">
<p class="heading"> ⚠️ BEWARE: BTN8982/IFX007T performance issues</p>
BTN8982 and IFX007T drivers have been designed for DC motors and are based on old H-bridge technology. They have very long mosfet rise time (multiple microseconds) which in many cases presents a considerable part of the PWM duty cycle. When running the BLDC motors, precise PWM duty cycle setting is crucial for smooth and efficient operation. Therefore these drivers will not be able to provide very smooth operation on high frequency PWM (above 15kHz).  Read more about it in the community thread: [link](https://community.simplefoc.com/t/simplefoc-powershield/582).<br>
This performance constraint is the main reason why the <span class="simple">Simple<span class="foc">FOC</span> <b>Power</b>Shield</span> project has for now been put on hold, and although these boards are available through Aliexpress and some other platforms, they will not be available through simplefoc.com. 
<br><br>

This does not mean that the board itself is not functional or that it will not work in your project though. It is still one of the cheapest (simplest) solutions out there for mid to high power BLDC control and with proper tuning of control loops you will still be able to get some good results with it. 
</blockquote>

### Features
{: .no_toc }
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **Low-cost**: Fabrication price under €25/pcs - **⚠️ will not be sold by simplefoc.com**
- **High-side current sensing**: - not yet supported by *Simple**FOC**library*
- **In-line current sensing**: - supported by *Simple**FOC**library*
- **Max power <500W**: max current 30A, power-supply 24V
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo boards, Arduino DUE...
- **Small size**: 53mm x 60mm
- **Encoder/Hall sensors interface**: Integrated 3.3kΩ pullups (configurable)
- **Open Source**: 
   - Fully available fabrication files  
        - If never done it before, see a similar guide for *Simple**FOC**shield*:  [how to make it yourself](https://docs.simplefoc.com/arduino_simplefoc_shield_fabrication)
   - Altium project
   - 3d model
   - schematics

Read more about this board at [link](https://github.com/simplefoc/Arduino-SimpleFOC-PowerShield)

### <span class="simple">Simple<span class="foc">FOC</span> <b>Drive</b></span> <small>v1.0</small> - <small>[Find out more](https://github.com/simplefoc/SimpleFOC-DriveShield)</small>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/SimpleFOC-DriveShield)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/SimpleFOC-DriveShield?color=blue)

<div class="width40">
<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/refs/heads/main/images/top.jpg" />
</div>

This is an open-source low-cost BLDC driver boards in the form of a Arduino shield. It is a part of the SimpleFOC project. The board is the big brother of the SimpleFOCShield and is designed to drive motors with higher current requirements, up to 30Amps. The board is created with the same philosophy as the SimpleFOCShield - to be simple to use, low-cost, and open-source and fully compatible with the SimpleFOClibrary.

Additionally the aim of the board is to serve as a template project for the community to build their own motor drivers.

- The board is relatively simple and can be easily modified to fit different requirements.
- The board is designed in EasyEDA and all the fabrication files are available for download

### Features
{: .no_toc }

- **Boards absolute max ratings**
  - Max current: 20A continuous (peak 30A - measured)
  - Max input voltage: 30V
- **Stackable**: running 2 motors in the same time
- **Encoder/Hall sensors interface**: Integrated 3.3kΩ pullups (configurable)
- **I2C interface**: Integrated 4.7kΩ pullups (configurable)
- **Configurable pinout**: Hardware configuration - soldering connections
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo boards...
- **Open Source**:
  - Fully designed in EasyEDA: [EasyEDA project](https://oshwlab.com/the.skuric/SimpleFOC-Drive)
  - Fully available on github: [GitHub project](https://github.com/simplefoc/SimpleFOC-DriveShield)
- **Low-cost**: Estimated price of 25-40€ - Will be available in the SimpleFOC shop


## Boards in the <span class="simple">Mini</span> form factor

This is a set of miniature boards designed to be small, low-cost, and easy to use. They are intended for low power applications and are designed to be compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>. The boards are created as minimal working examples and are intended to be used as a reference design for the community to build upon. The boards are fully open-source and the fabrication files are available in the respective repositories, as well as detailed guides on how to fabricate the boards yourself. The boards are additionally available in the [shop](https://www.simplefoc.com/shop) for those who prefer to buy them, as well as on other mainstream platforms like Aliexpress and Ebay (no relation to the <span class="simple">Simple<span class="foc">FOC</span>project</span>).

<div class="width40 inline_block_top" markdown="1">
### <span class="simple">Simple<span class="foc">FOC</span>Mini</span> <small>v1.1</small> - <small>[Find out more](simplefocmini)</small>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/simplefocmini)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/simplefocmini?color=blue)

Small package, low-cost BLDC driver board fully compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>


<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" class="img200"/>




### Features
{: .no_toc }
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **DRV8313 based** - [datasheet](https://www.ti.com/lit/ds/symlink/drv8313.pdf?ts=1650461862269&ref_url=https%253A%252F%252Fwww.google.com%252F)
  - Power supply: 8-35V
  - Max current: 2.5A per phase
  - Onboard 3.3V LDO
      - up to 10mA 
      - Can power a sensor like AS5600 or CUI AMT102 
- **Small size**: 26x21 mm
- **Fully open-source**:
  - [EasyEDA](https://easyeda.com/the.skuric/simplefocmini)
  - [GitHub](https://github.com/simplefoc/SimpleFOCMini) 
- **Low-cost**: 
   - JLCPCB production cost ~3-5€
   - Available in the [shop](https://www.simplefoc.com/shop): 7-15€ 

Read more about this board at [link](https://github.com/simplefoc/SimpleFOCMini)


</div><div class="width40 inline_block_top" style  markdown="1">

### <span class="simple">Simple<span class="foc">FOC</span> <b>Step</b>Mini</span> <small>v1.0</small> - <small>[See on Github](https://github.com/simplefoc/SimpleFOC-StepMini)</small>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/simplefoc-stepmini)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/simplefoc-stepmini?color=blue)

Small package, low-cost Stepper driver board fully compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-StepMini/main/docs/top.png" class="img200"/>


### Features
{: .no_toc }
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **DRV8844 based** - [datasheet](https://www.ti.com/lit/ds/symlink/drv8844.pdf)
    - Power supply: 8-35V
    - Max current: 2.5A per phase
    - Onboard 3.3V LDO 
        - up to 10mA 
        - Can power a sensor like AS5600 or CUI AMT102 
- **Small size:** 26x21 mm
- **Fully open-source:** 
   - [EasyEDA link](https://easyeda.com/the.skuric/simplefocmini_copy)
   - Project and fabrication files: [Github](https://github.com/simplefoc/SimpleFOC-StepMini)
- **Low-cost:** 
   - JLCPCB production cost ~3-5€
   - Will be available in the [shop](https://www.simplefoc.com/shop) 10-15€
</div>