---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span>Mini</span>
parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "SimpleFOCMini board showcase and getting started information."
nav_order: 2
permalink: /simplefocmini
has_children: true
has_toc: false
toc: true
---


# <span class="simple">Simple<span class="foc">FOC</span>Mini</span>  <small><i>v2.3</i></small>


![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/simplefocmini)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/simplefocmini?color=blue)

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/side_real.jpg" class="width20" alt="SimpleFOCMini v2 side view"/><img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" class="width20" alt="SimpleFOCMini v2 top view"/><img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/bottom.png" class="width20" alt="SimpleFOCMini v2 bottom view"/>

<span class="simple">Simple<span class="foc">FOC</span>Mini</span> is a compact BLDC driver for use with the <span class="simple">Simple<span class="foc">FOC</span>library</span>. Version 2 replaces the DRV8313 with a DRV8316 and adds three-phase low-side current sensing and supply-voltage sensing. Its PWM and enable pinout remains compatible with Mini v1.1, so existing control code can be reused.

[Get started with your mini board](mini_getting_started){: .btn .btn-docs}

## Version features comparison

| Feature | Mini v1.1 | Mini v2.3 |
| --- | --- | --- |
|Image | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/v1.1/images/top.png" style="height:250px" alt="SimpleFOCMini v1.1 top view"/> | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" style="height:250px" alt="SimpleFOCMini v2 top view"/> |
| Motor driver | DRV8313 | DRV8316 |
| Power supply | 8-35V | 5-35V |
| Maximum current | 2.5A per phase | 8A |
| Current sensing | Not integrated | Three-phase low-side (150mV/A) |
| Supply-voltage sensing | Not integrated | Integrated (0.101V/V scale) |
| 3.3V regulator output | Up to 10mA | Up to 20mA |
| Board size | 20 x 26mm | 24 x 25mm |
| PWM and enable pinout | Original layout | Compatible with v1.1 |

The v2.3 board is a complete redesign, but retains the v1.1 PWM and enable pinout and is compatible with code written for Mini v1.

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/compare_mini.jpg" class="img300" alt="SimpleFOCMini v1 and v2 size comparison">

## Features of SimpleFOCMini v2.3
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **DRV8316 based** - [datasheet](https://www.ti.com/lit/ds/symlink/drv8316.pdf)
  - Power supply: 5-35V
  - Maximum current: 8A
  - 3-PWM mode
- **Sensing**: 
  - 3x Low-side current sensing (150mV/A)
  - Power supply voltage sensing (Scale 0.101V/V)
- **Onboard 3.3V LDO**: Up to 20mA output
- **Small size**: 24x25 mm
- **Fully open-source**:
  - [EasyEDA](https://oshwlab.com/the.skuric/simplefocmini_copy_copy)
  - [GitHub](https://github.com/simplefoc/SimpleFOCMini) 
- **Low-cost**: 
   - JLCPCB production cost ~5€
   - Available from [Makerfabs](https://www.makerfabs.com/simplefocmini.html)



<blockquote class="warning">
<p class="heading">Low-side current-sensing compatibility</p>
Mini v2 uses low-side current sensing. Not all supported microcontrollers can use this sensing method; check the [microcontroller support guide](microcontrollers) before relying on current control. Observe the board's voltage and current ratings when selecting a motor and power supply.
</blockquote>


### Release log

Release | Date | Description
--- | --- | ---
v2.3 | 2025-07 | Smaller layout and supply-voltage sensing.
v2.0 | 2025-01 | Redesigned around the DRV8316 with three-phase low-side current sensing.
v1.1 | 2024-04 | A quick iteration with a few changes:<br> 1. Aligned motor output header with the input header so that it can be stacked in the protoboard<br>2. Input header updated to be easier to use with arduino UNO, nucleos, but also with qtpy...<br> - Changed the order of the IN1,IN2,IN3 and EN:<br> - Added an additional GND pin <br>
v1.0 | 2022-04 | Initial release

### Connection schematic
An electrical connection example of a BLDC motor with an encoder as position sensor. 
<p><img src="extras/Images/connection_mini.jpg" class="width60"></p>
For the v1.1 pinout and a connection example, see [connecting the Mini](mini_example). The v2 board retains the v1.1 PWM and enable pinout.

## Project example : Reaction wheel inverted pendulum
<iframe class="youtube"  src="https://www.youtube.com/embed/Ih-izQyXJCI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
This is a project of designing and controlling the reaction wheel inverted pendulum based entirely on Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> and <span class="simple">Simple<span class="foc">FOC</span>Shield</span>

This is a very fun project in many ways, and it is intended:
- Students in search for a good testing platform for their advanced algorithms
- Everyone with a bit of free time and a motivation to create something cool :D

For full documentation of necessary components, design choices and the code please visit the [project docs](simplefoc_pendulum).

<blockquote class="info"> <p class="heading">📢 
This project can be entirely done by using the  <span class="simple">Simple<span class="foc">FOC</span>Mini</span> board. </p>
</blockquote>

## Project example : Steer by wire - bidirectional haptic control examples 
<iframe class="youtube" src="https://www.youtube.com/embed/xTlv1rPEqv4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This video demonstrates <span class="simple">Simple<span class="foc">FOC</span>Shield</span> support for stacking with Arudino UNO and STM32 Nucleo-64 board. As well as support for different sensors magnetic and encoders with relatively large precision span.

The control algorithms implemented in this project are :
- **Steer by wire** (force feedback): two motors with virtually coupled positions
- **Interactive gauge** (haptic velocity control): two motors with virtually coupled position and velocity


For full documentation of the projects setup and the code please visit the [project docs](haptics_examples).

<blockquote class="info"> <p class="heading">📢 
This project can be entirely done by using the  <span class="simple">Simple<span class="foc">FOC</span>Mini</span> board. </p>
</blockquote>

## Getting started

You already have your own <span class="simple">Simple<span class="foc">FOC</span>Mini</span>? <br>

[Getting started guide](mini_getting_started){: .btn .btn-docs}



## How to get hold of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> 
- **Fabricate the board yourself**:  [Board fabrication docs](mini_fabrication){: .btn .btn-docs}
- **Order the finished and tested board**:  Check out our [shop](https://simplefoc.com/shop).

