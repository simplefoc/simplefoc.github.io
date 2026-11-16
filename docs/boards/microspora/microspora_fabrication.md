---
layout: default
title: Microspora Fabrication
parent: <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>
grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
nav_order: 3
permalink: /microspora_fabrication
has_children: false
has_toc: false
toc: true
---


# Fabrication guide for <span class="simple">Simple<span class="foc">FOC</span></span> microspora 
Here is a quick guide how to fabricate the <span class="simple">Simple<span class="foc">FOC</span></span> microspora board. The board has been designed using the EasyEDA software that makes it very easy to fabricate using the JLCPCB website. 

## Step by step guide
First step is to go to the EasyEDA's project library ([EasyEDA](https://oshwlab.com/the.skuric/microspora-simplefoc-antun)) and open the <span class="simple">Simple<span class="foc">FOC</span></span> microspora project in the EasyEDA editor. 
<p><img src="extras/Images/ms_fab/1.png" class="width60"></p>

### EasyEDA editor
Then double click on the PCB file **Microspora_Board_4layer**
<p><img src="extras/Images/ms_fab/2.png" class="width60"></p>

To export the gerber files click on **Export>PCB One-Click Prototyping** 
<p><img src="extras/Images/ms_fab/3.png" class="width60"></p>

The pop-up will show up asking if you want the Design Rule check and click ***Yes, Check DRC***. 
<p><img src="extras/Images/ms_fab/4.png" class="width60"></p>
Once the files are generated you the new pop-up will show up and there you just click ***Confirm***. 
<p><img src="extras/Images/ms_fab/5.png" class="width60"></p>


### JLCPCB site

You will be redirected to the [JLCPCB site](https://cart.jlcpcb.com/) where you will be able to see your board outline. 
<p><img src="extras/Images/ms_fab/6.png" class="width60"></p>

Go to the bottom of the page and enable **PCB Assembly**
<p><img src="extras/Images/ms_fab/7.png" class="width60"></p>

Select PCBA type as **Standard**
<p><img src="extras/Images/ms_fab/8.png" class="width60"></p>
And select double-sided assembly: **Both Sides**
<p><img src="extras/Images/ms_fab/9.png" class="width60"></p>
Then click **Next**
<p><img src="extras/Images/ms_fab/10.png" class="width60"></p>
And **NEXT** again:
<p><img src="extras/Images/ms_fab/11.png" class="width60"></p>

Now you will come to the BOM selection screen. There may be some components that are not available in any given time, but they are usually always available. 
<p><img src="extras/Images/ms_fab/12.png" class="width60"></p>
The one component that is commonly missing is the CAN transceiver (CA-IF1044VD-Q1), you can replace it with **SIT1042ATK/3**.
First click on the **Search button**
<p><img src="extras/Images/ms_fab/13.png" class="width60"></p>
Then type the **SIT1042ATK/3** and click **Select** 
<p><img src="extras/Images/ms_fab/14.png" class="width60"></p>
Then you should have all components, and you can click **NEXT**
<p><img src="extras/Images/ms_fab/15.png" class="width60"></p>

Check visually that all components are populated on the board and click **NEXT** 
<p><img src="extras/Images/ms_fab/16.png" class="width60"></p>


At the end set the use of your board for example **Research/Education/DIY > DIY**
<p><img src="extras/Images/ms_fab/17.png" class="width60"></p>
And **Save to Cart**
<p><img src="extras/Images/ms_fab/18.png" class="width60"></p>

## Fabrication price

Price of a 5 board batch at JLCPCB website is around 225$ (November 2026), making it around 45$ per board, which is very expensive. In August of 2026 15 board price was around 350$, making it about 23$ per board, so timing might be important when ordering.  

<p><img src="extras/Images/ms_fab/20.png" class="width60"></p>


## BOM file

Here is the complete list of BOM components 

Designator|Comment|Footprint|JLCPCB Part #|
--|--|--|--
USB1|TYPE-C16PIN|USB-C-SMD_TYPE-C-6PIN-2MD-073|C2765186
U8|MT6701QT-STD|QFN-16_L3.0-W3.0-P0.50-TL-EP1.7|C2913974
R22,R23,R24,R26,R29,R31,R33|330Ω|R0402|C25104
U1|DRV8316CRRGFR|VQFN-40_L7.0-W5.0-P0.50-BL-EP5.7|C5447274
SW2,SW3|TSA002A3512A02|SW-SMD_BZCN_TSA002A35XXX|C2888932
R21,R27,R28,R32|10kΩ|R0402|C25744
R19,R20|100kΩ|R0402|C25741
L1|47uH|IND-SMD_L3.0-W3.0_PRS3015|C702780
CN13|SM05B-SRSS-TB(LF)(SN)|CONN-SMD_SM05B-SRSS-TB-LF-SN|C136657
LED4|red|LED0603-RD|C2286
R30|120Ω|R0402|C25079
C27,C30,C34,C36,C38,C42,C43,C47,C48,C50,C51|100nF|C0402|C307331
Q1|AO3401A|SOT-23_L2.9-W1.3-P1.90-LS2.4-BR|C15127
U9|STM32G431CBU6|UFQFPN-48_L7.0-W7.0-P0.50-BL-EP|C529356
X1|16MHz|CRYSTAL-SMD_4P-L2.5-W2.0-BL|C2875244
C28,C29|10pF|C0402|C32949
U11|CA-IF1044VD-Q1|DFN-8_L3.0-W3.0-P0.65-BL-EP2.45|C5382546
D1|BZT52B5V1S|SOD-323_L1.7-W1.3-LS2.6-RD|C19077434
LED2|yellow|LED0603-RD-YELLOW|C2287
C31,C32,C33|22pF|C0402|C1555
C23,C25,C26,C37,C40,C41|1uF|C0402|C52923
CN4,CN5|SM03B-SRSS-TB(LF)(SN)|CONN-SMD_SM03B-SRSS-TB-LF-SN-P|C160403
CN7|SM06B-SRSS-TB(LF)(SN)|CONN-SMD_SM06B-SRSS-TB-LF-SN|C160405
R2,R3,R16,R17,R25|5.1kΩ|R0402|C25905
D3|1N4148WS|SOD-323_L1.8-W1.3-LS2.5-RD|C2128
C35,C54|22uF|C0603|C59461
C44,C45,C46,C52,C57|10uF|C0805|C6119892
U10|XC6206P332MR-G|SOT-23-3_L2.9-W1.6-P1.90-LS2.8-BR|C5446
