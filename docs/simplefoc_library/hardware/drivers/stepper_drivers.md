---
layout: default
title: Stepper drivers
nav_order: 2
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /stepper_drivers
parent: Drivers
grand_parent: Supported Hardware
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Stepper motor drivers
This library will be compatible with most of the 2 phase stepper motor driver boards that feature 2 full H-bridges or 4 half-bridges such as [<i class="fa fa-file"></i> MC33926](https://www.nxp.com/docs/en/data-sheet/MC33926.pdf), [<i class="fa fa-file"></i> L298](https://www.st.com/resource/en/datasheet/l298.pdf), [<i class="fa fa-file"></i> L293](http://www.ti.com/lit/ds/symlink/l293.pdf) and many more. **In order for the driver board to work with the library it needs to be controllable using 4 pwm signals.**

<blockquote class="warning"><p class="heading">⚠️ DIR/STEP stepper drivers not supported!</p>
This library does not support stepper drivers with DIR+STEP (step and direction) interface such as A4988, DRV8825, TB6600, TB6560 and similar.
</blockquote>

The choice of stepper driver depends directly of the stepper motor you are using, basically make sure the stepper driver can provide the current the motor needs to perform. 



## Supported board examples
Here are some stepper driver boards that are supported by this library. 

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="extras/Images/ms1508.jpg" style="height:100px">](https://www.ebay.com/itm/Dual-Channel-DC-Motor-Driver-Mini-Module-PWM-Speed-Control-Beyond-L298N-S2U/124342998274?hash=item1cf36b9502:g:zJoAAOSwFuZbSF25)| Stepper driver MX1508| - MX1508  chip <br> - 1 motor <br>- 5V-10V <br> - 2.5A | [Ebay](https://www.ebay.com/itm/Dual-Channel-DC-Motor-Driver-Mini-Module-PWM-Speed-Control-Beyond-L298N-S2U/124342998274?hash=item1cf36b9502:g:zJoAAOSwFuZbSF25) | 1€
[<img src="extras/Images/l298n.jpg" style="height:100px">](https://www.ebay.com/itm/L298N-DC-Stepper-Motor-Driver-Module-Dual-H-Bridge-Control-Board-for-Arduino/362863436137?hash=item547c58a169:g:gkYAAOSwe6FaJ5Df)| Stepper driver L298N| - L298N  chip <br> - 1 motor <br>- 5V-35V <br> - 2A(MAX single bridge) | [Ebay](https://www.ebay.com/itm/L298N-DC-Stepper-Motor-Driver-Module-Dual-H-Bridge-Control-Board-for-Arduino/362863436137?hash=item547c58a169:g:gkYAAOSwe6FaJ5Df) | 2€
[<img src="extras/Images/shiled_stepper.jpg" style="height:100px">](https://www.ebay.com/itm/L298P-Shield-R3-DC-Motor-Driver-Module-2A-H-Bridge-2-way-For-Arduino-UNO-2560/310787745501?hash=item485c64a6dd:g:m0sAAOSwXwdfMo5O)| Shield R3 DC Motor Driver Module| - L298P  chip <br> - 1 motor <br>- 5V-35V <br> - 2A(MAX single bridge) | [Ebay](https://www.ebay.com/itm/L298P-Shield-R3-DC-Motor-Driver-Module-2A-H-Bridge-2-way-For-Arduino-UNO-2560/310787745501?hash=item485c64a6dd:g:m0sAAOSwXwdfMo5O) | 6€
[<img src="extras/Images/shiled_stepper1.jpg" style="height:100px">](https://www.ebay.com/itm/L298P-Shield-R3-DC-Motor-Driver-Module-2A-H-Bridge-2-way-For-Arduino-UNO-2560/310787745501?hash=item485c64a6dd:g:m0sAAOSwXwdfMo5O)| Arduino Motor Drive Shield V1| - L293D chip <br> - 1 motor <br>- 5V-35V <br> - 0.6A( 1.2 peak) | [Ebay](https://www.ebay.com/itm/L298P-Shield-R3-DC-Motor-Driver-Module-2A-H-Bridge-2-way-For-Arduino-UNO-2560/310787745501?hash=item485c64a6dd:g:m0sAAOSwXwdfMo5O) | 6€
[<img src="extras/Images/shiled_stepper3.jpg" style="height:100px">](https://www.ebay.com/itm/Motor-Stepper-Servo-Robot-Shield-for-Arduino-I2C-v2-Kit-w-PWM-Driver-TOP/201415058167?hash=item2ee545e2f7:g:IkgAAOSwJ-5aTI4Q)| Arduino Motor Drive Shield V2| - TB6612 chip <br> - 1 motor <br>- 5V-35V <br> - 1.2A (3A peak) | [Ebay](https://www.ebay.com/itm/Motor-Stepper-Servo-Robot-Shield-for-Arduino-I2C-v2-Kit-w-PWM-Driver-TOP/201415058167?hash=item2ee545e2f7:g:IkgAAOSwJ-5aTI4Q) | 25€
[<img src="extras/Images/shield_monster.jpg" style="height:100px">](https://www.ebay.com/itm/L298N-Dual-VNH2SP30-Stepper-Motor-Driver-Module-30A-Monster-Moto-Shield-Replace/112031018900?hash=item1a1591af94:g:R4YAAOSwEaBaTafh)| Arduino Monster motor shield| - VNH2SP30  chip <br> - 1 motor <br>- 5V-16V <br> - 14A (30A peak) | [Ebay](https://www.ebay.com/itm/L298N-Dual-VNH2SP30-Stepper-Motor-Driver-Module-30A-Monster-Moto-Shield-Replace/112031018900?hash=item1a1591af94:g:R4YAAOSwEaBaTafh) | 8€

<span class="simple">Simple<span class="foc">FOC</span>library</span> will also support some fully integrated solutions

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="extras/Images/smartstepper.png" style="height:100px">](https://fr.aliexpress.com/item/1005002994341057.html?spm=a2g0o.productlist.0.0.6b7c1dc3oF0O7M&algo_pvid=211a8bd5-fe47-4797-aab2-afb283ded43b&algo_exp_id=211a8bd5-fe47-4797-aab2-afb283ded43b-37&pdp_ext_f=%7B%22sku_id%22%3A%2212000023120630182%22%7D)| Misfit Smart Stepper| - samd21 mcu <br> - A4954 driver chip <br> - Easy install om NEMA17 <br> - AS5047A sensor integrated <br> - 1 motor <br>- 8V-32V <br> - 2A | [AliExpress](https://fr.aliexpress.com/item/1005002994341057.html?spm=a2g0o.productlist.0.0.6b7c1dc3oF0O7M&algo_pvid=211a8bd5-fe47-4797-aab2-afb283ded43b&algo_exp_id=211a8bd5-fe47-4797-aab2-afb283ded43b-37&pdp_ext_f=%7B%22sku_id%22%3A%2212000023120630182%22%7D)<br> [MisfitTech](https://misfittech.net/nema-17-smart-stepper/) | 20-40€
[<img src="extras/Images/BIGTREETECH_S42B_v1.1.png" style="height:100px">](https://fr.aliexpress.com/wholesale?catId=0&initiative_id=SB_20211004101249&SearchText=BIGTREETECH+S42B)| BIGTREETECH S42B |  - stm32 mcu <br> - 2x A4950 driver chips <br> - Easy install om NEMA17 <br> - TLE5012 sensor integrated <br> - 1 motor <br>- 12V-24V <br> - 2A <br> [see comunity post](https://community.simplefoc.com/t/running-simplefoc-on-a-bigtreetech-s42b/736) | [AliExpress](https://fr.aliexpress.com/wholesale?catId=0&initiative_id=SB_20211004101249&SearchText=BIGTREETECH+S42B) <br>[Amazon](https://www.amazon.fr/BIGTREETECH-commande-emp%C3%AAche-limpression-multiples/dp/B08JLN4PJR/ref=sr_1_5?__mk_fr_FR=%C3%85M%C3%85%C5%BD%C3%95%C3%91&dchild=1&keywords=BIGTREETECH+S42B&qid=1633371175&qsid=259-6486797-8245010&sr=8-5&sres=B08JLN4PJR%2CB0991B8ZRL%2CB0882QGFZR%2CB07WC71M4R%2CB08182XHZZ%2CB091M7MDS3%2CB08BR7WMS7%2CB08VRM44Z2%2CB097Y5N5KG%2CB07WPY4979%2CB07TJWSV51%2CB089SPPGKJ%2CB08N6C9XCL%2CB08FHM91F2%2CB08B67DHWZ%2CB08ZSD3QBP%2CB08B1G8W39%2CB097RBJ3YR%2CB07W1Q42KK%2CB08VS8PJX7) | 20-40€

Alternatively, you can use two DC motor driver boards, one for each phase of the motor. Here are some examples:

Examples | Description | Specifications | Link | Price
---- | ---- | ---- | ---
[<img src="extras/Images/pololu.jpg" style="height:100px">](https://www.pololu.com/product/1212) | Pololu MC33926 | - MC33926 chip <br> - 1 motor <br>- 5V-28V <br> - 5A MAX| [Drotek](https://store-drotek.com/212-brushless-gimbal-controller-l6234.html)<br> [Ebay](https://www.pololu.com/product/1212) | 18€
[<img src="extras/Images/BTS7960B.jpg" style="height:100px">](https://fr.aliexpress.com/item/32965904058.html)| Aideepen BTS7960B| - BTS7960B  chip <br> - 1 motor <br>- 5V-30V <br> - 43A MAX| [Aliexpress](https://fr.aliexpress.com/item/32965904058.html) | 10€


Finally you can also use two 3-phase [BLDC motor drivers](bldc_drivers). Each of the BLDC drivers has 3 half-bridges, from which you will need to use 2. When you combine two of them you will be using 4 output pins out of 6 they have.  
