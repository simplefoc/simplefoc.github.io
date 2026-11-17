---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span>Shield</span> 
parent: Setup examples
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 1
permalink: /arduino_simplefoc_shield
grand_parent: Supported Hardware
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---



# Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> example
The <span class="simple">Simple<span class="foc">FOC</span>Shield</span>, being Arduino UNO shield, is compatible with any type of board with the same headers.
It is a BLDC driver with inline current sensing in the form of an Arduino Shield: the latest version, v4, uses the DRV8316 driver and INA240 current amplifiers. The connection below shows the v1 board, but the motor, power and encoder connectors are the same on all versions.

<p><img src="extras/Images/foc_shield_v13.png" class="img400"></p>

## Connector types
 - Terminal connectors 
    - motor phase lines `a`,`b` and `c`
    - power supply cables (12V to 24V, max 35V)
 - Encoder connector
    - Integrated configurable pull-ups
 - SPI and QWIIC/STEMMA QT JST connectors (v4)

More information on this link: [Arduino Simple FOC Shield](arduino_simplefoc_shield_showcase).

## Encoder 
- Channels `A` and `B` are connected to the encoder connector `P_ENC`, terminals `A` and `B`. 
- If your encoder has `index` signal you can connect it to the encoder connector as well, terminal `I`.

## Motor
- Motor phases `a`, `b` and `c` are connected directly the motor terminal connector `TB_M1`


## Example connection
<p><img src="extras/Images/foc_shield_v13.jpg" class="width60"></p>
