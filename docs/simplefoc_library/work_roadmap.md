---
layout: default
title: Work roadmap
parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
nav_order: 8
permalink: /roadmap
---

#  Feature Roadmap

This page is not really rigorously maintained and can be a bit outdated.

<div class="image_icon" >
    <a href="https://simplefoc.notion.site/Developement-4149a181ea5b4383964cc8cc250e7d11" >
        <img src="extras/Images/roadmap.png" >
        <i class="fa fa-external-link-square fa-2x"></i>
        <p >Roadmap with Notion</p>
    </a>
</div>
<div class="image_icon" >
    <a href="https://github.com/simplefoc/Arduino-FOC/releases" >
        <img src="extras/Images/releases.png" >
        <i class="fa fa-external-link-square fa-2x"></i>
        <p >Github changelog</p>
    </a>
</div>

For more info about the features of library releases visit the [github releases <i class="fa fa-tag"></i>](https://github.com/simplefoc/Arduino-FOC/releases). 

# Old roadmap 
<details markdown=1>

## Motion Control and FOC algorithm
- [x] **Motor control: Low-side current  sensing** - No.1
- [ ] **Motor control: Inline current sensing with DMA** - No.2
- [ ] Motor control: High-side current  sensing
- [x] **Bugfix: Floating point angle overflow** 
- [x] Motor control: Inline current  sensing
- [x] **Motor control: Support for stepper motors**
- [x] Motor control: Support for Hall sensor commutation 
- [x] Motor control: Support for fully open-loop operation (no sensor)
- [x] Add support for acceleration ramping
- [x] Velocity Low pass filter
- [x] Timer interrupt execution rather than in the `loop()`: ❌ No real performance improvement
- [x] Sine wave lookup table implementation
- [X] Implement Space Vector Modulation method : Pure SVM
- [x] Implement Space Vector Modulation method : PWM SVM

## MCU support
- [ ] ESP8266 - initial 
- [ ] Portenta H7 - initial 
- [ ] Renesas support - initial
- [x] Arduino leonardo 
- [x] Raspberry pi Pico - [PR #78](https://github.com/simplefoc/Arduino-FOC/pull/78)
- [x] SAM - Arduino DUE
- [x] SAMD21/51
- [x] Teensy support
- [x] ESP32 support
- [x] STM32 Nucleo support
- [x] STM32 Bluepill support
- [x] nRF52 support
- [x] Hardware specific code separation : easier porting in between devices `hardware_utils.cpp/.h`

## Driver support
- [x] Driver support: Disable the phases in 6PWM mode
- [x] Driver support: Implement support for MOSFET control low and high pairs
- [x] Driver support: DRV8302 boards

## Sensor support
- [ ] IMU as position sensor
- [ ] Back-EMF support
- [ ] Sensorless-FOC support
- [ ] Make support for magnetic encoder  SSI
- [x] Make support for magnetic encoder  PWM
- [x] Make support for magnetic encoder  Analog
- [x] Make support for magnetic encoder  I2C
- [x] Make support for magnetic encoder  ABI
- [x] Make support for magnetic encoder  SPI
- [x] Hall sensor support
- [x] Encoder index proper implementation

## User interaction
- [ ] Commander make a minimal version for lower memory devices
- [ ] Make the target setting interface for instantaneous position, velocity, torque setting (ex. `q 10 20 1`)
- [x] Implement motor commands 
- [x] Support monitoring 

## Usability 
- [ ] a lot more docs and examples 
- [x] <span class="simple">Simple<span class="foc">FOC</span>library</span> getting started page
- [x] <span class="simple">Simple<span class="foc">FOC</span>Shield</span> getting started page
- [x] Make the library accessible in the Arduino Library Manager 
- [x] Make minimal version of the arduino code - all in one arduino file
- [x] Documentation separation from README
- [x] Proper introduction of the Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>

## Videos  
- [ ] VIDEO: Publish a video tutorial for using the library and the samples  
- [ ] VIDEO: Coding setup and procedure video
- [x] VIDEO: Two motors running on HMBGC example
- [x] VIDEO: Initial video with simple demonstration

</details>


