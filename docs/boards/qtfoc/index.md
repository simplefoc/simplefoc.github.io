---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span> <b>Qt</b>FOC</span>
parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "QtFOC: a tiny open-source BLDC and stepper driver for Seeed Studio XIAO and Adafruit QT Py boards, with low-side current sensing and an onboard 5V buck converter."
nav_order: 5
permalink: /qtfoc
has_children: true
has_toc: false
toc: true
---

# <span class="simple">Simple<span class="foc">FOC</span> <b>Qt</b>FOC</span> <small><i>v1.6</i></small>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)

<img src="https://raw.githubusercontent.com/simplefoc/QtFOC/master/images/top.png" class="width20" alt="QtFOC top view"> <img src="https://raw.githubusercontent.com/simplefoc/QtFOC/master/images/bottom.png" class="width20" alt="QtFOC bottom view"> <img src="https://raw.githubusercontent.com/simplefoc/QtFOC/master/images/side.png" class="width20" alt="QtFOC side view">

<span class="simple"><b>Qt</b>FOC</span> is a tiny, open-source, low-cost BLDC driver board in the form factor of the **Seeed Studio XIAO** and **Adafruit QT Py** boards. You plug in a XIAO or a QT Py, connect a motor and a power supply, and you have a complete FOC motor controller in a **25 x 45mm** footprint, fully compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>.

The board generates its own 5V from the motor power supply and powers the microcontroller from it, so once the board is programmed it doesn't need a USB cable or an extra regulator.

