---
layout: default
title: Mini v1
description: "Connecting SimpleFOCMini with your hardware."
nav_order: 1
permalink: /mini_v1_connect_hardware
parent: Connecting the hardware
grand_parent: Starting with Mini
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span>Mini</span>
grand_grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
---


# Connecting the hardware to <span class="simple">Simple<span class="foc">FOC</span>Mini</span> v1

Connecting the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> to the microcontroller, BLDC motor and power-supply is very straight forward. 

<p>
<img src="extras/Images/mini_where.png" class="width40">
</p>

## Microcontroller
-  <span class="simple">Simple<span class="foc">FOC</span>Mini</span> is designed as a standalone BLDC driver, which will basically work with any microcontroller. 
- This board has 10 pins exposed for the connection to the microcontroller

<p>
<img src="extras/Images/mini_req_opt.png" class="width30">
</p>

There are 5 pins that are required to be connected 

Pin Name | Description 
--- | --- 
GND | Ground  (common ground) 
IN1 | PWM input phase 1 
IN2 | PWM input phase 2
IN3 | PWM input phase 3 
EN | Driver Enable  

<blockquote class="warning"><p class="heading">BEWARE: pin order</p>
Version v1.1 of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> has the order of the IN1,IN2,IN3 and EN changed with respect to the v1.0.
</blockquote>

These pins need to ba connected whenever using the  <span class="simple">Simple<span class="foc">FOC</span>Mini</span>. The 3 pwm pins and the enable pin are used to control the DRV8313 driver and in terms of the  <span class="simple">Simple<span class="foc">FOC</span>library</span> they correspond to the entries of the `BLDCDriver3PWM` class. The common ground pin is very important as well in order to make sure that all the PWM and Enable pins are read properly by the driver chip. Once you decide which pins you will be using for `INx` and `EN` pins you will be able to porvide them to the `BLDCDriver3PWM` class in your Arduino sketch.

```cpp
BLDCDriver3PWM driver = BLDCDriver3PWM(IN1, IN2, IN3, EN);
```

In addition to these pins there are 5 pins that are optional. 

Pin Name | Description 
--- | --- 
3.3V | 3.3V output - **NOT INPUT**  
GND | Ground 
nRT | Reset (active LOW)
nSP | Sleep (active LOW)
nFT | Fault output (active LOW) 

<span class="simple">Simple<span class="foc">FOC</span>Mini</span> is based on the DRV8313 driver which has integrated 3.3V regulator which might be useful in some applications to power a sensor or similar. The 3.3v pin of the  <span class="simple">Simple<span class="foc">FOC</span>Mini</span> can therefore be used as a 3.3v source pin with the maximum current output of 10mA. Both GND pins exposed in the <span class="simple">Simple<span class="foc">FOC</span>Mini</span>'s header are connected to the same ground, so you can choose the one which is more convenient to your application.
<blockquote class="warning"><p class="heading">BEWARE: 3.3V LDO Power limitations</p>
DRV8313 comes with the 3.3V voltage regulator and it is connected to the <span class="simple">Simple<span class="foc">FOC</span>Mini</span>'s 3.3V pin. However it has a limitation of 10mA, which is in general not enough to power a microcontroller. But it might be enough to power a LED light or some position sensors.
</blockquote>

Pin `nFT` (fault) is an active LOW output of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> which can be read to verify if the DRV8313 driver is working properly. If this pin is in LOW it means the DRV8313 is its fault state and it cannot drive the motor. Then the pin `nRT` (reset), which is also active LOW, can be used to reset the DRV8313 driver to reinitialise its internal state and exit the fault state, this cannot be done by simply toggling the enable pin. Finally the pin `nSP` (sleep) is an active LOW pin that puts the DRV8313 in the low-power sleep mode, consuming the current of under 1uA.





## BLDC motor
- Motor phases `a`, `b` and `c` are connected directly the motor terminal connector `M1`,`M2` and `M3`

<blockquote class="warning"><p class="heading">BEWARE: Power limitations</p>
<span class="simple">Simple<span class="foc">FOC</span>Mini</span> is designed for gimbal motors with internal resistance higher than R>10Ohm. The absolute maximal current of this board is 5A. Please make sure when using this board in your projects that the BLDC motor used does comply with these limits.  <br>
If you still want to use this driver with the BLDC motors with very low resistance R < 1Ohm make sure to limit the voltage set to the board. <br>
For a bit more information about the choice of motors visit <a href="bldc_motors"> BLDC motor docs</a>
</blockquote>

## Power supply
- Power supply cables are connected directly to the terminal pins `+` and `-` 
- Required power supply voltage is from 8V to 35V.


## Examples of connection schematics

<span class="simple">Simple<span class="foc">FOC</span>Mini</span> can be connected to any microcontroller (MCU) pin combination that can ensure that the mini's `GND` pin is connected to the MCU's `GND` pin, MCU's 3 pwm capable pins are connected to the `IN1`,`IN2` and `IN3` pins, and one MCU's digital pin is connected to the `EN` pin.


An example connection of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> and Nucleo board is shown on the image below. 

<img src="extras/Images/mini_connection_mucleo.png" class="width60">

<span class="simple">Simple<span class="foc">FOC</span>Mini</span> can be directly plugged into the Arduino headers of teh Nucleo board from the pin `10` to the `GND` pin and in that way reduce the amount of wires necessary. For more information on this example connection see [this library example](mini_example_nucleo).

--- | --- | --- | ---| --- | ---
Mini Pin | EN | IN3 | IN2 | IN1 | GND
Nucleo Pin | 10 | 11 | 12 | 13 | GND

```cpp
BLDCDriver3PWM driver = BLDCDriver3PWM(13, 12, 11, 10);
```

An example connection of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> and Arduino UNO is shown on the image below. 
<img src="extras/Images/mini_connection_uno.png" class="width60">

<span class="simple">Simple<span class="foc">FOC</span>Mini</span> can be directly plugged into the Arduino headers of the UNO board from the pin `8` to the `12` pin (pin 12 can act as a GND pin) and in that way reduce the amount of wires necessary. For more information on this example connection see [this library example](mini_example). 

--- | --- | --- | ---| --- | ---
Mini Pin | EN | IN3 | IN2 | IN1 | GND
UNO Pin | 8 | 9 | 10 | 11 | 12

```cpp
BLDCDriver3PWM driver = BLDCDriver3PWM(11, 10, 9, 8);
```

Another example of connecting the Arduino UNO with <span class="simple">Simple<span class="foc">FOC</span>Mini</span> is shown below
<img src="extras/Images/mini_connection_uno2.png" class="width60">

--- | --- | --- | ---| --- | ---
Mini Pin | EN | IN3 | IN2 | IN1 | GND
UNO Pin | 4 | 5 | 6 | 9 | GND

```cpp
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 6, 5, 4);
```

An example connection of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> and stm32 Bluepill is shown on the image below. 
<img src="extras/Images/mini_connection_bluepill.png" class="width60">


--- | --- | --- | ---| --- | ---
Mini Pin | EN | IN3 | IN2 | IN1 | GND
Bluepill Pin | PB15 | PA8 | PA9 | PA10 | GND

```cpp
BLDCDriver3PWM driver = BLDCDriver3PWM(PA10, PA9, PA8, PB15);
```