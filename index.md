---
layout: default
title: Home
nav_order: 1
description: "Arduino Simple Field Oriented Control (FOC) project documentation."
permalink: /
---
# Arduino Simple Field Oriented Control (FOC) project

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
- 🎯 Demystify FOC algorithm and make a robust but simple Arduino library: [Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>](https://docs.simplefoc.com/arduino_simplefoc_library_showcase)
  - <i>Support as many <b>motor + sensor + driver + mcu</b> combinations out there</i>
- 🎯 Develop modular and easy to use FOC supporting BLDC driver boards
   - For official driver boards see [<span class="simple">Simple<span class="foc">FOC</span>Boards</span>](https://docs.simplefoc.com/boards)
   - Many many more boards developed by the community members, see [<span class="simple">Simple<span class="foc">FOC</span> Community</span>](https://community.simplefoc.com/)

<blockquote class="info" markdown="1">
   <p class="heading">NEW RELEASE 📢: <span class="simple">Simple<span class="foc">FOC</span>library</span> v2.3.3 <a href="https://github.com/simplefoc/Arduino-FOC/releases/tag/v2.3.3">see release</a></p>
 - STM32 MCUs
   - support for center aligned pwm (even across multiple timers and motors/drivers) [#374](https://github.com/simplefoc/Arduino-FOC/pull/374), [#388](https://github.com/simplefoc/Arduino-FOC/pull/388)
   - support for DMA based low-side current sensing: [#383](https://github.com/simplefoc/Arduino-FOC/pull/383),[#378](https://github.com/simplefoc/Arduino-FOC/pull/378)
   - support for F7 architecture [#388](https://github.com/simplefoc/Arduino-FOC/pull/388),[#394](https://github.com/simplefoc/Arduino-FOC/pull/394)
- Teensy4 MCUs
   - support for low-side current sensing [#392](https://github.com/simplefoc/Arduino-FOC/pull/392)
   - support for center aligned 6pwm and 3pwm (optional) [#392](https://github.com/simplefoc/Arduino-FOC/pull/392)
 - KV rating calculation fix [#347](https://github.com/simplefoc/Arduino-FOC/pull/347)
 - Much more performant Park/Clarke calculation  [#340](https://github.com/simplefoc/Arduino-FOC/pull/340)
 - And much more - see the complete list of bugfixes and new features of v2.3.3 [fixes and PRs](https://github.com/simplefoc/Arduino-FOC/milestone/10?closed=1)
 - Docs
   - Added a practical guides section - [see the docs](digging_deeper)
   - Added a guide to choosing PWM pins for your application - [see the docs](choosing_pwm_pins)
   - Added docs on stm32 PWM and ADC pinouts - [ <i class="fa fa-sm fa-book"></i> stm32 pinouts](https://docs.simplefoc.com/stm32pinouts/)
</blockquote>

## Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
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
Over the period of the past few years several cool FOC supporting projects which provide hardware and software solutions have emerged, here are some of them. None of the projects listed uses <span class="simple">Simple<span class="foc">FOC</span></span>. 



Project | Open Source Hardware | Open source firmware | Simple to use | Low cost | Power rating | Stepper Support 
--- | --- | --- | --- | ---  | ---  | --- 
<img src="https://images.squarespace-cdn.com/content/v1/58aff26de4fcb53b5efd2f02/1523147803002-0OYG383CVIPARMB6Y9IT/ODrive_v34%400%2C5x.jpg?format=500w" style="width:100%;max-width:100px"  > <br><a href="https://odriverobotics.com/" >Odrive</a> | ✔️/(❌ from recently) |  ✔️/(❌ from recently) |  ✔️ |  ❌ (>200$) | High >50A | ❌
<img src="extras/Images/vesc.jpg" style="width:100%;max-width:100px"  > <br><a href="https://github.com/vedderb/bldc">Vesc </a> | ✔️ |  ✔️ |  ✔️ |  ❌ (>100$) | Vey High >100A | ❌
<img src="https://i3.ytimg.com/vi/g2BHEdvW9bU/maxresdefault.jpg" style="width:100%;max-width:100px"  ><br><a href="https://www.youtube.com/watch?v=g2BHEdvW9bU">Trinamic</a> | ❌ | ❌ | ✔️ |  ❌ (>200$) | Low ~10A | ✔️
<img src="https://www.infineon.com/export/sites/default/_images/product/evaluation-boards/BLDC_Motor_Shild_with_TLE9879QXA40.jpg_1711722916.jpg" style="width:100%;max-width:100px"  ><br><a href="https://www.infineon.com/cms/en/product/evaluation-boards/bldc_shield_tle9879/" >Infineon</a> |  ✔️ | ❌ | ✔️ | ✔️ (50$) | Low ~10A | ❌
<img src="https://hackster.imgix.net/uploads/attachments/998086/dev_kit_89eygMekks.jpg?auto=compress%2Cformat&w=1280&h=960&fit=max" style="width:100%;max-width:100px"  ><br><a href="https://github.com/gouldpa/FOC-Arduino-Brushless">FOC-Arduino-Brushless</a>  | ✔️ | ✔️ | ❌ |✔️ (Price ?) | Low ~10A |  ❌
<img src="https://tinymovr.com/cdn/shop/files/DSC_0940.jpg?v=1696112543&width=713" style="width:100%;max-width:100px"  ><br><a href="https://tinymovr.com/">Tinymovr R5.2</a> | ❌ | ✔️|✔️ | ❌ (~90$)  | High (~30A) |  ❌
<img src="https://tinymovr.com/cdn/shop/products/DSC_0886.jpg?v=1678819186&width=713" style="width:100%;max-width:100px"  ><br><a href="https://tinymovr.com/">Tinymovr M5.2</a> | ❌ | ✔️ |✔️ | ❌ (~90$) | Low (~6Amps) |  ❌
<img src="https://mjbots.com/cdn/shop/files/20240410-moteus_c1_r12-front.jpg?v=1712841091&width=750" style="width:100%;max-width:100px"> <br><a href="https://mjbots.com/">Mjbots moteus</a> | ✔️ | ✔️ |✔️|  ❌ (70-160$)  | Mid (20Amps) to High (100Amps) |  ❌
<img src="https://raw.githubusercontent.com/open-dynamic-robot-initiative/open_robot_actuator_hardware/master/electronics/micro_driver_electronics/images/micro_driver_v2_1.jpg" style="width:100%;max-width:100px"> <br><br><a href="https://github.com/open-dynamic-robot-initiative/open-motor-driver-initiative">Open robotics initiative <br> MicroDriver</a> | ✔️ | ✔️ |❌ | ✔️(~50$)  | Mid (-20Amps) |  ❌
<img src="https://www.solomotorcontrollers.com/wp-content/uploads/2023/08/SOLO-MINI.png" style="width:100%;max-width:100px"> <br><br><a href="https://www.solomotorcontrollers.com/shop/">SOLO</a> | ❌ | ❌ |✔️ | ❌(70$-600$)  | Mid (-16Amps) to Very high (-120Amps) |  ❌


<blockquote class="warning">
⚠️ This list is certainly not exhaustive, feel free to contribute to extending/completing/correcting it! 
</blockquote>



