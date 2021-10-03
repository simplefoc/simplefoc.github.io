---
layout: default
title: Portenta H7 boards
nav_order: 8
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /portenta_mcu
parent: Microcontrollers
grand_parent: Supported Hardware
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Arduino PRO Portenta H7 boards

MCU | 2 PWM mode | 4PWM mode | 3 PWM mode | 6 PWM mode | pwm frequency config 
--- | --- |--- |--- |--- |--- 
Portenta H7 | ✔️ | ✔️ | ✔️ | ❌ | ✔️ 

Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> recently started supporting the Portenta H7 boards as a result of a collaboration with the company <img src="extras/Images/arduino.png" style="height:15px"><a href="https://www.arduino.cc/">Arduino</a>. The support is still in its initial stage but most of the PWM related features are already implemented. 

 Board | Name | Specifications | Link | Price
---- | --- | --- | --- | --- | ---
[<img src="extras/Images/portenta.png" class="imgtable150">](https://store.arduino.cc/products/portenta-h7) | Portenta H7 | Dual core ST STM32H747XI ​Processor <br> - 2 Mbytes of Flash <br>- WiFi/BT Module<br> - 22 timers<br> - 4 DMA controller <br>- ap to 32 adc channels<br>- 480 MHz <br> <i>and much much more...</i> |[Arduino Store](https://store.arduino.cc/products/portenta-h7) | 90€ 


<blockquote class="warning"> <p class="heading">BEWARE: limitations of the current implementation ⚠️</p>
Portenta H7 has only the early stage of support. The PWM features work well and have been tested and most of the sensors will work well, but 6PWM support has still not been implemented. For now only the pins on the original board have been used, not the breakout board.
</blockquote>

## Arduino IDE support package
In order to use the Portenta H7 boards in the Arduino IDE please install the Arduino MBED OS Poertenta H7 boards support package using Arduino IDE board manager. Here is a [quick tutorial](https://docs.arduino.cc/tutorials/portenta-h7/por-ard-gs) provided by Arduino if you are not sure how to do it exactly.

Here is quick video showing how to do it:
<iframe class="youtube" src="https://www.youtube.com/embed/epAn3ynDjhY" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>