---
layout: default
title: Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 2
permalink: /arduino_simplefoc_shield_showcase
has_children: true
has_toc: false
---


# Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>  <small><i>v1.3.1</i></small>

This is an open-source low-cost Brushless DC (BLDC) motor driver board intended primarily for low-power FOC applications up to 5Amps. The board is fully compatible with the Arduino UNO and all the boards with the standard Arduino headers.
The <span class="simple">Simple<span class="foc">FOC</span>Shield</span>, in combination with  the <span class="simple">Simple<span class="foc">FOC</span>library</span> provides *user-friendly* way to control BLDC motors both in hardware and software.    

## YouTube demonstration video
<iframe class="youtube" src="https://www.youtube.com/embed/G5pbo0C6ujE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Features
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
- **Low-cost**: Price of €15 - [Check the pricing](https://www.simplefoc.com/simplefoc_shield_product) 
- **Max power 120W** - max current 5A, power-supply 12-24V
   - Designed for Gimbal motors with the internal resistance >10 Ω. 
- **Stackable**: running 2 motors in the same time
- **Encoder interface**: Integrated 3kΩ pullups (configurable)
- **Configurable pinout**: Hardware configuration - soldering connections
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo boards...
- **Open Source**: Fully available fabrication files - [how to make it yourself](arduino_simplefoc_shield_fabrication)
  
<blockquote class="warning"> 
<p class="heading">BEWARE</p>
This BLDC driver board is primarily designed for gimbal motors with the internal resistance of R >10 Ω. Please make sure that your motor fits in this category before deciding to use the <span class="simple">Simple<span class="foc">FOC</span>Shield</span>.
</blockquote>

<img src="extras/Images/shield_bo_v13.jpg" class="img300 img_half"><img src="extras/Images/shield_to_v13.jpg" class="img300  img_half">

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
[Here is a simple guide how to start preparing your setup](arduino_simplefoc_shield_getting_started)



## How to get hold of the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> 
- **Fabricate the board yourself**:  Please visit the [board fabrication](arduino_simplefoc_shield_fabrication) to find out how to manufacture the board yourself!<br>
- **Order the finished and tested board**:  Check out our [shop](https://simplefoc.com/simplefoc_shield_product).

