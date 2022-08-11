---
layout: default
title: nRF52 boards
nav_order: 9
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /nrf52_mcu
parent: Microcontrollers
grand_parent: Supported Hardware
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# nRF52 boards support

MCU | 2 PWM mode | 4 PWM mode | 3 PWM mode | 6 PWM mode | pwm frequency config 
--- | --- |--- |--- |--- |--- 
nRF52 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ 

Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> recently started supporting the nRF52 devices due to a lot of effort put in by [@Polyphe](https://github.com/Polyphe). These are the boards that have been tested so far:

 Board | Name | Specifications | Link | Price
---- | --- | --- | --- | --- | ---
[<img src="extras/Images/nano33_ble.jpg" class="imgtable150">](https://store.arduino.cc/arduino-mkr1000-wifi) | Arduino Nano 33 BLE |  ARM Cortex M4F <br>- 3.3V logic<br> - 12 PWMs<br> - all pins interrupts <br>- 8 adc pins<br>- 64Mhz| [Arduino Store](https://store.arduino.cc/arduino-mkr1000-wifi) | 18€ 
[<img src="extras/Images/bluefruit_le.jpg" class="imgtable150">](https://www.adafruit.com/product/3406) | Adafruit Feather nRF52 Bluefruit | ARM Cortex M4F <br>- 3.3V logic<br> - 12 PWMs<br> - all pins interrupts <br>- 8 adc pins<br>- 64Mhz|[Adafruit Store](https://www.adafruit.com/product/3406) | 22€ 


## Arduino IDE support package
In order to use the nRF52 boards in the Arduino IDE please instal the nRF52 support package using Arduino IDE board manager.

For some boards you might need to install the Adafruit nRF52 package in addition to the Arduino nRF52 package in your Board manager. Here is [a quick guide](https://learn.adafruit.com/bluefruit-nrf52-feather-learning-guide/arduino-bsp-setup) how to enable Adafruit packages in the Arduino IDE.