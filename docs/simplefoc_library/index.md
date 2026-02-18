---
layout: default
title: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 3
permalink: /arduino_simplefoc_library_showcase
has_children: True
has_toc: False
toc: true
---



# Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![arduino-library-badge](https://www.ardu-badge.com/badge/Simple%20FOC.svg)
![PlatformIO Registry](https://badges.registry.platformio.org/packages/askuric/library/Simple%20FOC.svg)
[![status](https://joss.theoj.org/papers/4382445f249e064e9f0a7f6c1bb06b1d/status.svg)](https://joss.theoj.org/papers/4382445f249e064e9f0a7f6c1bb06b1d)


[![AVR build](https://github.com/simplefoc/Arduino-FOC/actions/workflows/arduino.yml/badge.svg)](https://github.com/simplefoc/Arduino-FOC/actions/workflows/arduino.yml)
[![STM32 build](https://github.com/simplefoc/Arduino-FOC/actions/workflows/stm32.yml/badge.svg)](https://github.com/simplefoc/Arduino-FOC/actions/workflows/stm32.yml)
[![ESP32 build](https://github.com/simplefoc/Arduino-FOC/actions/workflows/esp32.yml/badge.svg)](https://github.com/simplefoc/Arduino-FOC/actions/workflows/esp32.yml)
[![RP2040 build](https://github.com/simplefoc/Arduino-FOC/actions/workflows/rpi.yml/badge.svg)](https://github.com/simplefoc/Arduino-FOC/actions/workflows/rpi.yml)
[![SAMD build](https://github.com/simplefoc/Arduino-FOC/actions/workflows/samd.yml/badge.svg)](https://github.com/simplefoc/Arduino-FOC/actions/workflows/samd.yml)
[![Teensy build](https://github.com/simplefoc/Arduino-FOC/actions/workflows/teensy.yml/badge.svg)](https://github.com/simplefoc/Arduino-FOC/actions/workflows/teensy.yml)
[![MBED build](https://github.com/simplefoc/Arduino-FOC/actions/workflows/arduino_mbed.yml/badge.svg)](https://github.com/simplefoc/Arduino-FOC/actions/workflows/arduino_mbed.yml)


![GitHub release (latest by date)](https://img.shields.io/github/v/release/simplefoc/arduino-foc)
![GitHub Release Date](https://img.shields.io/github/release-date/simplefoc/arduino-foc?color=blue)
![GitHub commits since tagged version](https://img.shields.io/github/commits-since/simplefoc/arduino-foc/latest/dev)
![GitHub commit activity (branch)](https://img.shields.io/github/commit-activity/m/simplefoc/arduino-foc/dev)

This Arduino library implements Field Oriented Control (FOC) algorithm for BLDC, Stepper, and Hybrid Stepper motors. FOC algorithm produces incomparably smooth operation and high degree of torque, velocity and position control.
The library is intended both for: 
- Beginners searching for a simple and *user-friendly* way to learn how to control BLDC and Stepper motors 
- Advanced users ready to dig deeper into the FOC algorithm and optimize the code for their particular application/hardware

### Features
- **Easy install**: 
   - Arduino IDE: Arduino Library Manager integration
   - PlatformIO
- **Open-Source**: Full code and documentation available on GitHub
- **Goal**: 
   - Support as many [sensor](position_sensors) + [motor](motors) + [driver](drivers) + [current sense](current_sense) combinations as possible
   - Support for **BLDC motors**, **Stepper motors**, and **Hybrid Stepper motors**
   - Inline current sensing with low-side and high-side configurations
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
      - Arduino: UNO R4, UNO, MEGA, DUE, Leonardo, Nano, Nano33 ....
      - STM32 (Nucleo, Bluepill, B-G431B-ESC1, etc.)
      - ESP32
      - Teensy
      - RP2040 (Raspberry Pi Pico)
      - SAMD (Arduino Zero, MKR boards)
      - MBED (Portenta, Nano 33 BLE)
      - Silabs
      - many more ...
      
## YouTube demonstration videos
<iframe class="youtube" src="https://www.youtube.com/embed/Y5kLeqTc6Zk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This video demonstrates <span class="simple">Simple<span class="foc">FOC</span>library</span> basic usage, electronic connections and shows its basic capabilities.

**The video hardware setup consists of:**
<ul class="width60">
<li> 
<b>HMBGC V2.2 board </b> <a class="pull-right" href="https://www.ebay.com/itm/HMBGC-V2-0-3-Axle-Gimbal-Controller-Control-Plate-Board-Module-with-Sensor/351497840990?hash=item51d6e7695e:g:BAsAAOSw0QFXBxrZ">Ebay link <span class="fa fa-link"></span></a>
</li>
<li>
AMT 103 CUI Encoder 2048ppr <a class="pull-right" href="https://www.mouser.fr/ProductDetail/CUI-Devices/AMT103-V?qs=%2Fha2pyFaduivK%252B0pk7%2Fn5JVYn0KI22hXp9BVM%2FOAA64YDfmI%2FUQlRWDW0CMgz3WfQ6GDou4mx58%3D">Mouser link <span class="fa fa-link"></span></a>
</li>
<li>
BLDC Gimbal Motor  <a class="pull-right" href="https://fr.aliexpress.com/item/32483131130.html?spm=a2g0o.productlist.0.0.6ddd749fFd3u9E&algo_pvid=a67f2ec1-5341-4f97-ba3e-720e24f6c4fb&algo_expid=a67f2ec1-5341-4f97-ba3e-720e24f6c4fb-10&btsid=0b0a187915885172220541390e7eed&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_">Aliexpress link <span class="fa fa-link"></span></a>
</li>
</ul> 

<iframe class="youtube" src="https://www.youtube.com/embed/RI4nNMF608I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This video demonstrates <span class="simple">Simple<span class="foc">FOC</span>library</span> support for high-performance BLDC drivers such as DRV8302 and the support for Arudino and STM32 MCU architectures. 

**The video hardware setup consists of:**
<ul class="width60">
<li>
<b>DRV8302 driver board</b> <a class="pull-right" href="https://bit.ly/2BZZ5fG">Aliexpress link <span class="fa fa-link"></span></a>
</li>
<li> 
Arduino UNO <a class="pull-right" href="https://www.ebay.com/itm/HMBGC-V2-0-3-Axle-Gimbal-Controller-Control-Plate-Board-Module-with-Sensor/351497840990?hash=item51d6e7695e:g:BAsAAOSw0QFXBxrZ">Ebay link <span class="fa fa-link"></span></a>
</li>
<li> 
Nucleo-64 F401RE<a class="pull-right" href="https://www.mouser.fr/ProductDetail/STMicroelectronics/NUCLEO-F401RE?qs=fK8dlpkaUMvGeToFJ6rzdA%3D%3D">Mouser link <span class="fa fa-link"></span></a>
</li>
<li>
USA-DIGITAL E3-8192 Encoder 8192ppr  <a class="pull-right" href="https://www.usdigital.com/products/encoders/incremental/kit/E3">USA Digital link <span class="fa fa-link"></span></a>
</li>
<li>
BLDC Gimbal Motor GBM5108-120T <a class="pull-right" href="https://www.onedrone.com/store/ipower-gbm5108-120t-gimbal-motor.html">iPower store <span class="fa fa-link"></span></a>
</li>
</ul> 

<iframe class="youtube" src="https://www.youtube.com/embed/xTlv1rPEqv4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This video demonstrates <span class="simple">Simple<span class="foc">FOC</span>library</span> support for Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> and using multiple motors with Arduino UNO. The project examples are based on bidirectional haptic control. 

**The video hardware setup consists of:**
<ul class="width60">
<li>
<b>Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span></b> <a class="pull-right" href="arduino_simplefoc_shield_showcase">More info <span class="fa fa-link"></span></a>
</li>
<li> 
Arduino UNO <a class="pull-right" href="https://www.ebay.com/itm/HMBGC-V2-0-3-Axle-Gimbal-Controller-Control-Plate-Board-Module-with-Sensor/351497840990?hash=item51d6e7695e:g:BAsAAOSw0QFXBxrZ">Ebay link <span class="fa fa-link"></span></a>
</li>
<li> 
Nucleo-64 F401RE<a class="pull-right" href="https://www.mouser.fr/ProductDetail/STMicroelectronics/NUCLEO-F401RE?qs=fK8dlpkaUMvGeToFJ6rzdA%3D%3D">Mouser link <span class="fa fa-link"></span></a>
</li>
<li> 
AS5600  <a class="pull-right" href="https://www.ebay.com/itm/1PC-New-AS5600-magnetic-encoder-sensor-module-12bit-high-precision/303401254431?hash=item46a41fbe1f:g:nVwAAOSwTJJd8zRK">Ebay link <span class="fa fa-link"></span></a>
</li>
<li>
AMT 103 CUI <a class="pull-right" href="https://www.mouser.fr/ProductDetail/CUI-Devices/AMT103-V?qs=%2Fha2pyFaduivK%252B0pk7%2Fn5JVYn0KI22hXp9BVM%2FOAA64YDfmI%2FUQlRWDW0CMgz3WfQ6GDou4mx58%3D">Mouser link <span class="fa fa-link"></span></a>
</li>
<li>
USA-DIGITAL E3-8192  <a class="pull-right" href="https://www.usdigital.com/products/encoders/incremental/kit/E3">USA Digital link <span class="fa fa-link"></span></a>
</li>
<li>
GBM5108-120T <a class="pull-right" href="https://www.onedrone.com/store/ipower-gbm5108-120t-gimbal-motor.html">iPower store <span class="fa fa-link"></span></a>
</li>
<li>
GBM4108-120T <a class="pull-right" href="https://www.robotshop.com/en/ipower-gbm4108h-120t-gimbal-motor.html">Robotshop <span class="fa fa-link"></span></a>
</li>
</ul> 


## Installation
There are multiple ways of installing this Arduino library, depending on your plans with the code and your particular application. 
Check our [installation guide](installation) for more information.

<img src="extras/Images/alm.gif" class="width50">

[Find out more <i class="fa  fa-external-link"></i>](installation)


## Supported hardware
Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> is made modular and will support most of the motor + sensor combinations out there, as well as many low-cost bldc driver boards.
<p><img src="extras/Images/connection.gif" class="width50"></p>

[Find out more <i class="fa  fa-external-link"></i>](supported_hardware)

## Writing the Code
This library code is written in a way to be as simple sa possible to use and to leave a lot of space for configuration and fine tuning for specific applications. 

[Find out more <i class="fa  fa-external-link"></i>](code)


## Project Examples 
We are very happy to provide you with several projects multiple hardware configurations and fully documented and explained code!
Go see the [examples](examples)!
<p style="width:100%">
<a href="position_control_example"><img src="extras/Images/position_control_example.jpg" class="img200 img_half"></a><a href="velocity_control_example"><img src="extras/Images/hmbgc_v22_velocity_control.jpg" class="img200 img_half"></a><a href="simplefoc_pendulum"><img src="extras/Images/foc_pendulum.jpg" class="img200 img_half"></a>
</p>