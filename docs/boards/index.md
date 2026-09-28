---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "SimpleFOC boards"
nav_order: 2
permalink: /boards
has_children: true
has_toc: false
toc: true
---

# <span class="simple">Simple<span class="foc">FOC</span> Boards</span>

One of the goals of the  <span class="simple">Simple<span class="foc">FOC</span>project</span> is to develop low-cost easy to use BLDC driver boards compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>and completely open source! Therefore, <span class="simple">Simple<span class="foc">FOC</span></span> team members have developed a set of boards, designed specifically for ease of use, to help you kickstart your FOC journey. In addition to being easy to use, the goal of these boards is serve as a reference design for the community to build upon. And finally, even though some of these boards are available in our [shop](https://www.simplefoc.com/shop), our docs provide a lot of documentation and step-by-step guides on how to fabricate the boards yourself.


The SimpleFOC board range includes Arduino-compatible shields, compact driver boards, and boards with an integrated microcontroller. The table below is a quick guide; follow the board links for full specifications, setup, and fabrication information.

|  | [SimpleFOCShield](#simplefocshield) | [SimpleFOC DriveShield](#driveshield) | [SimpleFOCMini](#simplefocmini) | [SimpleFOC-microspora](#microspora) |
| --- | --- | --- | --- | --- |
| Board | <img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/master/images/top.png" style="width:100%;height:90px;object-fit:contain" alt="SimpleFOCShield"/> | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/main/images/top.png" style="width:100%;height:90px;object-fit:contain" alt="SimpleFOC DriveShield"/> | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" style="width:100%;height:90px;object-fit:contain" alt="SimpleFOCMini"/> | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/top.png" style="width:100%;height:90px;object-fit:contain" alt="SimpleFOC-microspora"/> |
| Format | Arduino shield | Arduino shield | Compact driver | Integrated controller |
| Key hardware | DRV8313, inline current sensing | DRV8320H, inline current sensing | DRV8316, three-phase low-side current sensing | STM32G431, DRV8316, MT6701 encoder, CAN |
| Intended use | Low-power FOC with an external MCU (R3 headers) | Higher-current applications with an external MCU (R3 headers) | Small BLDC or stepper setups with an external MCU | Compact standalone BLDC and stepper control |
| |[Find out more](arduino_simplefoc_shield_showcase){: .btn .btn-docs}|[Find out more](https://github.com/simplefoc/SimpleFOC-DriveShield){: .btn .btn-docs}|[Find out more](simplefocmini){: .btn .btn-docs}|[Find out more](microspora_landing){: .btn .btn-docs}|

Other compatible boards are listed in the [supported hardware guide](supported_hardware), and community designs can be found on the [SimpleFOC forum](https://community.simplefoc.com/).



## Shield boards

These boards are designed to be compatible with the Arduino UNO R3 headers, enabling an easy to start experience with the <span class="simple">Simple<span class="foc">FOC</span>library</span> and the Arduino IDE. The boards can be used with any board with the standard Arduino headers, such as the Arduino MEGA, STM32 Nucleo boards, Adafruit Metro, ESP32 D1 R3, Arudino UNO R4 and many others. This format enables user to easily exchange the microcontrollers and find the best solution for their application. The boards are fully open-source and the fabrication files are available in the respective repositories, as well as detailed guides on how to fabricate the boards yourself. 

| Feature | <span id="simplefocshield"></span>[SimpleFOCShield v3.2](arduino_simplefoc_shield_showcase) | <span id="driveshield"></span>[SimpleFOC DriveShield v1.8](https://github.com/simplefoc/SimpleFOC-DriveShield) |
| --- | --- | --- |
| Board | <img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/master/images/top.png" style="height:250px" alt="SimpleFOCShield" /> | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/main/images/top.png" style="height:250px" alt="SimpleFOC DriveShield" /> |
| Motor driver | DRV8313 | DRV8320H gate driver with BSZ0904NSI MOSFETs |
| Current sensing | ACS712 inline sensor; ±5A measurement range | INA240 inline sensor; ±40A measurement range |
| Maximum current | 2A continuous; 3A peak | 20A continuous; 30A peak measured |
| Maximum input voltage | 35V | 30V |
| Onboard regulator | 8V regulator | 8V regulator (v1.8+) |
| Stackable | Yes | Yes |
| Encoder/Hall pullups | 3.3kΩ, configurable | 3.3kΩ, configurable |
| I2C pullups | 4.7kΩ, configurable | 4.7kΩ, configurable |
| Configurable pinout | Soldering pads | Soldering pads |
| Arduino headers | UNO, MEGA, STM32 Nucleo, and compatible boards | UNO, MEGA, STM32 Nucleo, and compatible boards |
| Additional connectors | - | STEMMA QT and SPI |
| Board size | 56 x 53 mm | 56 x 53 mm |
| Open-Source | [GitHub](https://github.com/simplefoc/Arduino-SimpleFOCShield) | [GitHub and fabrication files](https://github.com/simplefoc/SimpleFOC-DriveShield)|
|**Available**: | - | [Makerfabs](https://www.makerfabs.com/simplefoc-driveshield.html)|

For currents above 15A, the DriveShield may need a heatsink or thicker PCB copper. See its [thermal measurements](https://github.com/simplefoc/SimpleFOC-DriveShield#temperature-characteristics-study).

[Read more about SimpleFOC Shield board](arduino_simplefoc_shield_showcase){: .btn .btn-docs}

## Mini boards

This is a set of miniature boards designed to be small, low-cost, and easy to use. They are intended for low power applications and are designed to be compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>. The boards are created as minimal working examples and are intended to be used as a reference design for the community to build upon. The boards are fully open-source and the fabrication files are available in the respective repositories, as well as detailed guides on how to fabricate the boards yourself. 

<div class="width40 inline_block_top" markdown="1">
### <span class="simple">Simple<span class="foc">FOC</span>Mini</span> <small>v2.3</small> - <small>[Find out more](simplefocmini)</small> {#simplefocmini}
{: .no_toc }

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/simplefocmini)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/simplefocmini?color=blue)

Compact BLDC driver board fully compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>. Version 2 redesigned the board around the DRV8316 and added current and supply-voltage sensing while retaining the v1.1 PWM and enable pinout.


<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" class="img200" alt="SimpleFOCMini v2 board"/>




### Features
{: .no_toc }
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **DRV8316 based** - [datasheet](https://www.ti.com/lit/ds/symlink/drv8316.pdf)
   - Power supply: 5-35V
   - Max current: 8A
   - 3-PWM mode
- **Sensing**: Three-phase low-side current sensing and supply-voltage sensing
- **3.3V LDO**: Onboard, up to 20mA output
- **Small size**: 24x25 mm
- **Fully open-source**:
   - [EasyEDA](https://oshwlab.com/the.skuric/simplefocmini_copy_copy)
  - [GitHub](https://github.com/simplefoc/SimpleFOCMini) 
- **Available from**: [Makerfabs](https://www.makerfabs.com/simplefocmini.html)

[Read more about Mini board](simplefocmini){: .btn .btn-docs}


</div><div class="width40 inline_block_top" style  markdown="1">

### Additional Mini board: <span class="simple">Simple<span class="foc">FOC</span> <b>Step</b>Mini</span> <small>v1.0</small> - <small>[See on GitHub](https://github.com/simplefoc/SimpleFOC-StepMini)</small>
{: .no_toc }
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/simplefoc-stepmini)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/simplefoc-stepmini?color=blue)

Small package, low-cost Stepper driver board fully compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-StepMini/main/docs/top.png" class="img200"/>


### Features
{: .no_toc }
- **Plug & play**: In combination with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
- **DRV8844 based** - [datasheet](https://www.ti.com/lit/ds/symlink/drv8844.pdf)
    - Power supply: 8-35V
    - Max current: 2.5A per phase
    - Onboard 3.3V LDO 
        - up to 10mA 
        - Can power a sensor like AS5600 or CUI AMT102 
- **Small size:** 26x21 mm
- **Fully open-source:** 
   - [EasyEDA link](https://easyeda.com/the.skuric/simplefocmini_copy)
   - Project and fabrication files: [Github](https://github.com/simplefoc/SimpleFOC-StepMini)
- **Low-cost:** 
   - JLCPCB production cost ~3-5€
   - Will be available in the [shop](https://www.simplefoc.com/shop) 10-15€
</div>

## Integrated controller boards

### <span class="simple">Simple<span class="foc">FOC</span>-microspora</span> <small>v1.7</small> - <small>[Find out more](microspora_landing)</small> {#microspora}
{: .no_toc }

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/top.png" class="width40" alt="SimpleFOC-microspora board"/>

SimpleFOC-microspora combines a motor driver and microcontroller on one compact board. It is designed for low-power BLDC and stepper projects that benefit from an integrated controller, onboard magnetic encoder, and CAN connectivity.

- **MCU**: STM32G431CBU6
- **Motor driver**: DRV8316, 5-35V supply, up to 8A
- **Sensing**: MT6701 magnetic encoder, three-phase current sensing, and supply-voltage sensing
- **Connectivity**: CAN daisy-chain connectors, SPI, and a multi-purpose I2C/encoder/GPIO connector
- **Size**: 33 x 34 mm
- **Open hardware**: [EasyEDA project](https://oshwlab.com/the.skuric/microspora-simplefoc-antun)
- **Firmware and examples**: [SimpleFOC Microspora firmware](https://github.com/simplefoc/microspora_simplefoc_firmware)
- **Availability**: [Makerfabs](https://www.makerfabs.com/simplefoc-microspora.html)

[Read more about the Microspora board](microspora_landing){: .btn .btn-docs}