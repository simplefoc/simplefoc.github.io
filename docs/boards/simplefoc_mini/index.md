---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span>Mini</span>
parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "Arduino SimpleFOCShield board showcase."
nav_order: 2
permalink: /simplefocmini
has_children: true
has_toc: false
---


# <span class="simple">Simple<span class="foc">FOC</span>Mini</span>  <small><i>v1.1</i></small>

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/side.png" class="width20"/><img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" class="width20"/><img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/bottom.png" class="width20"/>

<span class="simple">Simple<span class="foc">FOC</span>Mini</span> is a small-package, low-cost, modular and user-friendly driver for running gimbal BLDC motors with FOC algorithm. As for the <span class="simple">Simple<span class="foc">FOC</span>Shields</span>, the main motivation of this board is to make using low-power BLDC motors in hobby applications more accessible.
The main goals of this board however are:

- Produce a small package BLDC driver that still has all the features of the <span class="simple">Simple<span class="foc">FOC</span>Shield </span>v1
- Make it in away to be a minimal working example for users that are interested to build their own boards based on the DRV8313 chip.
- Make it as cheap as possible.
- Use only long-term (as of mid 2022) available components.
- Finally, the goal of this board is to provide a modular and simple setup for controlling gimbal motors (up to 3Amps) using FOC control and enable fast prototyping and full exploitation of their capabilities.

This board in combination with the <span class="simple">Simple<span class="foc">FOC</span>library</span> will give you a simple and intuitive way to control the BLDC motors' current, torque, velocity and position. And this board can be used as a drop in replacement for the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v1.



<img  src="https://simplefoc.com/assets/img/mini.jpg"  class="width20"/><img  src="https://simplefoc.com/assets/img/mini_front.jpg"  class="width20"/><img  src="https://simplefoc.com/assets/img/mini_back.jpg"  class="width20"/>


## Features
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **DRV8313 based** - [datasheet](https://www.ti.com/lit/ds/symlink/drv8313.pdf?ts=1650461862269&ref_url=https%253A%252F%252Fwww.google.com%252F)
  - Power supply: 8-35V
  - Max current: 2.5A per phase
  - Onboard 3.3V LDO
- **Small size**: 26x21 mm
- **Fully open-source**:
  - [EasyEDA](https://easyeda.com/the.skuric/simplefocmini)
  - [GitHub](https://github.com/simplefoc/SimpleFOCMini) 
- **Low-cost**: 
   - JLCPCB production cost ~3-5€
   - Will be available in the [shop](https://www.simplefoc.com/shop) soon: 7-10€ 



<blockquote class="warning"> 
<p class="heading">BEWARE</p>
This BLDC driver board is primarily designed for gimbal motors with the internal resistance of R >10 Ω. Please make sure that your motor fits in this category before deciding to use the <span class="simple">Simple<span class="foc">FOC</span>Mini</span>.
</blockquote>

<img src="https://simplefoc.com/assets/img/shield_vs_mini.jpg" class="img300"><img src="https://user-images.githubusercontent.com/36178713/164240473-5abd7453-9d38-4f25-9195-378c39180054.jpg" class="img300">

### Release log

Release | Date | Description
--- | --- | ---
v1.1 | 2024-04 | A quick iteration with a few changes:<br> 1. Aligned motor output header with the input header so that it can be stacked in the protoboard<br>2. Input header updated to be easier to use with arduino UNO, nucleos, but also with qtpy...<br> - Changed the order of the IN1,IN2,IN3 and EN:<br> - Added an additional GND pin <br>
v1.0 | 2022-04 | Initial release

### Connection schematic
An electrical connection example of a BLDC motor with an encoder as position sensor. 
<p><img src="extras/Images/connection_mini.jpg" class="width60"></p>
For more information about how to connect you hardware to your shield, check the full [connection example](mini_example).

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
[Here is a simple guide how to start preparing your setup](mini_getting_started)



## How to get hold of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> 
- **Fabricate the board yourself**:  Please visit the [board fabrication](mini_fabrication) to find out how to manufacture the board yourself!<br>
- **Order the finished and tested board**:  Check out our [shop](https://simplefoc.com/shop).

