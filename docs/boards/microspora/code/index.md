---
layout: default
title: Microspora Code
parent: <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>
grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "Microspora board showcase and getting started information."
nav_order: 2
permalink: /microspora_code
has_children: true
has_toc: false
toc: true
---

# Microspora firmware and pinout reference

## Programming the board in DFU mode

<img src="./extras/Images/dfu_btns.png" class="width60" alt="Microspora  DFU buttons"/>

The board firmware can be built with [PlatformIO](https://platformio.org/) and or Arduino IDE. But in order to flash the 
firmware the board needs to be in the DFU mode. To do so follow these steps:

1. Connect the board to the computer using USB-C.
2. Enter DFU mode: hold **BOOT**, press and release **RESET**, wait one or two seconds, then release **BOOT**.
3. Upload the firmware from PlatformIO.

An example firmware for the Microspora board can be found in the [Microspora firmware repository](https://github.com/simplefoc/microspora_simplefoc_firmware). The reference firmware runs the FOC loop from a 10kHz hardware-timer interrupt. Serial monitoring uses 250000 baud. The board also exposes the SimpleFOC Commander interface and optional SimpleFOC CAN communication.


[See the board detailed pinout here](microspora_pinout){: .btn .btn-docs}


## Work in progress

The full documentation is still being expanded, but the project is already designed for use with the SimpleFOC ecosystem and supports the core features needed for compact low-power BLDC control.

For now, you can follow the discussions on the [SimpleFOC community forum](https://community.simplefoc.com) or the project Discord server for updates and usage tips.
