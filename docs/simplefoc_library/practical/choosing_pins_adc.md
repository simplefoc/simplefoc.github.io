---
layout: default
title: Choosing ADC pins
nav_order: 4
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /choosing_adc_pins
parent: Practical guides
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---


# A short guide to choosing Analog pins for your application

When it comes to choosing pins for your application, there are a few things to consider. The most important thing is to make sure that the pins you choose are compatible with the hardware you are using.
Make sure to check that your microcontroller family and SimpleFOC supports the necessary PWM generation, position sensor and current sensing technique. You can find this info in our docs [here](microcontrollers). 

Once you are sure that you microcontroller is apt to the application, it is time to consider which pins to use. There are three main questions:
1. Which pins to use for PWM generation?
2. Which pins to use for the position sensor?
3. Which pins to use for current sensing?

In this guide we will focus on the first question: **Which analog pins to use for current sensing?**

The answer to these questions depends on the microcontroller you are using and the hardware you have.  Here is the list of microcontroller families supported by SimpleFOC and the PWM generation modes supported by each of them:

MCU | In-line | Low-side | High-side
--- | --- |--- |--- 
Arduino AVR (8-bit) | ✔️ | ❌ |  ❌
Arduino DUE  | ✔️ | ❌ |  ❌
stm32 (in general) | ✔️ | ❌ |  ❌
stm32f1 family | ✔️ | ✔️ (one motor) |  ❌
stm32f4 family | ✔️ | ✔️ (one motor) |  ❌
stm32g4 family | ✔️ | ✔️ (one motor) |  ❌
stm32l4 family | ✔️ | ✔️ (one motor) |  ❌
stm32f7 family | ✔️ | ✔️ (one motor) |  ❌
stm32h7 family | ✔️ | ✔️ (one motor) |  ❌
stm32 B_G431B_ESC1 | ❌ | ✔️ (one motor) |  ❌
esp32/esp32s3 | ✔️ | ✔️ |  ❌
esp32s2/esp32c3 |  ✔️ | ❌ |  ❌ 
esp8266 | ❌ | ❌ |  ❌ 
samd21 | ✔️ | ✔️ (one motor) |  ❌ 
samd51 | ✔️ | ❌ |  ❌ 
teensy3 | ✔️ | ❌ |  ❌
teensy4 | ✔️ | ✔️(one motor) |  ❌
Raspberry Pi Pico | ✔️ | ❌ |  ❌
Portenta H7 | ✔️ | ❌ |  ❌
nRF52 | ✔️ | ❌ |  ❌
Renesas (UNO R4 Minima) | ❌ | ❌ |  ❌

### In-line current sensing
What is important to see is that most of the the microcontroller families, except esp8266 and Arduino UNO R4, can be used with in-line current sensing. For this current sensing technique, you can use any of the analog pins, as no synchronization between the PWM signals and the current sensing is necessary. 

### Low-side current sensing
When it comes to using the low-side current sensing, a precise synchronisation between the PWM signals and the current sensing is necessary ([read more here](low_side_current_sense)). 
SimpleFOC aims to support low-side current sensing for all the microcontroller families that have the capability to synchronise the ADC conversion with the PWM signals.
For the moment, the low-side current sensing is supported for the following microcontroller families: stm32, esp32, teensy4 and samd21. 


<blockquote class="info" markdown="1"><p class="heading">What about high-side current sensing?</p>
High-side current sensing is not supported by SimpleFOC at the moment. This is mostly because the high-side sensing is very rare in practice. Most of the mcu families that can support low-side sensing could support the high side as well, but as of now there is no support for it in the library. If you are interested in this feature, please let us know on our community forum.
</blockquote>

When it comes to choosing the analog pins for the low-side current sensing, as there is a need for the synchronization between the PWM signals (belonging to one or more timers) and the ADC conversion, the choice of the pins is more limited. **As a rule of thumb, it is recommended to use the pins that belong to the same ADC.** This comes form the fact that in the PWM synchronization process, timers often can trigger only one ADC at a time.

The information about the ADC associated with the pins is sometimes hard to obtain, especially for less experienced users. So in the next few sections we will provide some information on how to find the ADC pins for some of the most popular microcontroller families supporting the low-side current sensing.

- [ESP32 boards](#esp32-boards)
- [STM32 boards](#stm32-boards)
- [Teensy4 boards](#teensy4-boards)

## ESP32 boards

Esp32 boards have two ADCs that can be used for the low-side current sensing. The ADCs are associated with the pins in the following way (info from the [espressif docs](https://docs.espressif.com/projects/esp-idf/en/v4.4/esp32/api-reference/peripherals/adc.html)):


ESP32 SoC |`ADC1` pins | `ADC2` pins
--- |  --- |---
ESP32 |  GPIO32 - GPIO39 | GPIO0, GPIO2, GPIO4, GPIO12 - GPIO15, GOIO25 - GPIO27
ESP32-S2| GPIO1 - GPIO10 | GPIO11 - GPIO20
ESP32-S3| GPIO1 - GPIO10 | GPIO11 - GPIO20
ESP32-C2 | GPIO0 - GPIO4 | - 
ESP32-C3 | GPIO0 - GPIO4 | GPIO5
ESP32-C6 | GPIO0 - GPIO6 | -

<blockquote class="info" markdown="1"><p class="heading">Important</p>
Esp32 has a very flexible ADC configuration, so you can use any of the pins listed above for the low-side current sensing. However, it is recommended to use the pins that belong to the same `ADC`.
</blockquote>


## STM32 boards

Stm32 is the most powerful family of microcontrollers supported by SimpleFOC, at least in terms of motor control capabilities. SimpleFOC supports many of the STM32 families such as stm32f1, stm32f4, stm32g4, stm32l4, stm32f7, stm32h7 and all of them can be used in low-side current sensing mode. 

<blockquote class="info" markdown="1"><p class="heading">Important</p>
While for inline current sensing the requirements are more relaxed, for low-side current sensing with stm32 boards, it is required to use the analog pins that belong to the same `ADC`.
</blockquote>

Once you provide the SimpleFOC current sense object with a set of pins, the library will automatically associate the pins with the appropriate ADC and channels and will take care of the synchronization between the PWM signals and the ADC conversion. The library code will automatically check which ADCs the pins belong to and will find the common ADC for the pins. If the pins do not belong to the same ADC, the library will throw an error.

Finding the pins that belong to the same ADC is not an easy task, especially for less experienced users. Therefore, we've created a website that enables a relatively easy navigation of the pins and timers for the most popular stm32 boards. 

<a href ="https://docs.simplefoc.com/stm32pinouts/" class="btn btn-primary"><i class="fa fa-github"></i> Open stm32 pinout helper</a>   

## Teensy4 boards

Teensy4 boards are very powerful and have a lot of analog pins that can be used for with both of their ADCs. The pins that belong to the ADCs are listed in the table below. For more info see the awesome github repo [TeensyDocuments](https://github.com/KurtE/TeensyDocuments/blob/master/Teensy4%20Pins.pdf)

Board |	`ADC1` pins only | `ADC2` pins  only | Both ADCs
---| ---- | ---- | ----
Teensy 4 |	24, 25 | 26, 27 | 14, 15,..., 22, 23, 40, 41
Teensy 4.1 |	24, 25 | 26, 27, 38, 39 | 14, 15,..., 22,23


<blockquote class="info" markdown="1"><p class="heading">Important</p>
For low-side current sensing with Teensy4 boards, it is required to use the analog pins that belong to the `ADC1`. So you can use any analog pin except the ones that belong only to the `ADC2`.
</blockquote>