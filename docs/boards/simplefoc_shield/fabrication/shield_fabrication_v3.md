---
layout: default
title: Shield v3.x
description: "Arduino SimpleFOCShield board fabrication"
parent: Board Fabrication
grand_parent: <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
nav_order: 3
permalink: /arduino_simplefoc_shield_fabrication_v3
toc: true
---

# Fabrication guide for <span class="simple">Simple<span class="foc">FOC</span>Shield</span> <small><i>v3.x</i></small> 
Here is a quick guide how to fabricate the Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> [version <i class="fa fa-tag"></i>V3.2](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases) 


<p align="">
<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v3.2/images/top.png"  class="width30"><img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v3.2/images/bottom.png"  class="width30">
</p>


<blockquote class="info"> 📢 Official Easy EDA project <a href="https://oshwlab.com/the.skuric/simplefocshield_copy_copy"> here <i class="fa fa-external-link"></i></a><br></blockquote>
<blockquote>
<a href="https://oshwlab.com/the.skuric/simplefocshield_copy_copy"><img src="extras/Images/shield_easyeda.jpg" class="width40"></a>
</blockquote>

## Board releases
To check the release timeline, click [here](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases) 

Version  | link | Release date | Comment
----- | ----- | ---- | ----
*Simple**FOC**Shield* v3.1 |release v3.1 | 10/22 | - Complete redesign <br> - Transition to DRV8313 <br> - Transition to ACS712 <br> - Smaller footprint: 56mm x 53mm<br> - Fault and reset pins exposed (optional) <br> - Fault led indication <br>- Fully developed using EasyEDA
*Simple**FOC**Shield* v3.2 |[release v3.2](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases/tag/v2.0.4) | 04/24 | - Official release <br> - Resolved the bug [#9](https://github.com/simplefoc/Arduino-SimpleFOCShield/issues/9)   





[<i class="fa fa-github"></i> Github](https://github.com/simplefoc/Arduino-SimpleFOCShield) repository contains: 
- <b><i class="fa fa-file"></i> Schematics</b> - PDF file
- <b><i class="fa fa-file"></i> Gerber</b> - Gerber, NC Drill & Assembly files
- <b><i class="fa fa-file"></i> Bill Of Materials (BOM)</b> - Excel sheet
- <b><i class="fa fa-file"></i> 3D model</b> -  3D PDF & STEP file
- <b><i class="fa fa-file"></i> PCB design project</b> - EasyEDA/Altium Designer


## Fabrication procedure and cost
The price of the fabrication will highly depend of the number of boards and the companies you decide to use both as PCB manufacturers and the component suppliers. Here are two nice articles explaining how to approach the problem choosing the PCB manufacturer form [electronics-notes.com](https://www.electronics-notes.com/articles/constructional_techniques/printed-circuit-board-pcb/how-to-choose-right-best-pcb-manufacturer.php) and component supplier from [electronics-notes.com](https://www.electronics-notes.com/articles/constructional_techniques/printed-circuit-board-pcb/how-to-choose-right-best-pcb-manufacturer.php). 
But in most of cases it will always boil down to the price :)

In general there are two main ways to fabricate the board:
- PCB fabrication + Self assembly - [Read more](#pcb-fabrication--self-assembly)
- PCB fabrication and assembly service 
   - Using JLCPB - [Read more](#step-by-step-guide-to-pcba-fabrication-with-jlcpcb---preferred-choice-) 📢 **preferred (easier) choice** 
   - Using other manufacturers - [Read more](#pcb-fabrication-and-assembly-service-with-other-manufacturers)
    


## PCB fabrication + Self assembly
The version v3 of the shiled does not longer use big SMD components as the versions v1 nad v2 did. So this version is a bit more demanding to self fabricate as all the resistors, capacitors and diods have 0603 footprint (as oposed to 1206 for v1 and v2). However, if you are interested in this way of producing this board here is a quick guide how to make it.

### PCB fabrication
Download the [<i class="fa fa-file"></i> gerber files](https://github.com/simplefoc/Arduino-SimpleFOCShield) and provide them to your PCB manufacturer of choice. So far I have used [seeedstrudio](https://www.seeedstudio.com/) and [JLCPCB](https://jlcpcb.com/). The price of 10 PCBs is around 5\$ but the shipping was around 20\$. 

Quantity | SeedStudio |  JLCPCB
---- | ---- | ---- | ---
 5 | 5€ | 2€
 10 | 5€ |2€
 30 | 30€ | 15€
 50 | 50€ | 30€
 
<i class="fa fa-info-circle"></i> <i><small>On top of these prices you will have to pay shipping of approximately  20€.</small></i>

### Component purchase
Here is a complete list of all the components you will need to make the board:
<table>
      <thead>
         <tr>
            <th>Component</th>
            <th>Description</th>
            <th>Quantity</th>
            <th> Manufacturer Number (Example only) </th>
         </tr>
      </thead>
      <tbody>
         <tr>
            <td colspan="4"><b>DRV8313 circuit:</b> <a href="https://www.ti.com/lit/ds/symlink/drv8313.pdf?ts=1719079575798"><i class="fa fa-file"></i> Datasheet</a></td>
         </tr>
         <tr>
            <td>C5</td>
            <td>470 nF</td>
            <td>1</td>
            <td><a href="https://jlcpcb.com/partdetail/Yageo-CC0603ZRY5V6BB474/C327269">CC0603ZRY5V6BB474</a></td>
         </tr>
         <tr>
            <td>C6</td>
            <td>100 uF (Electrolytic)</td>
            <td>1</td>
            <td><a href="https://jlcpcb.com/partdetail/279343-VT1V101MCRE77/C294520">VT1V101M-CRE77</a></td>
         </tr>
         <tr>
            <td>C7, C8</td>
            <td>1 uF</td>
            <td>2</td>
            <td><a href="https://jlcpcb.com/partdetail/1943-CL10B104KB8NNNC/C1591">CL10B104KB8NNNC</a></td>
         </tr>
         <tr>
            <td>R6, R5</td>
            <td>4.7 kΩ</td>
            <td>2</td>
            <td><a href="https://jlcpcb.com/parts/componentSearch?searchTxt=0603WAF4701T5E">0603WAF4701T5E</a></td>
         </tr>
         <tr>
            <td>R3</td>
            <td>330 Ω</td>
            <td>1</td>
            <td><a href="https://jlcpcb.com/partdetail/23865-0603WAF3300T5E/C23138">0603WAF3300T5E</a></td>
         </tr>
         <tr>
            <td>DRV8313</td>
            <td>Motor driver chip</td>
            <td>1</td>
            <td><a href="https://jlcpcb.com/partdetail/TexasInstruments-DRV8313PWPR/C92482">DRV8313PWPR</a></td>
         </tr>
         <tr>
            <td colspan="4"><b>Pull Up and Pull Down resistors</b></td>
         </tr>
         <tr>
            <td>PULL_SCL, PULL_SDA, R2</td>
            <td>4.7 kΩ</td>
            <td>3</td>
            <td><a href="https://jlcpcb.com/parts/componentSearch?searchTxt=0603WAF4701T5E">0603WAF4701T5E</a></td>
         </tr>
         <tr>
            <td>PULL_A, PULL_B, PULL_I</td>
            <td>3.3 kΩ</td>
            <td>3</td>
            <td><a href="https://jlcpcb.com/partdetail/23705-0603WAF3301T5E/C22978">RN73H2ETTD3001F50</a></td>
         </tr>
         <tr>
            <td colspan="4"><b>LED circuit</b></td>
         </tr>
         <tr>
            <td>D1, D2</td>
            <td>LED (any color)</td>
            <td>2</td>
            <td><a href="https://jlcpcb.com/partdetail/73147-19_213_Y2C_CQ2R2L_3T_CY/C72038">19-213/Y2C-CQ2R2L/3T(CY)</a></td>
         </tr>
         <tr>
            <td>R1</td>
            <td>4.7 kΩ</td>
            <td>1</td>
            <td><a href="https://jlcpcb.com/parts/componentSearch?searchTxt=0603WAF4701T5E">0603WAF4701T5E</a></td>
         </tr>
         <tr>
            <td colspan="4"><b>Linear regulator circuit</b></td>
         </tr>
         <tr>
            <td>L1</td>
            <td>L78M08</td>
            <td>1</td>
            <td><a href="https://jlcpcb.com/partdetail/Stmicroelectronics-L78M08ABDTTR/C273659">L78M08ABDT-TR</a></td>
         </tr>
         <tr>
             <td>CL1</td>
            <td>0.1 uF</td>
            <td>1</td>
            <td><a href="https://jlcpcb.com/partdetail/Yageo-CC0603KRX7R9BB104/C14663">CC0603KRX7R9BB104</a></td>
         </tr>
         <tr>
            <td colspan="4"><b>Current sense circuit</b></td>
         </tr>
         <tr>
            <td>CBYP1,CBYP2</td>
            <td>1 uF</td>
            <td>2</td>
            <td><a href="https://jlcpcb.com/partdetail/16531-CL10A105KB8NNNC/C15849">CL10A105KB8NNNC</a></td>
         </tr>
         <tr>
             <td>CS1,CS2</td>
            <td>ACS712ELCTR-05B-T current sense amp</td>
            <td>2</td>
            <td><a href="https://jlcpcb.com/partdetail/45473-ACS712ELCTR_05BT/C44471">ACS712ELCTR-05B-T</a></td>
         </tr>
         <tr>
            <td colspan="4"><b>Terminal connectors</b></td>
         </tr>
         <tr>
            <td>TB_M1</td>
            <td>3 pin, 5mm</td>
            <td>1</td>
            <td><a href="https://www.mouser.fr/ProductDetail/CUI-Devices/TB002-500-03BE?qs=%2Fha2pyFadujMo%2F8XIx7GL3VaKbn4rpnI4huWO6RUre2577fclJuWwA%3D%3D">TB002-500-03BE</a></td>
         </tr>
         <tr>
            <td>TB_PWR</td>
            <td>2 pin, 5mm</td>
            <td>1</td>
            <td><a href="https://www.mouser.fr/ProductDetail/CUI-Devices/TB002-500-02BE?qs=%2Fha2pyFadujMo%2F8XIx7GL%2F8B4TM%252BUPJvcyODkgPPYDPGTjOBZNS5pw%3D%3D">TB002-500-02BE</a></td>
         </tr>
         <tr>
            <td colspan="4"><b>Header connectors</b></td>
         </tr>
         <tr>
            <td>P1, P4, P2, P3</td>
            <td>8 pin, 10 pin, 6 pin female, long</td>
            <td>2</td>
            <td><a href="https://www.sparkfun.com/products/11417">sparkfun</a> <a href="https://store.arduino.cc/strip-2x3-6-8-10-ways-arduino-uno-printed?queryID=70fa5b2be995a4e88b89876392222482">Arduino</a> </td>
         </tr>
         <tr>
            <td>P_ENC</td>
            <td> 5 pin, male/female</td>
            <td>1</td>
            <td><a href="https://www.mouser.fr/ProductDetail/649-1012937990501BLF ">649-1012937990501BLF</a></td>
         </tr>
      </tbody>
   </table>

The same list ( a bit optimized for PCB manufacturers ) you can download on link [<i class="fa fa-file"></i> Bill of Materials](https://github.com/simplefoc/Arduino-SimpleFOCShield). 
<blockquote class="warning"> <p class="heading">Note</p>
The components with the manufacturers numbers I have chosen are not really set in stone, feel free to optimize them both in terms of prices and values if necessary. </blockquote>

## Step by step guide to PCBA fabrication with JLCPCB - **preferred choice** 📢


Here is a quick guide how to fabricate the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> board. The board has been designed using the EasyEDA software that makes it very easy to fabricate using the JLCPCB website. 


## Step by step guide


First step is to go to the [EasyEDA's project library](https://oshwlab.com/the.skuric/simplefocshield_copy_copy) and open the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> project in the EasyEDA editor. 
<p><img src="extras/Images/v3 (1).jpg" class="width60"></p>

Then you need to open the <span class="simple">Simple<span class="foc">FOC</span>Shield</span> PCB file by clicking on it and then go to the top bar dropdown ***Fabrication*** and select ***One-click order PCB/SMT***
<p><img src="extras/Images/v3 (2).jpg" class="width60"></p>

The pop-up will show up asking if you want the Design Rule check, which is not necessary so you can click on ***No, continue and Order***. 
<p><img src="extras/Images/v3 (3).jpg" class="width60"></p>
<p><img src="extras/Images/v3 (4).jpg" class="width60"></p>
Once the files are generated you the new pop-up will show up and there you just click ***OK***. 
<p><img src="extras/Images/v3 (5).jpg" class="width60"></p>
You will be redirected to the [JLCPCB site](https://cart.jlcpcb.com/quote?orderType=1&stencilLayer=2&stencilWidth=100&stencilLength=100&stencilCounts=5) with all the filled in entries. You just need to select he color and the quantity that you would like to manufacture. 
<p><img src="extras/Images/v3 (6).jpg" class="width60"></p>
Then go to the bottom of the page and and click on ***Confirm***
<p><img src="extras/Images/v3 (7).jpg" class="width60"></p>
The gerber file viewer will open, there you can click **Next**. 
<p><img src="extras/Images/v3 (8).jpg" class="width60"></p>
The BOM and CPL files should be loaded automatically so then you should be able to click ***Continue***. 
<p><img src="extras/Images/v3 (9).jpg" class="width60"></p>

In the next window you will see all the components that are shown in the BOM (Bill of materials), here it's possible that link in my case you'll be missing some components due to the temporary shortage.
<p><img src="extras/Images/v3 (10).jpg" class="width60"></p>
If that is the case search replace the missing components with the ones that are available. 
- Make sure to add the footprint `0603` to the search prompt
- Check the in stock box
- Try checking Basic components box as well ( basic components are cheaper ) 
<p><img src="extras/Images/v3 (11).jpg" class="width60"></p>

Once you have all your components make sure to select them all and you can click ***Next***.
<p><img src="extras/Images/v3 (12).jpg" class="width60"></p>

The next window will show the placement of the components on the board. Here you can see the components on the board and verify that they are all oriented properly.
There are three components that are almost always misaligned
- LM7808 regulator
- both diodes
To correct their orientation select them and click on the ***Rotate*** twice!
<p><img src="extras/Images/v3 (13).jpg" class="width60"></p>

Once all the components are placed well, click on **Next**.
<p><img src="extras/Images/v3 (14).jpg" class="width60"></p>

That is it, the hard part is done. Next window asks the reason for the order, you can select the one that fits you the best. In my case, I usually select the ***DIY***.
<p><img src="extras/Images/v3 (15).jpg" class="width60"></p>

The only thing left to do is to save it to teh cart and go through the payment and shipping information. 
<p><img src="extras/Images/v3 (16).jpg" class="width60"></p>


## PCB fabrication and assembly service with other manufacturers
This option is a lot simpler and produces much better results. It is also even cheaper for larger quantities > 50pcs.
To order the boards, download the [<i class="fa fa-file"></i> Gerber files](https://github.com/simplefoc/Arduino-SimpleFOCShield) and [<i class="fa fa-file"></i> Bill of Materials](https://github.com/simplefoc/Arduino-SimpleFOCShield) and supply it to your manufacturer of choice. In my case, I'm using the [JLCPCB](https://jlcpcb.com/).

<iframe class="youtube"  src="https://www.youtube.com/embed/sax_9sUgBuk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<blockquote class="warning">This video is made for v2 version of the shield but the same procedure can be used for the v3.x as well. Additionally, this video was made in 2021, in the meantime, the JLCPCB website interface has evolved a bit, but the important steps are still the same.</blockquote>

## Fully assembled versions
Order your own fully tested and assembled Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>  from our [shop](https://simplefoc.com/shop).

