---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span> <b>Drive</b>Shield</span>
parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "SimpleFOC DriveShield: an open-source Arduino shield BLDC driver for currents up to 20A continuous, with inline current sensing."
nav_order: 3
permalink: /driveshield
has_children: true
has_toc: false
toc: true
---

# <span class="simple">Simple<span class="foc">FOC</span> <b>Drive</b>Shield</span> <small><i>v1.8</i></small>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/SimpleFOC-DriveShield)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/SimpleFOC-DriveShield?color=blue)

The <span class="simple">Simple<span class="foc">FOC</span> <b>Drive</b>Shield</span> is the big brother of the [<span class="simple">Simple<span class="foc">FOC</span>Shield</span>](arduino_simplefoc_shield_showcase): an open-source, low-cost BLDC driver in the form of an Arduino shield, for motors that need **up to 20A continuous**. It has the same philosophy, form factor and configuration as the Shield: simple to use, stackable, configured with solder pads, and fully compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>.

It is also meant as a **template project** for the community: a relatively simple design, made in EasyEDA with all the fabrication files available, that is easy to modify for your own driver.

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/v1.8/images/top.png" class="width30"> <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/v1.8/images/bottom.png" class="width30"> 

[Get started with your board](driveshield_getting_started){: .btn .btn-primary} [Order from Makerfabs](https://www.makerfabs.com/simplefoc-driveshield.html){: .btn .btn-docs} [GitHub project](https://github.com/simplefoc/SimpleFOC-DriveShield){: .btn .btn-docs}

## Features

- **Max current**: 20A continuous, 30A peak (measured)
- **Supply voltage**: 8-30V
- **Gate driver**: [DRV8320H](https://www.ti.com/lit/ds/symlink/drv8320.pdf), 3PWM mode, hardware configured
   - Protections: undervoltage lockout, charge pump fault, MOSFET overcurrent and short circuit, gate driver fault, overtemperature
   - Fault LED, and an optional fault output to the microcontroller
- **MOSFETs**: [BSZ0904NSI](https://www.infineon.com/dgdl/Infineon-BSZ0904NSI-DataSheet-v02_04-EN.pdf?fileId=db3a30432f29829e012f2a1ec7d90032), 30V / 75A, standard 3x3mm footprint, easy to replace
- **Inline current sensing**: [INA240A1](https://www.ti.com/lit/ds/symlink/ina240.pdf) amplifiers (gain 20) with 2mΩ shunts, ±40A range at 3.3V
- **Power supply voltage sensing**: voltage divider (1/11)
- **Integrated 8V regulator**: powers the microcontroller board through `VIN` (v1.8+)
- **Stackable**: run two motors from one microcontroller
- **Encoder/Hall interface**: 3.3kΩ pull-ups, configurable
- **I2C interface**: 4.7kΩ pull-ups, configurable
- **Connectors**: STEMMA QT/QWIIC for I2C, JST for SPI sensors
- **Configurable pinout**: solder pads, same options as the <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo-64, ESP32 boards with UNO headers, ...
- **Open source**: designed in **EasyEDA** - [EasyEDA project](https://oshwlab.com/the.skuric/SimpleFOC-Drive), all fabrication files on [GitHub](https://github.com/simplefoc/SimpleFOC-DriveShield)
- **Low cost**: about 25-40€

## Shield or DriveShield?

Both boards share the footprint, the headers, the solder pad configuration and the connectors, so moving from one to the other is easy. The difference is the power stage:

| Feature | <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v4 | <span class="simple">Simple<span class="foc">FOC</span> <b>Drive</b>Shield</span> v1.8 |
| --- | --- | --- |
| Board | <img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v4.1/images/top_real.jpg" class="img200"> | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/v1.8/images/top_real.jpg" class="img200"> |
| Power stage | [DRV8316](https://www.ti.com/lit/ds/symlink/drv8316.pdf) integrated driver | [DRV8320H](https://www.ti.com/lit/ds/symlink/drv8320.pdf) gate driver + 6x [BSZ0904NSI](https://www.infineon.com/dgdl/Infineon-BSZ0904NSI-DataSheet-v02_04-EN.pdf?fileId=db3a30432f29829e012f2a1ec7d90032) MOSFETs |
| Continuous current | 3-5A | 20A |
| Peak current | 8A | 30A (measured) |
| Supply voltage | up to 35V | 8-30V |
| Current sensing | INA240A2, 5mΩ, ±6.6A / ±10A | INA240A1, 2mΩ, ±40A |
| Voltage sensing | ✔️ | ✔️ |
| Fault output to MCU | ❌ (LED only) | ✔️ optional, pin `A3` |
| Onboard 8V regulator | ✔️ | ✔️ (v1.8+) |
| STEMMA QT / SPI connectors | ✔️ | ✔️ |
| Footprint | 56 x 53mm | 56 x 53mm |
| Typical motors | Gimbal motors, small drone motors | Drone motors, larger BLDC motors, robot joints |

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/v1.8/images/comp.jpg" class="width50">

As a rule of thumb: if your motor needs more than a few Amps, or has a phase resistance well below 1Ω, use the DriveShield.

## Thermal performance

The board can measure currents up to about 40A, and is meant for applications up to about 20A continuous. At higher currents it gets hot, depending on the PCB copper thickness and the cooling.

The temperatures were measured on the top of the board, on the DRV8320H and the MOSFETs, with a 24V supply, at about 25°C ambient, with no heatsink. The motor ran slowly in open loop with a constant current, for two PCB versions:

- **Standard 4-layer**: 1oz (35µm) outer layers, 0.5oz inner layers
- **Thick 4-layer**: 2oz (70µm) outer layers, 0.5oz inner layers

Current | MOSFETs, standard | MOSFETs, thick | DRV8320H, standard | DRV8320H, thick
--- | --- | --- | --- | ---
10A | 57°C | 53°C | 50°C | 52°C
15A | 78°C | 68°C | 62°C | 62°C
20A | 125°C | 100°C | 82°C | 82°C

The MOSFETs are rated up to 150°C and the DRV8320H up to 125°C, so the board can run at 20A without extra cooling, but with little margin.

<blockquote class="warning" markdown="1"><p class="heading">Above 15A continuous</p>
Use a heatsink on the MOSFETs, or order the PCB with thick copper (2oz outer layers).
</blockquote>

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/v1.8/images/experiment/1oz%20(1).jpg" class="width30"> <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/v1.8/images/experiment/2oz%20(1).jpg" class="width30">

## Release history

Version | Date | Changes
--- | --- | ---
v0.1 | 05/24 | Test version: DRV8300 gate driver, SE3082G dual MOSFETs, ACS712 current sensing, 20V max
v1.0 | 06/24 | DRV8320H gate driver, 30V max
v1.1 | 07/24 | 3x3mm BSZ09x MOSFETs instead of SE3082G
[v1.2](https://github.com/simplefoc/SimpleFOC-DriveShield/releases/tag/v1.2) | 08/24 | First release: 4-layer PCB, stacking (solder pads), configurable pull-ups
v1.7 | 02/26 | STEMMA QT and SPI connectors, INA240 current sensing, power supply voltage sensing
[v1.8](https://github.com/simplefoc/SimpleFOC-DriveShield/releases/tag/v1.8) | 06/26 | 8V regulator to power the microcontroller from `VIN`

## How to get one

- **Order the assembled board** from [Makerfabs](https://www.makerfabs.com/simplefoc-driveshield.html).
- **Make it yourself**: the Gerber, BOM and pick and place files are in the [GitHub repository](https://github.com/simplefoc/SimpleFOC-DriveShield), and the [EasyEDA project](https://oshwlab.com/the.skuric/SimpleFOC-Drive) can be ordered directly from JLCPCB. The process is the same as for the [Shield v4](arduino_simplefoc_shield_fabrication_v4).
