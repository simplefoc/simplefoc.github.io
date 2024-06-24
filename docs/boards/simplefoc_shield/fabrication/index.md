---
layout: default
title: Board Fabrication
description: "Arduino SimpleFOCShield board fabrication"
parent: <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
nav_order: 2
permalink: /arduino_simplefoc_shield_fabrication
has_children: true
has_toc: false
---
# Fabrication guide for <span class="simple">Simple<span class="foc">FOC</span>Shield</span> 
Here is a quick guide how to fabricate the Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> check the most [recent versions <i class="fa fa-tag"></i>](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases). Here we will showcase the two most recent releases of <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v1, <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v2 and <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v3.
    
<div class="image_icon width30" >
    <a href="arduino_simplefoc_shield_fabrication_v1" >
        <img style="width:50%;display:inline" src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v1.3.3/images/top.png" > <img style="width:50%;display:inline" src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v1.3.3/images/bottom.png" >
        <i class="fa fa-external-link-square fa-2x"></i>
        <p> <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v1</p>
    </a>
</div>

<div class="image_icon width30" >
    <a href="arduino_simplefoc_shield_fabrication_v2" >
        <img style="width:50%;display:inline" src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v2.0.4/images/top.png" > <img style="width:50%;display:inline" src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v2.0.4/images/bottom.png" >
        <i class="fa fa-external-link-square fa-2x"></i>
        <p > <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v2</p>
    </a>
</div>

<div class="image_icon width30" >
    <a href="arduino_simplefoc_shield_fabrication_v3" >
        <img style="margin-left:3%;margin-right:3%;width:42%;display:inline" src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v3.2/images/top.png" > <img style="margin-left:3%;margin-right:3%;width:42%;display:inline" src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v3.2/images/bottom.png" >
        <i class="fa fa-external-link-square fa-2x"></i>
        <p > <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v3</p>
    </a>
</div>

## Board releases
To check the release timeline, click [here](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases) 

Version  |link | Release date | Comment
----- | ----- | ---- | ----
*Simple**FOC**Shield* **v1.3** |[release v1.3](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v1.3) | 04/20 | Inital release
*Simple**FOC**Shield* v1.3.1 | [release v1.3.1](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v1.3.1) | 07/20 | added Nucleo stacking support
*Simple**FOC**Shield* v1.3.2 |[release v1.3.2](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v1.3.2) | 09/20 | added I2C pullups
*Simple**FOC**Shield* v1.3.3 |[release v1.3.3](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v1.3.3) | 12/20 | adapted L6234 circuit + full Arduino header
*Simple**FOC**Shield* **v2.0** |[release v2.0](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v2.0) | 01/21 | - 3A in-line current sensing <br>- 5V regulator <br>- new pinout for hardware config 
*Simple**FOC**Shield* v2.0.1 |[release v2.0.1](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v2.0.1) | 01/21 | - reduced via size <br> - configurable range
*Simple**FOC**Shield* v2.0.2 |[release v2.0.2](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v2.0.2) | 02/21 | replaced 7805(connected to 5V) with 78M08 (connected to VIN) to be compatible with stm32 Nucleo-64
*Simple**FOC**Shield* v2.0.3 |[release v2.0.3](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v2.0.3) | 03/21 | - Shortened the lines from ADC to current sense <br> - Typo fix : underside label switched phase A and phase B
*Simple**FOC**Shield* v2.0.4 |[release v2.0.4](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v2.0.4) | 09/21 | - Pullup config simplified <br> - Max input voltage 35V <br> - removed CAP2 for a CL1 <br> - Easy EDA version of the project
*Simple**FOC**Shield* v3.1 |release v3.1 | 10/22 | - Complete redesign <br> - Transition to DRV8313 <br> - Transition to ACS712 <br> - Smaller footprint: 56mm x 53mm<br> - Fault and reset pins exposed (optional) <br> - Fault led indication <br>- Fully developed using EasyEDA
*Simple**FOC**Shield* **v3.2** |[release v3.2](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v2.0.4) | 04/24 | - Official release <br> - Resolved the bug [#9](https://github.com/simplefoc/Arduino-SimpleFOCShield/issues/9)



## Fully assembled versions
Order your own fully tested and assembled Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>  from our [shop](https://simplefoc.com/simplefoc_shield_product).

