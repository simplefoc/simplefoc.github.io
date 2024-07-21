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


# Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>  <small><i>v3.2</i></small> 


![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/arduino-simplefocshield)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/arduino-simplefocshield?color=blue)

This is an open-source low-cost Brushless DC (BLDC) motor driver board intended primarily for low-power FOC applications up to 5Amps. The board is fully compatible with the Arduino UNO and all the boards with the standard Arduino headers.
The <span class="simple">Simple<span class="foc">FOC</span>Shield</span>, in combination with  the <span class="simple">Simple<span class="foc">FOC</span>library</span> provides *user-friendly* way to control BLDC motors both in hardware and software.    

<img src="extras/Images/top_botv3.jpg" class="img300 img_half">

## YouTube demonstration video
<iframe class="youtube" src="https://www.youtube.com/embed/G5pbo0C6ujE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Features
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

### New Features of the version v3.x
 - Transition away from stm's L6234 chip to [DRV8313](https://www.ti.com/lit/ds/symlink/drv8313.pdf?ts=1719079575798), which is much more available
 - Transition form TI's INA240 current amps to Allegro's [ACS712](https://www.sparkfun.com/datasheets/BreakoutBoards/0712.pdf) hall sensors
 - Smaller footprint: 56mm x 53mm
 - Fault and reset pins exposed (optional)
 - Fault led indication
 - Designed completely in EasyEDA, which is a free online PCB design tool - **[Official Easy EDA project](https://oshwlab.com/the.skuric/simplefocshield_copy_copy)**


<blockquote class="warning"> 
<p class="heading">BEWARE</p>
This BLDC driver board is primarily designed for gimbal motors with the internal resistance of R >10 Ω. Please make sure that your motor fits in this category before deciding to use the <span class="simple">Simple<span class="foc">FOC</span>Shield</span>.
</blockquote>

## Board version comparison

Feature | <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v1.x | <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v2.x | <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v3.x |
|-|-|-|-|
|<img src="https://simplefoc.com/assets/img/v1.jpg" class="img300 img_half">|<img src="https://simplefoc.com/assets/img/v2.jpg" class="img300  img_half">|<img src="https://simplefoc.com/assets/img/v3.jpg" class="img300  img_half">
**PWM Driver** | [L6234](https://www.st.com/resource/en/datasheet/l6234.pdf) | [L6234](https://www.st.com/resource/en/datasheet/l6234.pdf) | [DRV8313](https://www.ti.com/lit/ds/symlink/drv8313.pdf?ts=1719165774986&ref_url=https%253A%252F%252Fwww.google.com%252F)
**Current Sense** | ❌ | [INA240](https://www.ti.com/lit/ds/symlink/ina240.pdf?ts=1719180172738) | [ACS712](https://www.allegromicro.com/en/products/sense/current-sensor-ics/zero-to-fifty-amp-integrated-conductor-sensor-ics/acs712)
**Current measurement range** | ❌ | (configurable) ±3.3/5Amps | ±5Amps
**Onboard LDO** | ❌ | LM7808 | LM7808
**Max current** | 2Amps (5Amp peak) | 2Amps (5Amp peak) | 2Amps (3Amp peak)
**Max voltage** | 24V | 35V | 35V
**Protections** | Overtemperature | Overtemperature | Overtemperature, Overcurrent
**Stackable** | ✔️ | ✔️ | ✔️
**I2C pullups** | ✔️ | ✔️ | ✔️
**Encoder pullups** | ✔️ | ✔️ | ✔️
**Footprint** | 68mm x 53 mm | 68mm x 53 mm | 56mm x 53mm
**Design tool** | Altium Designer 2019 | Altium Designer 2019 | EasyEDA 


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
- **Order the finished and tested board**:  Check out our [shop](https://simplefoc.com/simplefoc_shield_product).

