---
layout: default
title: Home
nav_order: 1
description: "Arduino Simple Field Oriented Control (FOC) project documentation."
permalink: /
---
# Arduino Simple Field Oriented Control (FOC) project

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![arduino-library-badge](https://www.ardu-badge.com/badge/Simple%20FOC.svg)
![PlatformIO Registry](https://badges.registry.platformio.org/packages/askuric/library/Simple%20FOC.svg)
[![status](https://joss.theoj.org/papers/4382445f249e064e9f0a7f6c1bb06b1d/status.svg)](https://joss.theoj.org/papers/4382445f249e064e9f0a7f6c1bb06b1d)

![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/arduino-foc)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/arduino-foc?color=blue)
![GitHub commits since tagged version](https://img.shields.io/github/commits-since/simplefoc/arduino-foc/latest/dev)
![GitHub commit activity (branch)](https://img.shields.io/github/commit-activity/m/simplefoc/arduino-foc/dev)

We live in very exciting times 😃! BLDC motors are entering the hobby community more and more and many great projects have already emerged leveraging their far superior dynamics and power capabilities. These motors have numerous advantages over regular DC motors but they have one big disadvantage, the complexity of control. Even though it has become relatively easy to design and manufacture PCBs and create our own hardware solutions for driving BLDC motors, the proper low-cost solutions have been challenging to develop. One of the reasons for this is the apparent complexity of writing the driving algorithms, Field Oriented Control (FOC) being one of the most efficient ones.
The solutions that can be found online are almost exclusively very specific for certain hardware configurations and microcontroller architectures. Similar to BLDCs, Stepper motors can hugely benefit from FOC control, but the solutions for FOC controlled stepper motors are even more scarce.
<br>
Therefore this is an attempt to: 
- 🎯 Demystify FOC algorithm and make a robust but simple Arduino library: [Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>](https://docs.simplefoc.com/arduino_simplefoc_library_showcase)
  - <i>Support as many <b>motor + sensor + driver + mcu</b> combinations out there</i>
  - Make transitioning from one hardware combination to another as seamless as possible
- 🎯 Develop modular and easy to use FOC supporting driver boards
   - For official driver boards see [<span class="simple">Simple<span class="foc">FOC</span>Boards</span>](https://docs.simplefoc.com/boards)
   - Many many more boards developed by the community members, see [<span class="simple">Simple<span class="foc">FOC</span> Community</span>](https://community.simplefoc.com/)

<blockquote class="info" markdown="1">
   <p class="heading">NEW RELEASE 📢: <span class="simple">Simple<span class="foc">FOC</span>library</span> v2.4.0 <a href="https://github.com/simplefoc/Arduino-FOC/releases/tag/v2.4.0">see release</a></p>

 - STM32
   - Added support for ADC reads in addition to Lowside current sense [#506](https://github.com/simplefoc/Arduino-FOC/pull/506) - [see in docs](regular_adc_read)
   - Added support for multiple motors low-side CS (one per ADC) with ADC current sensing [#503](https://github.com/simplefoc/Arduino-FOC/pull/503)
   - BG341 low-side current sense sync was lost in v2.3.5 - fixed [#482](https://github.com/simplefoc/Arduino-FOC/pull/482)
 - ESP32 
   - Many ESP32 safety optimisations by [@uLipe](https://github.com/uLipe): [#490](https://github.com/simplefoc/Arduino-FOC/pull/490),[#491](https://github.com/simplefoc/Arduino-FOC/pull/491),[#492](https://github.com/simplefoc/Arduino-FOC/pull/492),[#493](https://github.com/simplefoc/Arduino-FOC/pull/493),[#495](https://github.com/simplefoc/Arduino-FOC/pull/495)
   - Better ADC-Timer alignement for more stable current sensing [See this commit](https://github.com/simplefoc/Arduino-FOC/commit/877699b4db4e6e3ecc16b16cc4337af928e746f4)
   - Now compiles for all v3.x arduino-esp32 versions (v2.3.5 was compatible with v3.2.x) 
   - `adcRead` small refactor - no more magic numbers
 - Others
   - Teensy4 support for phase state setting [#498](https://github.com/simplefoc/Arduino-FOC/pull/498) by [@Ragiton](https://github.com/Ragiton)
   - Added support for Arduino Nano Matter board by [@silabs-szabog](https://github.com/silabs-szabog) : [#485](https://github.com/simplefoc/Arduino-FOC/pull/484)
 - **Major New features**
    - Add current and voltage feed forward terms to motor classes by [@Copper280z](https://github.com/Copper280z) in [#454](https://github.com/simplefoc/Arduino-FOC/pull/454)
    - Velocity Calculation rework by [@Copper280z](https://github.com/Copper280z) in [#45](https://github.com/simplefoc/Arduino-FOC/pull/45)
    - Motion control - [docs](https://docs.simplefoc.com/motion_control)
       - Added `custom` motion control mode - see in [docs](https://docs.simplefoc.com/custom_control) and in [examples](https://github.com/simplefoc/Arduino-FOC/tree/master/examples/motion_control)
       - Added `angle_nocascade` control mode for position control without velocity cascade - see in [docs](https://docs.simplefoc.com/angle_loop)  - [#384](https://github.com/simplefoc/Arduino-FOC/pull/384)
       - **Now all the closed and open loop modes can be used with any torque control modes (voltage and current)** 
           - Ex. `velocity_openloop` with `foc_current` torque control
   - Torque control - [docs](https://docs.simplefoc.com/torque_control)
      - Added `estimated_current` torque control mode for model-based current estimation without current sensing - see in [docs](https://docs.simplefoc.com/estimated_current_mode) 
      - Now we can easily switch between voltage and estimated current control
 - Docs updates - see [docs](https://docs.simplefoc.com/)
     - Write the code page updated - see [docs](https://docs.simplefoc.com/code)
     - Better step-by-step setup guides - see [docs](https://docs.simplefoc.com/example_from_scratch)
     - New motion control documentation - see [docs](https://docs.simplefoc.com/motion_control)
     - New torque/FOC control documentation - see [docs](https://docs.simplefoc.com/torque_control)
     - Theory corner updated and extended - see [docs](https://docs.simplefoc.com/theory_corner)
     - Library source updated and extended - see [docs](https://docs.simplefoc.com/source_code)
     - Practical guides updated and extended - see [docs](https://docs.simplefoc.com/practical_guides)
       - Motor parameter measurement guide - see [docs](https://docs.simplefoc.com/practical_guides#motor-parameters-and-characterization)
       - PID tuning guides - see [docs](https://docs.simplefoc.com/practical_guides#pid-tuning)
 - Examples
   - `align_current_sense.ino` example added to the `examples/utils/current_sense_test` allowing to verify the alignment between the driver and the current sense phases 

 - Changelog
    - See the release notes for more details: [see release](https://github.com/simplefoc/Arduino-FOC/releases)
    - See the milestones: [v2.3.6 (unreleased)](https://github.com/simplefoc/Arduino-FOC/milestone/14) and [v2.4.0](https://github.com/simplefoc/Arduino-FOC/milestone/13)
</blockquote>

# Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> <small>- [Read more ...](arduino_simplefoc_library_showcase)</small>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/arduino-foc)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/arduino-foc?color=blue)

<iframe class="youtube"  src="https://www.youtube.com/embed/Y5kLeqTc6Zk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This video is a bit outdated but it demonstrates the *Simple**FOC**library* basic usage, electronic connections and shows its capabilities.

### Features
- **Easy install**: 
   - Arduino IDE: Arduino Library Manager integration
   - PlatformIO
- **Open-Source**: Full code and documentation available on github
- **Goal**: 
   - Support as many [sensor](position_sensors) + [motor](motors) + [driver](drivers) + [current sense](current_sense) combinations as possible
  - Make transitioning from one hardware combination to another as seamless as possible
   - Provide up-to-date and in-depth documentation with API references and examples
- **Easy to setup and configure**: 
   - Easy hardware configuration 
   - Each hardware component is a C++ object (easy to understand) 
   - Easy [tuning the control loops](motion_control)
   - Advanced control features: velocity and current feed-forward, improved velocity calculation
   - [*Simple**FOC**Studio*](studio) configuration GUI tool for real-time tuning and monitoring
   - Built-in communication and monitoring via Serial, I2C, or custom protocols
- **Cross-platform**:
   - Seamless code transfer from one microcontroller family to another 
   - Supports multiple [MCU architectures](microcontrollers):
      - Arduino: UNO R4, UNO, MEGA, DUE, Leonardo, Nano, Nano33, MKR ....
      - STM32 (Nucleo, Bluepill, B-G431B-ESC1, H7 family, etc.)
      - ESP32 (ESP32, ESP32-S2, ESP32-S3, ESP32-C3, ESP32-C6)
      - Teensy (3.x, 4.x)
      - RP2040/RP2350 (Raspberry Pi Pico)
      - SAMD (Arduino Zero, MKR boards)
      - MBED (Portenta, Nano 33 BLE)
      - Silabs
      - many more ...


# <span class="simple">Simple<span class="foc">FOC</span>Boards</span>  <small>- [Read more ...](boards)</small>

One of the goals of the  <span class="simple">Simple<span class="foc">FOC</span>project</span> is to develop low-cost easy to use BLDC driver boards compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>and completely open source! Therefore, <span class="simple">Simple<span class="foc">FOC</span></span> team members have developed a set of boards, designed specifically for ease of use, to help you kickstart your FOC journey. In addition to being easy to use, the goal of these boards is serve as a reference design for the community to build upon. And finally, even though some of these boards are available in our [shop](https://www.simplefoc.com/shop), our docs provide a lot of documentation and step-by-step guides on how to fabricate the boards yourself.


In addition to the official boards, there are many other boards compatible with <span class="simple">Simple<span class="foc">FOC</span>library</span>  that you can explore, see the [docs](supported_hardware). Additionally, some other cool hardware designs have been proposed by the community. Check out our [community forum](https://community.simplefoc.com/) for more info.

Here are some of the official boards developed by the <span class="simple">Simple<span class="foc">FOC</span></span> team:

<div class="width40 inline_block_top" markdown="1">

## <span class="simple">Simple<span class="foc">FOC</span>Shield</span>

<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/master/images/top.png"  class="img200"/>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/arduino-simplefocshield)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/arduino-simplefocshield?color=blue)

### Features
- **DRV8313 based** - [datasheet](https://www.ti.com/lit/ds/symlink/drv8313.pdf)
  - Power supply: 8-35V
  - Max current: 2A per phase (3Amp peak)
- **Absolute max ratings** - Designed for Gimbal motors with the internal resistance >10 Ωs. 
   - Max current: 3A, 
   - Max input voltage: 35V
- **In-line current sensing**: Up to 5Amps bidirectional
   - ACS712 hall current sensor
- **Integrated 8V regulator**: 
   - Enable/disable by soldering pads
- **Stackable**: running 2 motors in the same time
- **Encoder/Hall sensors interface**: Integrated 3.3kΩ pullups (configurable)
- **I2C interface**: Integrated 4.7kΩ pullups (configurable)
- **Configurable pinout**: Hardware configuration - soldering connections
- **Arduino headers**: Arduino UNO, Arduino MEGA, STM32 Nucleo boards...
- **Open Source**: 
   - Fully designed in **EasyEDA**: [EasyEDA project](https://oshwlab.com/the.skuric/simplefocshield_copy_copy)
   - Fully available fabrication files - [how to make it yourself](arduino_simplefoc_shield_fabrication)
- **Low-cost**: 
   - JLCPCB production cost ~10-15€
   - Available in the [shop](https://www.simplefoc.com/shop): 15-30€ 

</div><div class="width40 inline_block_top" style  markdown="1">

## <span class="simple">Simple<span class="foc">FOC</span>Mini</span>

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" class="img200"/>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/simplefocmini)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/simplefocmini?color=blue)

### Features
- **DRV8313 based** - [datasheet](https://www.ti.com/lit/ds/symlink/drv8313.pdf)
  - Power supply: 8-35V
  - Max current: 2A per phase (3Amp peak)
- **Absolute max ratings** - Designed for Gimbal motors with the internal resistance >10 Ωs. 
   - Max current: 3A, 
   - Max input voltage: 35V
- **Small size**: 26x21 mm
- **Open Source**: 
   - Fully designed in **EasyEDA**: [EasyEDA project](https://easyeda.com/the.skuric/simplefocmini)
   - Fully available fabrication files - [how to make it yourself](mini_fabrication)
- **Low-cost**: 
   - JLCPCB production cost ~3-5€
   - Available in the [shop](https://www.simplefoc.com/shop): 7-15€ 

</div>

A short demo video for the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> (its a bit outdated but still relevant)

<iframe class="youtube"  src="https://www.youtube.com/embed/G5pbo0C6ujE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Find out more about the boards and how to make them yourself in the [boards](boards) section. 

## Alternative FOC supporting projects

The focus of <span class="simple">Simple<span class="foc">FOC</span></span> is making the FOC simple (:D) and supporting as many motor + sensor + driver + mcu combinations as possible. If you are a bit less concerned with the firmware *simplicity* and don't mind trading-off cross-platform flexibility for performance, there are several other projects out there that also use FOC and could provide a more compact, more prefromant or more robust solutions for your applications. Many of these projects are open-source and have been our inspiration from the start, especially VESC and Odrive. Make sure to check them out if you are starting your FOC journey!

P.S. None of these projects uses <span class="simple">Simple<span class="foc">FOC</span>library</span>, but <span class="simple">Simple<span class="foc">FOC</span>library</span> can be used as a firware for several driver boards developed in these projects.

Project | Open Source Hardware | Open source firmware | Simple to use | Low cost | Power rating | Stepper Support | SimpleFOC support
--- | --- | --- | --- | ---  | ---  | --- | --- 
<img src="https://images.squarespace-cdn.com/content/v1/58aff26de4fcb53b5efd2f02/1523147803002-0OYG383CVIPARMB6Y9IT/ODrive_v34%400%2C5x.jpg?format=500w" style="width:100%;max-width:100px"  > <br><a href="https://odriverobotics.com/" >Odrive</a> | ✔️/(❌ from recently) |  ✔️/(❌ from recently) |  ✔️ |  ❌ (>200$) | High >50A | ❌ |  ✔️
<img src="extras/Images/vesc.jpg" style="width:100%;max-width:100px"  > <br><a href="https://github.com/vedderb/bldc">Vesc </a> | ✔️ |  ✔️ |  ✔️ |  ❌ (>100$) | Vey High >100A | ❌ |  ✔️
<img src="https://i3.ytimg.com/vi/g2BHEdvW9bU/maxresdefault.jpg" style="width:100%;max-width:100px"  ><br><a href="https://www.youtube.com/watch?v=g2BHEdvW9bU">Trinamic</a> | ❌ | ❌ | ✔️ |  ❌ (>200$) | Low ~10A | ✔️ | ❌
<img src="https://www.infineon.com/export/sites/default/_images/product/evaluation-boards/BLDC_Motor_Shild_with_TLE9879QXA40.jpg_1711722916.jpg" style="width:100%;max-width:100px"  ><br><a href="https://www.infineon.com/cms/en/product/evaluation-boards/bldc_shield_tle9879/" >Infineon</a> |  ✔️ | ❌ | ✔️ | ✔️ (50$) | Low ~10A | ❌ | ❌
<img src="https://hackster.imgix.net/uploads/attachments/998086/dev_kit_89eygMekks.jpg?auto=compress%2Cformat&w=1280&h=960&fit=max" style="width:100%;max-width:100px"  ><br><a href="https://github.com/gouldpa/FOC-Arduino-Brushless">FOC-Arduino-Brushless</a>  | ✔️ | ✔️ | ❌ |✔️ (Price ?) | Low ~10A |  ❌ | ? (not tested)
<img src="https://tinymovr.com/cdn/shop/files/DSC_0940.jpg?v=1696112543&width=713" style="width:100%;max-width:100px"  ><br><a href="https://tinymovr.com/">Tinymovr R5.2</a> | ❌ | ✔️|✔️ | ❌ (~90$)  | High (~30A) |  ❌ | ? (not tested)
<img src="https://tinymovr.com/cdn/shop/products/DSC_0886.jpg?v=1678819186&width=713" style="width:100%;max-width:100px"  ><br><a href="https://tinymovr.com/">Tinymovr M5.2</a> | ❌ | ✔️ |✔️ | ❌ (~90$) | Low (~6Amps) |  ❌ | ? (not tested)
<img src="https://mjbots.com/cdn/shop/files/20240410-moteus_c1_r12-front.jpg?v=1712841091&width=750" style="width:100%;max-width:100px"> <br><a href="https://mjbots.com/">Mjbots moteus</a> | ✔️ | ✔️ |✔️| ❌ (70-160$) | Mid (20Amps)<br> to<br> High (100Amps) | ❌ | ? (not tested)
<img src="https://raw.githubusercontent.com/open-dynamic-robot-initiative/open_robot_actuator_hardware/master/electronics/micro_driver_electronics/images/micro_driver_v2_1.jpg" style="width:100%;max-width:100px"> <br><br><a href="https://github.com/open-dynamic-robot-initiative/open-motor-driver-initiative">Open robotics initiative <br> MicroDriver</a> | ✔️ | ✔️ |❌ | ✔️(~50$)  | Mid (~20Amps) |  ❌ | ? (not tested)
<img src="https://www.solomotorcontrollers.com/wp-content/uploads/2023/08/SOLO-MINI.png" style="width:100%;max-width:100px"> <br><br><a href="https://www.solomotorcontrollers.com/shop/">SOLO</a> | ❌ | ❌ |✔️ | ❌(70$-600$)  | Mid (~16Amps) <br>to <br> Very high (~120Amps) |  ❌ |  ❌ 


<blockquote class="warning">
⚠️ This list is certainly not exhaustive, feel free to contribute to extending/completing/correcting it! 
</blockquote>



