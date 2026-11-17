---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>
parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "SimpleFOC-microspora: an all-in-one BLDC and stepper motor controller with MCU, driver, encoder, current sensing and CAN on a 33x34mm board."
nav_order: 1
permalink: /microspora_landing
has_children: true
has_toc: false
toc: true
---

# <span class="simple">Simple<span class="foc">FOC</span>-microspora</span> <small><i>v1.7</i></small>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/SimpleFOC-microspora)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/SimpleFOC-microspora?color=blue)

<img style="height:200px" src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/side.png" alt="SimpleFOC-microspora side view"/><img style="height:200px" src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/top.png" alt="SimpleFOC-microspora top view"/><img style="height:200px" src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/bottom.png" alt="SimpleFOC-microspora bottom view"/><img style="height:200px" src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/top_real.jpg" alt="SimpleFOC-microspora board photo"/>

<span class="simple">Simple<span class="foc">FOC</span>-microspora</span> is an all-in-one BLDC and stepper motor controller for the <span class="simple">Simple<span class="foc">FOC</span>library</span>. The microcontroller, motor driver, magnetic encoder, current sensing and CAN transceiver all fit on a 33x34mm board that mounts directly on the back of the motor. Connect a motor, a power supply and a USB-C cable, and you are ready to go.

The board is a fork of the [MicroSpora board by RamBros](https://github.com/rambros3d/MicroSpora-SimpleFOC), adapted for the <span class="simple">Simple<span class="foc">FOC</span></span> ecosystem.

📢 Available now from **Makerfabs**: [See more on makerfabs.com](https://www.makerfabs.com/simplefoc-microspora.html)

[Get started with your microspora board](microspora_connection){: .btn .btn-docs}

## Features

- **Plug & play** with the Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **MCU**: STM32G431CBU6 (Cortex-M4, 170MHz, FPU)
- **Motor driver**: DRV8316C
  - Power supply: 8-35V
  - Maximum current: ~8A
  - 6-PWM mode, configured over SPI
- **BLDC and stepper motors** (stepper in [hybrid stepper mode](hybrid_stepper_theory))
- **Sensing**:
  - MT6701 14-bit magnetic encoder, on the bottom of the board, right above the motor shaft
  - 3-phase low-side current sensing, with SPI-configurable gain
  - Supply voltage sensing
- **Connectivity**:
  - USB-C for programming (DFU, no programmer needed) and serial communication
  - CAN with two daisy-chain JST connectors and a cuttable 120Ω termination
  - 6-pin SPI JST connector
  - 5-pin I2C / UART / encoder / step-dir JST connector, Qwiic / STEMMA QT compatible
- **Compact**: 33x34mm, with mounting slots for most gimbal motors (GM2804 to GM5208) and NEMA17 steppers
- **Fully open-source**:
  - [EasyEDA](https://oshwlab.com/the.skuric/microspora-simplefoc-antun)
  - [GitHub](https://github.com/simplefoc/SimpleFOC-microspora)
  - [Example firmware](https://github.com/simplefoc/microspora_simplefoc_firmware)

## What can you do with it?

- Run **gimbal BLDC motors and NEMA17 steppers** in closed loop with the onboard encoder, see the [code guide](microspora_code)
- Use **FOC current control** for precise torque control, with [automatic motor characterisation](motor_characterisation) and [current loop tuning](tuning_current_loop)
- Connect **multiple boards on one CAN bus** and control them from a computer, see [CAN communication](microspora_can)
- **Tune and plot the motor live** in the [SimpleFOC web controller](webcontroller), no extra code needed
- Add **sensors and peripherals** over the JST connectors: an output shaft encoder, an IMU, or a step/dir input

## Documentation

| Guide | What's inside |
| --- | --- |
| [Connecting the hardware](microspora_connection) | Motor wiring, power supply, connectors, mounting |
| [Toolchain and flashing](microspora_setup) | PlatformIO and Arduino IDE setup, DFU upload |
| [Writing the code](microspora_code) | Step-by-step guide to a complete BLDC or stepper example |
| [CAN communication](microspora_can) | Controlling one or more boards over CAN |
| [Pinout reference](microspora_pinout) | All MCU pins and connector pinouts |
| [Fabrication](microspora_fabrication) | Ordering the board from JLCPCB, with the full BOM |

## Release log

Release | Date | Description
--- | --- | ---
v1.7 | 2026-06 | Sensor moved to the bottom side, much better performance for stepper motors
v1.6 | 2026-04 | JST pinout optimised for Qwiic / STEMMA QT compatibility, reset button reintroduced
v1.5 | 2026-01 | Pinout refinements
v1.4 | 2025-12 | CAN daisy-chain support, two additional JST connectors for SPI and I2C / encoder / GPIO
v1.2 | 2025-07 | CAN and additional GPIO JST connectors, from 6-layer to 4-layer PCB
v0.1 | 2025-05 | Initial public release of the MicroSpora board by [RamBros](https://github.com/rambros3d/MicroSpora-SimpleFOC)

## How to get hold of the <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>

- **Order the finished and tested board**: available from [Makerfabs](https://www.makerfabs.com/simplefoc-microspora.html)
- **Fabricate the board yourself**: [Board fabrication guide](microspora_fabrication){: .btn .btn-docs}

Questions, projects, ideas? Join the discussion on the [SimpleFOC community forum](https://community.simplefoc.com).
