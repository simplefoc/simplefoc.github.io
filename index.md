---
layout: default
title: Home
nav_order: 1
description: "Arduino Simple Field Oriented Control (FOC) project documentation."
permalink: /
---
# Arduino Simple Field Oriented Control (FOC) project

![Library Compile](https://github.com/simplefoc/Arduino-FOC/workflows/Library%20Compile/badge.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![arduino-library-badge](https://www.ardu-badge.com/badge/Simple%20FOC.svg?)
[![status](https://joss.theoj.org/papers/4382445f249e064e9f0a7f6c1bb06b1d/status.svg)](https://joss.theoj.org/papers/4382445f249e064e9f0a7f6c1bb06b1d)

![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/arduino-foc)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/arduino-foc?color=blue)
![GitHub commits since tagged version](https://img.shields.io/github/commits-since/simplefoc/arduino-foc/latest/dev)
![GitHub commit activity (branch)](https://img.shields.io/github/commit-activity/m/simplefoc/arduino-foc/dev)

We live in very exciting times 😃! BLDC motors are entering the hobby community more and more and many great projects have already emerged leveraging their far superior dynamics and power capabilities. BLDC motors have numerous advantages over regular DC motors but they have one big disadvantage, the complexity of control. Even though it has become relatively easy to design and manufacture PCBs and create our own hardware solutions for driving BLDC motors the proper low-cost solutions are yet to come. One of the reasons for this is the apparent complexity of writing the BLDC driving algorithms, Field oriented control (FOC) being an example of one of the most efficient ones.
The solutions that can be found on-line are almost exclusively very specific for certain hardware configuration and the microcontroller architecture used.
Additionally, most of the efforts at this moment are still channeled towards the high-power applications of the BLDC motors and proper low-cost and low-power FOC supporting boards are very hard to find today and even may not exist. <br>
Therefore this is an attempt to: 
- 🎯 Demystify FOC algorithm and make a robust but simple Arduino library: [Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> ](arduino_simplefoc_library_showcase)
  - <i>Support as many <b>motor + sensor + current sense + driver + mcu</b> combinations out there</i>
- 🎯 Develop a modular FOC supporting BLDC driver boards:
   - ***NEW*** 📢: *Minimalistic* BLDC driver (<3Amps) :   [<span class="simple">Simple<b>FOC</b>Mini</span> ](simplefocmini).
   - *Low-power* gimbal driver (<5Amps) :   [Arduino <span class="simple">Simple<b>FOC</b>Shield</span> ](arduino_simplefoc_shield_showcase).
   - *Medium-power* BLDC driver (<30Amps): [Arduino <span class="simple">Simple<b>FOC</b>PowerShield</span> ](https://github.com/simplefoc/Arduino-SimpleFOC-PowerShield).

<blockquote class="info" markdown="1">
   <p class="heading">NEW RELEASE 📢: <span class="simple">Simple<span class="foc">FOC</span>library</span> v2.3.2 <a href="https://github.com/simplefoc/Arduino-FOC/releases/tag/v2.3.2">see release</a></p>
 - Improved [space vector modulation code](https://github.com/simplefoc/Arduino-FOC/pull/309) thanks to [@Candas1](https://github.com/Candas1)
 - Bugfix for stepper motor initialization
 - Bugfix for current sensing when only 2 phase currents available - please re-check your current sense PID tuning
 - Bugfix for teensy3.2 - [#321](https://github.com/simplefoc/Arduino-FOC/pull/321)
 - Added teensy3/4 compile to the github CI using platformio
 - Fix compile issues with recent versions of ESP32 framework
 - Add ADC calibration on STM32 MCUs thanks to [@Candas1](https://github.com/Candas1)
 - Bugfix for crash when using ADC2 on ESP32s - [thanks to @mcells](https://github.com/simplefoc/Arduino-FOC/pull/346)
 - Bugfix for renesas PWM on UNO R4 WiFi - [thanks to @facchinm](https://github.com/simplefoc/Arduino-FOC/pull/322)
 - And more bugfixes - see the complete list of 2.3.2 [fixes and PRs](https://github.com/simplefoc/Arduino-FOC/milestone/9?closed=1)
</blockquote>

## Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> <i><small>v2.3.1</small></i>
<iframe class="youtube"  src="https://www.youtube.com/embed/Y5kLeqTc6Zk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
This video demonstrates the Simple FOC library basic usage, electronic connections and shows its capabilities.

### Features
- **Easy install**: 
   - Arduino IDE: Arduino Library Manager integration
   - PlatformIO
- **Open-Source**: Full code and documentation available on github
- **Goal**: 
   - Support as many [sensor](position_sensors) + [motor](motors) + [driver](drivers) + [current sense](current_sense)   combination as possible.
   - Provide the up-to-date and in-depth documentation with API references and the examples
- **Easy to setup and configure**: 
   - Easy hardware configuration 
   - Each hardware component is a C++ object (easy to understand) 
   - Easy [tuning the control loops](motion_control)
   - [*Simple**FOC**Studio*](studio) configuration GUI tool
   - Built-in communication and monitoring
- **Cross-platform**:
   - Seamless code transfer from one microcontroller family to another 
   - Supports multiple [MCU architectures](microcontrollers):
      - Arduino: UNO, MEGA, DUE, Leonardo, Nano, UNO R4, MKR ....
      - STM32
      - ESP32
      - Teensy
      - many more ...


## Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> <i><small>v2.0.4</small></i>
<iframe class="youtube"  src="https://www.youtube.com/embed/G5pbo0C6ujE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Features
- **Plug & play**: In combination with Arduino *Simple**FOC**library* - [github](https://github.com/simplefoc/Arduino-FOC)
- **Low-cost**: Price of €15 - [Check the pricing](https://www.simplefoc.com/shop) 
- **In-line current sensing**: Up to 3Amps/5Amps bidirectional
   - configurable: 3.3Amps - 3.3V adc, 5Amps - 5V adc
- **Integrated 8V regulator**: 
   - Enable/disable by soldering pads
- **Max power 120W** - max current 5A, power-supply 12-35V
   - Designed for Gimbal motors with the internal resistance >10 Ωs. 
- **Stackable**: running 2 motors in the same time
- **Encoder/Hall sensors interface**: Integrated 3.3kΩ pullups (configurable)
- **I2C interface**: Integrated 4.7kΩ pullups (configurable)
- **Configurable pinout**: Hardware configuration - soldering connections
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo boards...
- **Open Source**: Fully available fabrication files - [how to make it yourself](arduino_simplefoc_shield_fabrication)

##### If you are interested in this board as a product, find more information on this link: [Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>](https://simplefoc.com/simplefoc_shield_product)


<p><img src="extras/Images/simple_foc_shield_v13_small.gif" class="img200" ><img src="https://simplefoc.com/assets/img/v1.jpg" class="img200 img_half" ><img src="https://simplefoc.com/assets/img/v2.jpg" class="img200 img_half" ></p>

## Alternative FOC supporting projects
These are just a few of the alternative FOC supporting projects which provide hardware and software solutions. 

<a href="https://odriverobotics.com/" >Odrive</a> | <a href="https://www.youtube.com/watch?v=g2BHEdvW9bU">Trinamic</a> | <a href="https://www.infineon.com/cms/en/product/evaluation-boards/bldc_shield_tle9879/" >Infineon</a> | <a href="https://github.com/gouldpa/FOC-Arduino-Brushless">FOC-Arduino-Brushless</a>
------------ | ------------- | ------------ | -------------
<img src="https://images.squarespace-cdn.com/content/v1/58aff26de4fcb53b5efd2f02/1523147803002-0OYG383CVIPARMB6Y9IT/ODrive_v34%400%2C5x.jpg?format=500w" style="width:100%;max-width:250px"  > | <img src="https://i3.ytimg.com/vi/g2BHEdvW9bU/maxresdefault.jpg" style="width:100%;max-width:250px"  > | <img src="https://www.infineon.com/export/sites/default/_images/product/evaluation-boards/BLDC_Motor_Shild_with_TLE9879QXA40.jpg_1711722916.jpg" style="width:100%;max-width:250px"  >| <img src="https://hackster.imgix.net/uploads/attachments/998086/dev_kit_89eygMekks.jpg?auto=compress%2Cformat&w=1280&h=960&fit=max" style="width:100%;max-width:250px"  >
✔️ Open Source | ❌ Open Source | ✔️ Open Source(recently) | ✔️ Open Source
✔️Simple to use | ✔️ Simple to use | ✔️Simple to use | ❌ Simple to use
❌ Low cost ($100) | ❌ Low cost ($100) | ✔️Low cost ($40) | ✔️ Low cost
❌ Low power (>50A) | ✔️ Low power  | ✔️  Low power | ✔️ Low power
❌ Stepper support | ❌ Stepper support | ❌ Stepper support | ❌ Stepper support
