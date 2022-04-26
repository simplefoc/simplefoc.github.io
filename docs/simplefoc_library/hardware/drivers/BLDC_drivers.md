---
layout: default
title: BLDC drivers
nav_order: 1
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /bldc_drivers
parent: Drivers
grand_parent: Supported Hardware
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# BLDC motor drivers
This library will be compatible with the most of 3 phase BLDC motor drivers. Such as [<i class="fa fa-file"></i> L6234](https://www.st.com/en/motor-drivers/l6234.html), [<i class="fa fa-file"></i> DRV8305](https://www.ti.com/product/DRV8305), [<i class="fa fa-file"></i> DRV8313](https://www.ti.com/product/DRV8313)  or even [<i class="fa fa-file"></i> L293](http://www.ti.com/lit/ds/symlink/l293.pdf). 

At this moment, a low-cost BLDC driver board is still reasonably hard to find making our choice of hardware is quite restricted. This is the one of the motivations to develop the <span class="simple">Simple<span class="foc">FOC</span>Shield</span>, a versatile and simple BLDC driver. Fortunately, the community is starting to gain momentum in this direction and it is probably a matter of time before BLDC motors become a standard in the hobby community as well, what is really exciting! 😃

The choice what kind of BLDC driver you will need in your project directly depends of the BLDC motor you are using. Therefore we can divide them in two groups:
- [Low power BLDC drivers](#low-power-boards---gimbal-motors-) - *Gimbal motors (R>10Ω)* 
- [High performance BLDC drivers](#high-performance-boards) - *High-power BLDC motors (R<1Ω)*

## Low power boards  ( gimbal motors )
Here are some BLDC driver boards that are designed for gimbal motors and work with the library *off-the-shelf*. Gimbal motors usually have more than 10 pole pairs and have internal resistance >10Ω. They are designed for very smooth performance on low speeds.  Gimbal motors are very versatile and are perfect for high-quality replacement of stepper motors and DC servo motors. 

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="https://simplefoc.com/assets/img/v1.jpg" style="height:100px">](https://simplefoc.com/simplefoc_shield_product)| Arduino<br> <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v1| - L6234 chip <br> - 8-24V <br> - up to 5 Amps <br> - 1 motor <br>- Arduino Shield <br> - Encoder+I2C Pullups | [More info](https://simplefoc.com/simplefoc_shield_product) | 15€
[<img src="https://simplefoc.com/assets/img/v2.jpg" style="height:100px">](https://simplefoc.com/simplefoc_shield_product)| Arduino<br> <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v2| - L6234 chip <br> - 8-24V <br> - up to 5 Amps <br> - 1 motor <br>- Arduino Shield <br> - Encoder+I2C Pullups <br> - In-line current sensing <br> - On-board voltage regulator | [SimpleFOC store](https://simplefoc.com/simplefoc_shield_product_v2) <br> [Aliexpress](https://fr.aliexpress.com/item/1005002496275228.html?spm=a2g0o.productlist.0.0.51b44925t9nr53&algo_pvid=42a7dd52-305b-4cb0-af17-60a892aaad3a&algo_exp_id=42a7dd52-305b-4cb0-af17-60a892aaad3a-0&pdp_ext_f=%7B%22sku_id%22%3A%2212000020877377792%22%7D#feedback) <br> [Ebay](https://www.ebay.com/itm/165027599242?hash=item266c69538a:g:bZIAAOSw8QJg9mvD)| ~20€
[<img src="extras/Images/mini.png" style="height:100px">](https://github.com/simplefoc/SimpleFOCMini) | <span class="simple">Simple<span class="foc">FOC</span>Mini</span> v1 | - DRV8313 chip <br> - 8-30V <br> - up to 2.5 Amps <br> - 3.3V LDO onboard <br> - 1 motor <br> - 21x26mm | [SimpleFOC store](https://simplefoc.com/shop)<br><i>(comming soon)</i> | 5-7€
[<img src="extras/Images/l6234.jpg" style="height:100px">](https://www.ebay.com/itm/L6234-Breakout-Board/153204519965?hash=item23abb3741d:g:LE4AAOSwe35bctgg) | Drotek L6234<br> breakout board | - L6234 chip <br> - 1 motor <br> - 25x25mm | [Drotek](https://store-drotek.com/212-brushless-gimbal-controller-l6234.html)<br> [Ebay](https://www.ebay.fr/itm/L6234-Breakout-Board-/153204519965) | 30€
[<img src="extras/Images/dual_simplefoc.jpg" style="height:100px">](https://github.com/ToanTech/Deng-s-foc-controller) | Deng FOC controller<br> breakout board | - L6234 chip <br> - 8-24V <br> - up to 5 Amps <br> - 2 motor <br> - 39x56mm | [Aliexpress](https://store-drotek.com/212-brushless-gimbal-controller-l6234.html)<br> [Ebay](https://www.ebay.com/itm/373690016017?hash=item5701a92111:g:YkYAAOSwF8ZhHgi3) | 35-50€

Alternatively, you can find gimbal controller boards with integrated BLDC drivers and microcontroller chips.

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="extras/Images/pinout.jpg" style="height:100px">](https://www.ebay.com/itm/HMBGC-V2-0-3-Axle-Gimbal-Controller-Control-Plate-Board-Module-with-Sensor/351497840990?hash=item51d6e7695e:g:BAsAAOSw0QFXBxrZ:rk:1:pf:1) | HMBGC V2.2 | - 4599 mosfet<br> - 2 motors  <br> - 50x30mm <br> - Atmega328 | [Ebay](https://www.ebay.com/itm/HMBGC-V2-0-3-Axle-Gimbal-Controller-Control-Plate-Board-Module-with-Sensor/351497840990?hash=item51d6e7695e:g:BAsAAOSw0QFXBxrZ:rk:1:pf:1) | 20€
[<img src="extras/Images/bgc_30.jpg" style="height:100px">](https://fr.aliexpress.com/item/4000411471994.html?spm=a2g0o.productlist.0.0.5d047d57y4zGC4&algo_pvid=861ada4b-b12f-4019-be84-fae9870a12ed&algo_expid=861ada4b-b12f-4019-be84-fae9870a12ed-1&btsid=0ab6f83a15906954691168349e30d7&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_) | BGC 3.0 | - 4599 mosfet<br> - 2 motors  <br> - 50x50mm <br> - Atmega328 | [Aliexpress](https://fr.aliexpress.com/item/4000411471994.html?spm=a2g0o.productlist.0.0.5d047d57y4zGC4&algo_pvid=861ada4b-b12f-4019-be84-fae9870a12ed&algo_expid=861ada4b-b12f-4019-be84-fae9870a12ed-1&btsid=0ab6f83a15906954691168349e30d7&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_) | 10€
[<img src="extras/Images/bgc31.jpg" style="height:100px">](https://www.ebay.com/itm/BGC-3-1-MOS-Large-Current-Two-Axis-Brushless-Gimbal-Controller-Driver-Alexmos/302692769869?hash=item4679e5204d:g:m9AAAOSweHtdzM8o) | BGC 3.1  | - l6234<br> - 2 motors  <br> - 50x50mm <br> - Atmega328 | [Ebay](https://www.ebay.com/itm/BGC-3-1-MOS-Large-Current-Two-Axis-Brushless-Gimbal-Controller-Driver-Alexmos/302692769869?hash=item4679e5204d:g:m9AAAOSweHtdzM8o) | 10€
[<img src="extras/Images/strom.jpg" style="height:100px">](https://www.ebay.com/itm/Storm32-BGC-32Bit-3-Axis-Brushless-Gimbal-Controller-V1-32-DRV8313-Motor-Driver/174343022855?hash=item2897a76907:g:20YAAOSwbEhfBo28) | Storm32 BGC | - DRV8313 <br> - 3 motors  <br> - 50x50mm <br> - Stm32f103 | [Ebay](https://www.ebay.com/itm/Storm32-BGC-32Bit-3-Axis-Brushless-Gimbal-Controller-V1-32-DRV8313-Motor-Driver/174343022855?hash=item2897a76907:g:20YAAOSwbEhfBo28) | 25€

Finally one of the cheapest possible solutions for running a gimbal BLDC motor is to use one of the dual DC motor motor drivers such as:

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="extras/Images/l298n.jpg" style="height:100px">](https://www.ebay.com/itm/L298N-DC-Stepper-Motor-Driver-Module-Dual-H-Bridge-Control-Board-for-Arduino/362863436137?hash=item547c58a169:g:gkYAAOSwe6FaJ5Df)| Stepper driver L298N| - L298N  chip <br> - 1 motor <br>- 5V-35V <br> - 2A(MAX single bridge) | [Ebay](https://www.ebay.com/itm/L298N-DC-Stepper-Motor-Driver-Module-Dual-H-Bridge-Control-Board-for-Arduino/362863436137?hash=item547c58a169:g:gkYAAOSwe6FaJ5Df) | 2€

<blockquote class="warning">
<p class="heading">L298N limitations</p>
L298N is based on bipolar transistor technology and has relatively long transistor rise times which can produce non-smooth operation.
We suggest the L298N based boards only in closed loop mode, as the position sensor can correct possible noise from the driver. 
It can also be a good board for beginner users, to get familier with the FOC as a cheep solution, but with certain performance limits.
</blockquote>

## High performance boards
<span class="simple">Simple<span class="foc">FOC</span>library</span> will support basically any BLDC motor driver which can be controlled using 3 PWM or 6PWM signals. Up to this moment ([version 1.3.1](https://github.com/simplefoc/Arduino-FOC/releases)) the library doesn't implement the current control loop. The motor torque is controlled via voltage directly ([more info](voltage_torque_control))

Here are the boards that have been tested and are compatible with the library:

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="extras/Images/drv8302.png" style="height:100px">](https://fr.aliexpress.com/item/4000126430773.html?spm=a2g0o.productlist.0.0.702a312aXmzuUK&algo_pvid=50131a88-ac88-4755-bb71-978c07ec461e&algo_expid=50131a88-ac88-4755-bb71-978c07ec461e-5&btsid=0b0a119a15957548552557385e6f5e&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_)| DRV8302 driver | - DRV8302 chip <br> - 1 motor <br>- 45V/27A <br> - BEMF/current sensing  <br> - fault protection| [Aliexpress](https://fr.aliexpress.com/item/4000126430773.html?spm=a2g0o.productlist.0.0.702a312aXmzuUK&algo_pvid=50131a88-ac88-4755-bb71-978c07ec461e&algo_expid=50131a88-ac88-4755-bb71-978c07ec461e-5&btsid=0b0a119a15957548552557385e6f5e&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_) | 30€
[<img src="extras/Images/drv8301.png" style="height:100px">](https://fr.aliexpress.com/item/4000203180955.html?spm=a2g0o.productlist.0.0.39871962xgolNI&algo_pvid=a86f85ad-3d0b-46cd-a05a-cb7c89e92c9e&algo_expid=a86f85ad-3d0b-46cd-a05a-cb7c89e92c9e-4&btsid=0b0a01f815957554085321097e9fdf&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_)| DRV8301 driver | - DRV8301 chip <br> - 1 motor <br>- 45V/27A <br> - BEMF/current sensing  <br> - fault protection <br> - SPI configuration| [Aliexpress](https://fr.aliexpress.com/item/4000203180955.html?spm=a2g0o.productlist.0.0.39871962xgolNI&algo_pvid=a86f85ad-3d0b-46cd-a05a-cb7c89e92c9e&algo_expid=a86f85ad-3d0b-46cd-a05a-cb7c89e92c9e-4&btsid=0b0a01f815957554085321097e9fdf&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_) | 45€
[<img src="extras/Images/B-G431B-ESC1_SPL.jpg" style="height:100px">](https://www.st.com/en/evaluation-tools/b-g431b-esc1.html)| B-G431B-ESC1 | - STM32G431CB chip <br> - On-board ST-LINK/V2-1 <br> - 1 motor <br>- 30V/40A <br> - low-side current sensing  <br> - fault protection     | [STM webiste](https://www.st.com/en/evaluation-tools/b-g431b-esc1.html) <br> [Mouser](https://eu.mouser.com/ProductDetail/STMicroelectronics/B-G431B-ESC1/?qs=%2Fha2pyFaduj9HtQf9%2FgsBmvGqEl7EbEPOyTxg06xIidkuUIykXhpkA%3D%3D) | 16€
[<img src="extras/Images/SHIELD_IFX007T.jpg" style="height:100px">](https://www.infineon.com/cms/en/product/evaluation-boards/bldc-shield_ifx007t/)| Infineon <br> BLDC-SHIELD_IFX007T shield | -  IFX007T half-bridges <br> - 1 motor <br>- 40V/30A <br> - BEMF/low-side current sensing  <br> - fault protection | [Infineon](https://www.infineon.com/cms/en/product/evaluation-boards/bldc-shield_ifx007t/) | 40€
[<img src="https://simplefoc.com/assets/img/dagor/Dagor_iso.png" style="height:120px">](https://github.com/byDagor/Dagor-Brushless-Controller)| [@byDagor](https://github.com/byDagor) <br> Dagor Brushless Controller | -  DRV8305 driver <br> - 1 motor <br>- 25V/40A <br> - Current sensing  <br> - Integrated sensor<br> - Esp32 based<br> - fault protection | [simplefoc shop](https://simplefoc.com/shop)<br> <i>alpha batch sold out</i> | 40€
[<img src="extras/Images/powershield.jpg" style="height:120px">](https://github.com/simplefoc/Arduino-SimpleFOC-PowerShield)| Arduino<br> <span class="simple">Simple<span class="foc">FOC</span><span class="power">Power</span>Shield</span> | -  BTN8982 half-bridges <br> - 1 motor <br>- 40V/30A<br> - fault protection <br> <b>Release v1:</b> <br> - In-line current sensing  <br> - I2C/Hall/Encoder pullups*<br> - 2x Stackable*    |  [fabricate](https://github.com/simplefoc/Arduino-SimpleFOC-PowerShield)  | ~20€
[<img src="extras/Images/high_perf_new.png" style="height:100px">](https://github.com/ChenDMLSY/FOC-SimpleFOC-MotorDriveDevelopmentBoard)| FOC-SimpleFOC-MotorDriveDevelopmentBoard | - IR2103 drivers <br> - 1 motor <br>- 36V/20A <br> - low-side current sensing| [Aliexpress](https://fr.aliexpress.com/item/1005002852603523.html?spm=a2g0o.productlist.0.0.76861dc3dEgETJ&algo_pvid=48101b3f-ba22-40b0-b6de-421d79a675b8&algo_exp_id=48101b3f-ba22-40b0-b6de-421d79a675b8-2&pdp_ext_f=%7B%22sku_id%22%3A%2212000022467783080%22%7D) [Ebay](https://www.ebay.com/itm/373689972247?hash=item5701a87617:g:i0IAAOSwrKlhHf~r) | 30€
[<img src="extras/Images/odrive.jpg" style="height:100px">](https://odriverobotics.com/shop/odrive-v36)| ODRIVE V3.6 |  - STlink programmer needed <br> - 2 motors <br>- 12-48V <br> - 60A (120A peak) <br> - low-side current sensing| [Aliexpress](https://www.aliexpress.com/item/1005002349959313.html?spm=a2g0o.productlist.0.0.6248381eOCYTRO&algo_pvid=30554e7d-0b77-44fc-a790-e35040ce3de9&algo_exp_id=30554e7d-0b77-44fc-a790-e35040ce3de9-0&pdp_ext_f=%7B%22sku_id%22%3A%2212000020231141543%22%7D) <br> [ODive shop](https://odriverobotics.com/shop) | 70-100€ <br> 200€


<blockquote class="warning">
<p class="heading">IFX007T and BTN8982 chips limitations</p>
IFX007T and BTN8982 are based on relatively old transistor technology and have relatively long transistor rise times which can produce non-smooth operation.
We suggest the boards based on these chips to be used only in closed loop mode, as the position sensor can correct possible noise from the driver. 
It can also be a good board for beginner users, to get familier with the high-current FOC as a cheep solution, but with certain performance limits.
</blockquote>