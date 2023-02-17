---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "SimpleFOC boards"
nav_order: 2
permalink: /boards
has_children: true
has_toc: false
---

# <span class="simple">Simple<span class="foc">FOC</span> Boards</span>

One of the goals of the  <span class="simple">Simple<span class="foc">FOC</span>project</span> is to develop low-cost easy to use BLDC driver boards compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>and completely open source!

For now there have been three official BLDC drivers developed by the <span class="simple">Simple<span class="foc">FOC</span>project</span>:
- <span class="simple">Simple<span class="foc">FOC</span>Shield</span>  - <small>[Find out more](arduino_simplefoc_shield_showcase)</small>
- <span class="simple">Simple<span class="foc">FOC</span>Mini</span> 📢**NEW**  - <small>[Find out more](simplefocmini)</small>
- <span class="simple">Simple<span class="foc">FOC</span> <b>Power</b>Shield</span>

## Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> <small>v2.0.4</small> - <small>[Find out more](arduino_simplefoc_shield_showcase)</small>

This is an open-source low-cost Brushless DC (BLDC) motor driver board intended primarily for low-power FOC applications up to 5Amps. The board is fully compatible with the Arduino UNO and all the boards with the standard Arduino headers.
The <span class="simple">Simple<span class="foc">FOC</span>Shield</span>, in combination with  the <span class="simple">Simple<span class="foc">FOC</span>library</span> provides *user-friendly* way to control BLDC motors both in hardware and software.  

<div class="width40">
<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/master/images/top.png"/>
</div>

### Features
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **Low-cost**: Price of 15-20€ - [Check the pricing](https://www.simplefoc.com/shop) 
- **In-line current sensing**: Up to 3Amps/5Amps bidirectional
   - configurable: 3.3Amps - 3.3V adc, 5Amps - 5V adc
- **Integrated 8V regulator**: 
   - Enable/disable by soldering pads
- **Max power 120W** - max current 5A, power-supply 12-24V **(35V max)**
   - Designed for Gimbal motors with the internal resistance >10 Ωs. 
- **Stackable**: running 2 motors in the same time
- **Encoder/Hall sensors interface**: Integrated 3.3kΩ pullups (configurable)
- **I2C interface**: Integrated 4.7kΩ pullups (configurable)
- **Configurable pinout**: Hardware configuration - soldering connections
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo boards...
- **Open Source**: Fully available fabrication files - [how to make it yourself](https://docs.simplefoc.com/arduino_simplefoc_shield_fabrication)


## 📢**NEW**: <span class="simple">Simple<span class="foc">FOC</span>Mini</span> <small>v1.0</small> - <small>[Find out more](simplefocmini)</small>

Small package, low-cost BLDC driver board fully compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>


<img src="extras/Images/mini.png" class="width40"/><img  src="https://user-images.githubusercontent.com/36178713/164240473-5abd7453-9d38-4f25-9195-378c39180054.jpg"  class="width40"/>




## Features
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **DRV8313 based** - [datasheet](https://www.ti.com/lit/ds/symlink/drv8313.pdf?ts=1650461862269&ref_url=https%253A%252F%252Fwww.google.com%252F)
  - Power supply: 8-24V
  - Max current: 2.5A per phase
  - Onboard 3.3V LDO
- **Small size**: 26x20 mm
- **Fully open-source**:
  - [EasyEDA](https://easyeda.com/the.skuric/simplefocmini)
  - [GitHub](https://github.com/simplefoc/SimpleFOCMini) 
- **Low-cost**: 
   - JLCPCB production cost ~3-5€
   - Will be available in the [shop](https://www.simplefoc.com/shop) soon: 7-10€ 

   Read more about this board at [link](https://github.com/simplefoc/SimpleFOCMini)

## Arduino <span class="simple">Simple<span class="foc">FOC</span> <b>Power</b>Shield</span> <small>v0.2</small> <small>⚠️<i>( under developement)</i></small>

A powerful arduino shield for running BLDC motors using the FOC algorithm. This board is based on the [BTN8982](https://www.infineon.com/dgdl/Infineon-BTN8982TA-DS-v01_00-EN.pdf?fileId=db3a30433fa9412f013fbe32289b7c17) half bridges and can support currents up to 30 Amps continuos and 50Amps peak. Making it a board that can run virtually any BLDC motor.


<div class="width40">
<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOC-PowerShield/main/images/top.png"/>
</div>

<blockquote class="warning" markdown="1">
<p class="heading"> ⚠️ BEWARE: BTN8982/IFX007T performance issues</p>
BTN8982 and IFX007T drivers have been designed for DC motors and are based on old H-bridge technology. They have very long mosfet rise time (multiple microseconds) which in many cases presents a considerable part of the PWM duty cycle. When runnig the BLDC motors, precise PWM duty cycle setting is crutial for smooth and efficient operation. Therefore these drivers will not be able to provide very smooth operation on high frequency PWM (above 15kHz).  Read more about it in the community thread: [link](https://community.simplefoc.com/t/simplefoc-powershield/582).<br>
This performance constraint is the main reason why the <span class="simple">Simple<span class="foc">FOC</span> <b>Power</b>Shield</span> project has for now been put on hold, and although these boards are available through Aliexpress and some other platforms, they will not be available through simplefoc.com. 
<br><br>

This does not mean that the board itself is not functional or that it will not work in your project though. It is still one of the cheepest (simplest) solutions out there for mid to high power BLDC control and with proper tuning of control loops you will still be able to get some good results with it. 
</blockquote>

### Features
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **Low-cost**: Fabrication price under €25/pcs - **⚠️ will not be sold by silplefoc.com**
- **High-side current sensing**: - not yet supported by *Simple**FOC**library*
- **In-line current sensing**: - supported by *Simple**FOC**library*
- **Max power <500W**: max current 30A, power-supply 24V
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo boards, Aruidno DUE...
- **Small size**: 53mm x 60mm
- **Encoder/Hall sensors interface**: Integrated 3.3kΩ pullups (configurable)
- **Open Source**: 
   - Fully available fabrication files  
        - If never done it before, see a similar guide for *Simple**FOC**Shueld*:  [how to make it yourself](https://docs.simplefoc.com/arduino_simplefoc_shield_fabrication)
   - Altium project
   - 3d model
   - schematics

Read more about this board at [link](https://github.com/simplefoc/Arduino-SimpleFOC-PowerShield)
