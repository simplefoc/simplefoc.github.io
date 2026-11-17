---
layout: default
title: Shield v4.x
description: "Arduino SimpleFOCShield v4 board fabrication"
parent: Board Fabrication
grand_parent: <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
nav_order: 4
permalink: /arduino_simplefoc_shield_fabrication_v4
toc: true
---

# Fabrication guide for <span class="simple">Simple<span class="foc">FOC</span>Shield</span> <small><i>v4.x</i></small>

Here is a quick guide to fabricating the Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> [version <i class="fa fa-tag"></i>v4.1](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v4.1).

<p>
<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v4.1/images/top.png" class="width30"><img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v4.1/images/bottom.png" class="width30">
</p>

<blockquote class="info"> 📢 Official EasyEDA Pro project <a href="https://oshwlab.com/the.skuric/project_mzcekczw"> here <i class="fa fa-external-link"></i></a></blockquote>

## Board releases

Version | Link | Release date | Comment
----- | ----- | ---- | ----
<span class="simple">Simple<span class="foc">FOC</span>Shield</span> v4.1 | [release v4.1](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v4.1) | 04/26 | - Official v4 release <br> - Transition to DRV8316 <br> - Transition to INA240 amplifiers <br> - Power supply voltage sensing <br> - QWIIC/STEMMA QT and SPI JST connectors

The [<i class="fa fa-github"></i> GitHub repository](https://github.com/simplefoc/Arduino-SimpleFOCShield) contains:
- <b><i class="fa fa-file"></i> Schematic</b> - PDF file
- <b><i class="fa fa-file"></i> Gerber</b> - Gerber and NC drill files
- <b><i class="fa fa-file"></i> Bill Of Materials (BOM)</b> - CSV file, with LCSC part numbers
- <b><i class="fa fa-file"></i> Pick and place</b> - CSV and Excel files
- <b><i class="fa fa-file"></i> 3D model</b> - STEP and OBJ files
- <b><i class="fa fa-file"></i> PCB design project</b> - EasyEDA Pro

## PCB fabrication and assembly service - recommended

The v4 is not well suited for hand assembly. The DRV8316 comes in a 0.5mm pitch VQFN-40 package with an exposed thermal pad underneath, which needs a reflow oven or a hot-air station, and the passive components are 0603. A PCB fabrication and assembly (PCBA) service is the easiest way to get the board made.

The design is made in EasyEDA Pro and all the components in the BOM have [LCSC](https://www.lcsc.com/) part numbers, so the simplest way is to order from [JLCPCB](https://jlcpcb.com/):

1. Download the Gerber files, the BOM and the pick and place file from the [GitHub repository](https://github.com/simplefoc/Arduino-SimpleFOCShield), or open the [EasyEDA project](https://oshwlab.com/the.skuric/project_mzcekczw) and order directly from it.
2. Upload the Gerber files (as a zip) on JLCPCB. The board has **4 layers**, the default settings are fine for the rest.
3. Enable **PCB Assembly**, on the **top side**.
4. Upload the BOM and the pick and place file, and check the component placement in the preview, especially the orientation of the DRV8316, the INA240s and the LEDs.
   - The pick and place file also lists the configuration solder pads (`SC...` designators) on the bottom side. They are only copper pads, not components, so don't place any parts for them.
5. Place the order.

Other PCBA services work as well, using the Gerber, BOM and pick and place files, but you may need to find replacements for the LCSC parts.

<blockquote class="info" markdown="1"><p class="heading">Through-hole parts</p>
The BOM only contains the SMD parts. The Arduino stacking headers and the encoder header are through-hole parts you solder yourself, as are the terminals for the motor and power supply wires. You can also ask the PCBA service to mount the headers.
</blockquote>

## Components

The main components of the board:

Component | Part | Description
--- | --- | ---
Driver | [DRV8316CTRGFR](https://www.ti.com/lit/ds/symlink/drv8316.pdf) | Integrated 3-phase driver, 8A peak, hardware variant (no SPI)
Current sensing | 2x [INA240A2PWR](https://www.ti.com/lit/ds/symlink/ina240.pdf) | Current sense amplifiers, gain 50
Shunt resistors | 2x 5mΩ, 1206 | Inline current sensing
Voltage regulator | L78M08CDT-TR | 8V regulator for powering the MCU board
Connectors | JST SM06B-SRSS-TB, SM04B-SRSS-TB | SPI and QWIIC/STEMMA QT connectors
Bulk capacitor | 100uF, 35V or more | Power supply decoupling

The full list with all the passives is in the BOM file in the [repository](https://github.com/simplefoc/Arduino-SimpleFOCShield).

## Fully assembled versions

Order your own fully tested and assembled Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> from our [shop](https://simplefoc.com/simplefoc_shield_product).