[Get started with your board](qtfoc_getting_started){: .btn .btn-primary} [Order from Makerfabs](https://www.makerfabs.com/simplefoc-qtfoc.html){: .btn .btn-makerfabs} [GitHub project](https://github.com/simplefoc/QtFOC){: .btn .btn-github}

<img src="https://raw.githubusercontent.com/simplefoc/QtFOC/master/images/side_real1.jpg" class="img300" alt="QtFOC with a XIAO board"> <img src="https://raw.githubusercontent.com/simplefoc/QtFOC/master/images/spi_motor.jpg" class="img300" alt="QtFOC with a motor and an SPI sensor">

## What can it run?

The XIAO / QT Py format has few pins, but enough for a complete FOC setup:

1. **A motor**: a BLDC motor, or a stepper motor with the `HybridStepperMotor` class (<span class="simple">Simple<span class="foc">FOC</span>library</span> v2.3.5+)
2. **A position sensor**: SPI (on the JST connector), encoder, I2C or PWM, see the [limitations](#limitations)
3. **Current sensing**: low-side current sensing on two phases
4. **Communication**: USB, or Serial on the UART header

## Features

- **DRV8316C** integrated 3-phase driver - [datasheet](https://www.ti.com/lit/ds/symlink/drv8316.pdf)
   - Hardware configured, no SPI configuration needed
   - 3PWM mode
   - Protections: undervoltage lockout, charge pump fault, overcurrent (~16A), overtemperature
- **Input voltage**: 8-35V
- **Current**: up to 8A peak (DRV8316 limit), the continuous current depends on cooling
- **Current sensing**: low-side, phases A and B, 150mV/A (about ±11A with a 3.3V ADC) or 300mV/A (about ±5.5A), selected with a solder pad on the bottom of the board
- **Power supply voltage sensing**: VIN/11 on `A2`
- **Onboard 5V buck converter**: powers the XIAO / QT Py from VIN through a diode, so USB and VIN can be connected at the same time (200mA max)
- **SPI connector**: 6-pin JST SH for SPI position sensors (AS5047, AS5048A, MT6701, MT6835, ...)
- **UART / GPIO header**: `3V3`, `GND`, `RX`, `TX`
- **Fault LED** and **power LED**
- **Compact**: 25 x 45mm, with 4 [mounting holes](#mounting-holes) for M2 screws
- **Open source**: designed in **EasyEDA** - [EasyEDA project](https://oshwlab.com/the.skuric/simplefocmini_copy_copy_copy_copy), all fabrication files on [GitHub](https://github.com/simplefoc/QtFOC)
- **Low cost**: about the same as the <span class="simple">Simple<span class="foc">FOC</span>Mini</span>, around 10$

## Limitations

- **Low-side current sensing** is not supported on all microcontrollers, see the [compatible boards](#compatible-boards). Without it, use voltage torque control.
- **Few free pins**: once the motor, the current sensing and the voltage sensing are connected, only `D6` (TX) and `D7` (RX / CS) and the SPI pins `D8`-`D10` are left.
   - With an SPI sensor, only `D6` is free.
   - An encoder needs both `D6` and `D7`, so it can't be used together with an SPI sensor or the UART header.
   - The I2C pins of the header (`D4` / `D5`) drive the motor. I2C sensors can be connected to the [STEMMA QT connector](qtfoc_getting_started#i2c-sensors-on-the-stemma-qt-connector) of QT Py boards that have a separate I2C bus.
- **5V buck converter**: up to 200mA, enough for the MCU and the sensor, but not for the WiFi / BLE radio of ESP32 boards. If you use WiFi or BLE, also power the MCU board from USB.

## Mounting holes

The board has four 2mm mounting holes, for M2 screws or standoffs, in the corners of a **21 x 36mm** rectangle:

<img src="https://raw.githubusercontent.com/simplefoc/QtFOC/master/images/holes.png" class="width40" alt="QtFOC mounting hole dimensions">

- **Board size**: 25 x 43mm, plus about 2mm for the SPI connector tab
- **Hole diameter**: 2mm, plated
- **Hole spacing**: 21mm across the board, 36mm along it

The spacing across the board (21mm) is the same as on the [<span class="simple">Simple<span class="foc">FOC</span>Mini</span>](simplefocmini#mounting-holes), so both boards fit the same rail or bracket width.

<blockquote class="warning" markdown="1"><p class="heading">The holes are connected to GND</p>
The plated mounting holes are connected to the board's ground. If you mount the board on a metal frame, the frame is connected to GND too, so make sure nothing else on the frame is at a different potential.
</blockquote>

## Compatible boards

Any board with the XIAO / QT Py pinout fits. Here is a non-exhaustive list:

Board | MCU | Supported by <span class="simple">Simple<span class="foc">FOC</span></span> | Low-side current sensing
--- | --- | --- | ---
[QT Py SAMD21](https://www.adafruit.com/product/4600) <br> [XIAO SAMD21](https://www.seeedstudio.com/Seeeduino-XIAO-Arduino-Microcontroller-SAMD21-Cortex-M0+-p-4426.html) | SAMD21 | ✔️ | ✔️ (v2.3.5+)
[QT Py ESP32-S3](https://www.adafruit.com/product/5426) <br> [XIAO ESP32-S3](https://www.seeedstudio.com/XIAO-ESP32S3-p-5627.html) | ESP32-S3 | ✔️ | ✔️
[QT Py ESP32 Pico](https://www.adafruit.com/product/5395) | ESP32 | ✔️ | ✔️
[XIAO ESP32-C6](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C6-p-5884.html) | ESP32-C6 | ✔️ | ✔️
[QT Py RP2040](https://www.adafruit.com/product/4900) <br> [XIAO RP2040](https://www.seeedstudio.com/XIAO-RP2040-v1-0-p-5026.html) | RP2040 | ✔️ | ❌
[XIAO RP2350](https://www.seeedstudio.com/Seeed-XIAO-RP2350-p-5944.html) | RP2350 | ✔️ (v2.3.5+) | ❌
[QT Py ESP32-S2](https://www.adafruit.com/product/5325) | ESP32-S2 | ✔️ | ❌
[QT Py ESP32-C3](https://www.adafruit.com/product/5405) <br> [XIAO ESP32-C3](https://www.seeedstudio.com/Seeed-XIAO-ESP32C3-p-5431.html) | ESP32-C3 | ✔️ | ❌
[XIAO nRF52840](https://www.seeedstudio.com/Seeed-XIAO-BLE-nRF52840-p-5201.html) | nRF52840 | ✔️ | ❌
[QT Py CH32V203](https://www.adafruit.com/product/5996) | CH32V203 | ❌ | ❌

See the [microcontroller support](microcontrollers) docs for more details.

## QtFOC or Mini?

Both boards use the DRV8316, so they have the same power stage. The <span class="simple">Simple<span class="foc">FOC</span>Mini</span> is a standalone driver that you wire to any microcontroller. <span class="simple"><b>Qt</b>FOC</span> is a shield for XIAO / QT Py boards, with the wiring, the 5V supply for the MCU and the sensor connector already on the board.

| Feature | <span class="simple">Simple<span class="foc">FOC</span>Mini</span> v2.3 | <span class="simple"><b>Qt</b>FOC</span> v1.6 |
| --- | --- | --- |
| Board | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" class="img200"> | <img src="https://raw.githubusercontent.com/simplefoc/QtFOC/master/images/top.png" class="img200"> |
| Driver | DRV8316 | DRV8316 |
| Supply voltage | 5-35V | 8-35V |
| Current sensing | 3 phases, low-side | 2 phases, low-side |
| Voltage sensing | ✔️ | ✔️ |
| Power for the MCU | 3.3V LDO, 20mA | 5V buck, 200mA |
| Sensor connector | ❌ | SPI (JST SH) |
| Microcontroller | Any, wired with jumpers | XIAO / QT Py, plugged in |
| Size | 24 x 25mm | 25 x 45mm |

## Release history

Version | Date | Changes
--- | --- | ---
v1.0 | 07/25 | First version, the MCU needs a separate power supply
v1.6 | 02/26 | First public release: MCU powered from the DRV8316 buck converter (5V)

## How to get one

- **Order the assembled board**: available from [Makerfabs](https://www.makerfabs.com/simplefoc-qtfoc.html).
- **Make it yourself**: the board is fully open source: the Gerber, BOM and pick and place files are in the [GitHub repository](https://github.com/simplefoc/QtFOC), and the [EasyEDA project](https://oshwlab.com/the.skuric/simplefocmini_copy_copy_copy_copy) can be ordered directly from JLCPCB. See the [JLCPCB ordering guide](board_fabrication){: .btn .btn-docs}
