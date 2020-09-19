---
layout: default
title: BLDC drivers
parent: Supported Hardware
nav_order: 2
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /bldc_drivers
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# BLDC motor drivers
This library will be compatible with the most of the 3 phase BLDC motor drivers. Such as [<i class="fa fa-file"></i> L6234](https://www.st.com/en/motor-drivers/l6234.html), [<i class="fa fa-file"></i> DRV8305](https://www.ti.com/product/DRV8305), [<i class="fa fa-file"></i> DRV8313](https://www.ti.com/product/DRV8313)  or even [<i class="fa fa-file"></i> L293](http://www.ti.com/lit/ds/symlink/l293.pdf). 

At this moment, a low-cost BLDC driver board is still reasonably hard to find making our choice of hardware is quiet restricted. This is the one of the motivations to develop the <span class="simple">Simple<span class="foc">FOC</span>Shield</span>, a versatile and simple BLDC driver. Fortunately, the community is starting to gain momentum in this direction and it is probably a matter of time before BLDC motors become a standard in the hobby community as well, what is really exciting! 😃

The choice what kind of BLDC driver you will need in your project directly depends of the BLDC motor you are using. Therefore we can divide them in two groups:
- [Low power BLDC drivers](#low-power-boards---gimbal-motors-) - *Gimbal motors (R>10Ω)* 
- [High performance BLDC drivers](#high-performance-boards) - *High-power BLDC motors (R<1Ω)*

## Low power boards  ( gimbal motors )
Here are some BLDC driver boards that are designed for gimbal motors and work with the library *off-the-shelf*. Gimbal motors usually have more than 10 pole pairs and have internal resistance >10Ω. They are designed for very smooth performance on low speeds.  Gimbal motors are very versatile and are perfect for high-quality replacement of stepper motors and DC servo motors. 

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="extras/Images/hor_cad_shield.jpg" style="height:100px">](https://simplefoc.com/simplefoc_shield_product)| Arduino<br> <span class="simple">Simple<span class="foc">FOC</span>Shield</span>| - L6234 chip <br> - 1 motor <br>- Arduino Shield <br> - Encoder Pullups | [More info](https://simplefoc.com/simplefoc_shield_product) | 15€
[<img src="extras/Images/l6234.jpg" style="height:100px">](https://www.ebay.com/itm/L6234-Breakout-Board/153204519965?hash=item23abb3741d:g:LE4AAOSwe35bctgg) | Drotek L6234<br> breakout board | - L6234 chip <br> - 1 motor <br> - 25x25mm | [Drotek](https://store-drotek.com/212-brushless-gimbal-controller-l6234.html)<br> [Ebay](https://www.ebay.fr/itm/L6234-Breakout-Board-/153204519965) | 30€

Alternatively, you can find gimbal controller boards with integrated BLDC drivers and microcontroller chips such as HMBGC V2.2 and BGC 3.0 similar boards.

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="extras/Images/pinout.jpg" style="height:100px">](https://www.ebay.com/itm/HMBGC-V2-0-3-Axle-Gimbal-Controller-Control-Plate-Board-Module-with-Sensor/351497840990?hash=item51d6e7695e:g:BAsAAOSw0QFXBxrZ:rk:1:pf:1) | HMBGC V2.2 | - 4599 mosfet<br> - 2 motors  <br> - 50x30mm <br> - Atmega328 | [Ebay](https://www.ebay.com/itm/HMBGC-V2-0-3-Axle-Gimbal-Controller-Control-Plate-Board-Module-with-Sensor/351497840990?hash=item51d6e7695e:g:BAsAAOSw0QFXBxrZ:rk:1:pf:1) | 20€
[<img src="extras/Images/bgc_30.jpg" style="height:100px">](https://fr.aliexpress.com/item/4000411471994.html?spm=a2g0o.productlist.0.0.5d047d57y4zGC4&algo_pvid=861ada4b-b12f-4019-be84-fae9870a12ed&algo_expid=861ada4b-b12f-4019-be84-fae9870a12ed-1&btsid=0ab6f83a15906954691168349e30d7&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_) | BGC 3.0 | - 4599 mosfet<br> - 2 motors  <br> - 50x50mm <br> - Atmega328 | [Aliexpress](https://fr.aliexpress.com/item/4000411471994.html?spm=a2g0o.productlist.0.0.5d047d57y4zGC4&algo_pvid=861ada4b-b12f-4019-be84-fae9870a12ed&algo_expid=861ada4b-b12f-4019-be84-fae9870a12ed-1&btsid=0ab6f83a15906954691168349e30d7&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_) | 10€

## High performance boards
<span class="simple">Simple<span class="foc">FOC</span>library</span> will support basically any BLDC motor driver which can be controlled using 3 PWM signals. Up to this moment ([version 1.3.1](https://github.com/askuric/Arduino-FOC/releases)) the library doesn't implement the current control loop. The motor torque is controlled via voltage directly ([more info](voltage_torque_control))

Here are the boards that have been tested and are compatible with the library:

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="extras/Images/drv8302.png" style="height:100px">](https://fr.aliexpress.com/item/4000126430773.html?spm=a2g0o.productlist.0.0.702a312aXmzuUK&algo_pvid=50131a88-ac88-4755-bb71-978c07ec461e&algo_expid=50131a88-ac88-4755-bb71-978c07ec461e-5&btsid=0b0a119a15957548552557385e6f5e&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_)| DRV8302 driver | - DRV8302 chip <br> - 1 motor <br>- 45V/27A <br> - BEMF/current sensing  <br> - fault protection| [Aliexpress](https://fr.aliexpress.com/item/4000126430773.html?spm=a2g0o.productlist.0.0.702a312aXmzuUK&algo_pvid=50131a88-ac88-4755-bb71-978c07ec461e&algo_expid=50131a88-ac88-4755-bb71-978c07ec461e-5&btsid=0b0a119a15957548552557385e6f5e&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_) | 30€
[<img src="extras/Images/drv8301.png" style="height:100px">](https://fr.aliexpress.com/item/4000203180955.html?spm=a2g0o.productlist.0.0.39871962xgolNI&algo_pvid=a86f85ad-3d0b-46cd-a05a-cb7c89e92c9e&algo_expid=a86f85ad-3d0b-46cd-a05a-cb7c89e92c9e-4&btsid=0b0a01f815957554085321097e9fdf&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_)| DRV8301 driver | - DRV8301 chip <br> - 1 motor <br>- 45V/27A <br> - BEMF/current sensing  <br> - fault protection <br> - SPI configuration| [Aliexpress](https://fr.aliexpress.com/item/4000203180955.html?spm=a2g0o.productlist.0.0.39871962xgolNI&algo_pvid=a86f85ad-3d0b-46cd-a05a-cb7c89e92c9e&algo_expid=a86f85ad-3d0b-46cd-a05a-cb7c89e92c9e-4&btsid=0b0a01f815957554085321097e9fdf&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_) | 45€