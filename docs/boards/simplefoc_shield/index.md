---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "Arduino SimpleFOCShield board showcase."
nav_order: 1
permalink: /arduino_simplefoc_shield_showcase
has_children: true
has_toc: false
toc: true
---


# Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>  <small><i>v4.1</i></small> 


![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/arduino-simplefocshield)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/arduino-simplefocshield?color=blue)

This is an open-source low-cost Brushless DC (BLDC) motor and Stepper driver board intended for low and mid-power FOC applications, up to 5A continuous and 8A peak. The board is fully compatible with the Arduino UNO and all the boards with the standard Arduino headers.
The <span class="simple">Simple<span class="foc">FOC</span>Shield</span>, in combination with  the <span class="simple">Simple<span class="foc">FOC</span>library</span> provides *user-friendly* way to control BLDC motors both in hardware and software.    

<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v4.1/images/top.png" class="width30"> <img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v4.1/images/bottom.png" class="width30"> <img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v4.1/images/side.png" class="width30">

[Get started with your board](arduino_simplefoc_shield_installation){: .btn .btn-primary} [Fabricate the board](arduino_simplefoc_shield_fabrication){: .btn .btn-docs}

### Features
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> - [github](https://github.com/simplefoc/Arduino-FOC)
- **Low-cost**: Price of 15-30€ - [Check the pricing](https://www.simplefoc.com/shop) 
- **DRV8316 driver**: 3-5A continuous, 8A peak
   - Protections: undervoltage, overcurrent, overtemperature, with a fault LED
- **In-line current sensing**: bidirectional, ±6.6A (3.3V) or ±10A (5V)
   - INA240A2 current sense amplifiers (gain 50, 5mΩ shunts)
- **Power supply voltage sensing**: optional voltage divider (1/11)
- **Max input voltage**: 35V
- **Integrated 8V regulator**: powers the MCU board, enable/disable by soldering pads
- **Stackable**: running 2 motors in the same time
- **Encoder/Hall sensors interface**: Integrated 3.3kΩ pullups (configurable)
- **I2C interface**: Integrated 4.7kΩ pullups (configurable)
- **QWIIC/STEMMA QT connector**: 4-pin JST connector for I2C sensors and modules
- **SPI connector**: 6-pin JST connector for SPI magnetic sensors
- **Configurable pinout**: Hardware configuration - soldering connections
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo boards...
- **Open Source**: 
   - Fully designed in **EasyEDA Pro**: [EasyEDA project](https://oshwlab.com/the.skuric/project_mzcekczw) 🎉
   - Fully available fabrication files - [how to make it yourself](arduino_simplefoc_shield_fabrication)

### New features of the version v4.x
 - Transition from the DRV8313 to the [DRV8316](https://www.ti.com/lit/ds/symlink/drv8316.pdf) driver, with a much higher current capacity: 8A peak instead of 3A
 - Transition from the ACS712 Hall sensors back to TI's [INA240](https://www.ti.com/lit/ds/symlink/ina240.pdf) current amplifiers, for more precise current sensing
 - Power supply voltage sensing
 - **SPI** and **QWIIC/STEMMA QT** JST connectors
 - Designed completely in **EasyEDA Pro**, a free online PCB design tool - **[Official EasyEDA project](https://oshwlab.com/the.skuric/project_mzcekczw)**

<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/master/images/qwiic.jpg" style="height:300px">

<blockquote class="info" markdown="1"><p class="heading">Upgrading from v3</p>
The pin options of the solder pads are the same as on v3, so your pinout carries over. Check the [v4 soldering pads](pads_soldering_v4) for what's new, and update the [current sensing code](foc_shield_code#step-4-current-sensing-and-foc-current-control), as the amplifiers have changed.
</blockquote>

## YouTube demonstration video
<iframe class="youtube" src="https://www.youtube.com/embed/G5pbo0C6ujE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<small>This video shows an older version of the board.</small>

## Board version comparison

Feature | <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v1.x | <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v2.x | <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v3.x | <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v4.x
|-|-|-|-|-|
|<img src="https://simplefoc.com/assets/img/v1.jpg" class="img200 img_half">|<img src="https://simplefoc.com/assets/img/v2.jpg" class="img200  img_half">|<img src="https://simplefoc.com/assets/img/v3.jpg" class="img200  img_half">|<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v4.1/images/top_real.jpg" class="img200  img_half">
**PWM Driver** | [L6234](https://www.st.com/resource/en/datasheet/l6234.pdf) | [L6234](https://www.st.com/resource/en/datasheet/l6234.pdf) | [DRV8313](https://www.ti.com/lit/ds/symlink/drv8313.pdf) | [DRV8316](https://www.ti.com/lit/ds/symlink/drv8316.pdf)
**Current Sense** | ❌ | [INA240](https://www.ti.com/lit/ds/symlink/ina240.pdf) | [ACS712](https://www.allegromicro.com/en/products/sense/current-sensor-ics/zero-to-fifty-amp-integrated-conductor-sensor-ics/acs712) | [INA240](https://www.ti.com/lit/ds/symlink/ina240.pdf)
**Current measurement range** | ❌ | (configurable) ±3.3/5Amps | ±5Amps | ±6.6A (3.3V) / ±10A (5V)
**Voltage sensing** | ❌ | ❌ | ❌ | ✔️
**Onboard LDO** | ❌ | LM7808 | LM7808 | L78M08
**Max current** | 2Amps (5Amp peak) | 2Amps (5Amp peak) | 2Amps (3Amp peak) | 3-5Amps (8Amp peak)
**Max voltage** | 24V | 35V | 35V | 35V
**Protections** | Overtemperature | Overtemperature | Overtemperature, Overcurrent | Undervoltage, Overcurrent, Overtemperature
**Stackable** | ✔️ | ✔️ | ✔️ | ✔️
**I2C pullups** | ✔️ | ✔️ | ✔️ | ✔️
**Encoder pullups** | ✔️ | ✔️ | ✔️ | ✔️
**SPI / QWIIC connectors** | ❌ | ❌ | ❌ | ✔️
**Footprint** | 68mm x 53 mm | 68mm x 53 mm | 56mm x 53mm | 56mm x 53mm
**Design tool** | Altium Designer 2019 | Altium Designer 2019 | EasyEDA | EasyEDA Pro


### Connection schematic
An electrical connection example of a BLDC motor with an encoder as position sensor. 
<p><img src="extras/Images/foc_shield_v13.jpg" class="width60"></p>
For more information about how to connect you hardware to your shield, check the full [connection example](arduino_simplefoc_shield).

## Project example : Reaction wheel inverted pendulum
<iframe class="youtube"  src="https://www.youtube.com/embed/Ih-izQyXJCI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
This is a project of designing and controlling the reaction wheel inverted pendulum based entirely on Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> and <span class="simple">Simple<span class="foc">FOC</span>Shield</span>

This is a very fun project in many ways, and it is intended:
- Students in search for a good testing platform for their advanced algorithms
- Everyone with a bit of free time and a motivation to create something cool :D

For full documentation of necessary components, design choices and the code please visit the [project docs](simplefoc_pendulum).


## Project example : Steer by wire - bidirectional haptic control examples 
<iframe class="youtube" src="https://www.youtube.com/embed/xTlv1rPEqv4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This video demonstrates <span class="simple">Simple<span class="foc">FOC</span>Shield</span> support for stacking with Arudino UNO and STM32 Nucleo-64 board. As well as support for different sensors magnetic and encoders with relatively large precision span.

The control algorithms implemented in this project are :
- **Steer by wire** (force feedback): two motors with virtually coupled positions
- **Interactive gauge** (haptic velocity control): two motors with virtually coupled position and velocity


For full documentation of the projects setup and the code please visit the [project docs](haptics_examples).


## Getting started

You already have your own <span class="simple">Simple<span class="foc">FOC</span>Shield</span>? <br>
[Here is a simple guide how to start preparing your setup](arduino_simplefoc_shield_installation)



## How to get hold of the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> 
- **Fabricate the board yourself**:  Please visit the [board fabrication](arduino_simplefoc_shield_fabrication) to find out how to manufacture the board yourself!<br>
- **Order the finished and tested board**:  Check out our [shop](https://simplefoc.com/simplefoc_shield_product). The v4.1 will also be available from Makerfabs soon.

