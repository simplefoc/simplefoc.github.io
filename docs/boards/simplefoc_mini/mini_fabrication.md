---
layout: default
title: Fabrication
description: "Arduino SimpleFOCMini board fabrication"
parent: <span class="simple">Simple<span class="foc">FOC</span>Mini</span>
grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
nav_order: 2
permalink: /mini_fabrication
has_children: true
has_toc: false
toc: true
---


# Fabrication guide for <span class="simple">Simple<span class="foc">FOC</span>Mini</span> 
Here is a quick guide how to fabricate the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> board. The board has been designed using the EasyEDA software that makes it very easy to fabricate using the JLCPCB website. 


## Step by step guide
First step is to go to the [EasyEDA's project library](https://oshwlab.com/the.skuric/simplefocmini) and open the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> project in the EasyEDA editor. 
<p><img src="extras/Images/mini_fab (3).png" class="width60"></p>

Then you need to open the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> PCB file by clicking on it and then go to the top bar dropdown ***Fabrication*** and select ***One-click order PCB/SMT***
<p><img src="extras/Images/mini_fab (4).png" class="width60"></p>

The pop-up will show up asking if you want the Design Rule check, which is not necessary so you can click on ***No, continue and Order***. 
<p><img src="extras/Images/mini_fab (5).png" class="width60"></p>
Once the files are generated you the new pop-up will show up and there you just click ***OK***. 
<p><img src="extras/Images/mini_fab (6).png" class="width60"></p>
You will be redirected to the [JLCPCB site](https://cart.jlcpcb.com/quote?orderType=1&stencilLayer=2&stencilWidth=100&stencilLength=100&stencilCounts=5) with all the filled in entries. You just need to select he color and teh quantity that you would like to manufacture and go to the bottom where you can click on ***Confirm***
<p><img src="extras/Images/mini_fab (7).png" class="width60"></p>
The BOM and CPL files should be loaded automatically so then you should be able to click ***Next***. You need to provide the description of your purchase, ***DIY*** will do.
<p><img src="extras/Images/mini_fab (8).png" class="width60"></p>
In the next window you will see all the components that are shown in the BOM (Bill of materials), here I'd suggest you do uncheck the two through hole female header components H1 and P1.  And everything else should be fine, you can click ***Next***.
<p><img src="extras/Images/mini_fab (9).png" class="width60"></p>
And that is it, the last window will show your purchase summary and show you the visualisation of the board. The only thing left to do is to save it to teh cart and go through the payment and shipping information. 
<p><img src="extras/Images/mini_fab (1).png" class="width60"></p>


## Fabrication price

Price of a 5 board batch at JLCPCB website is around 32$, making it 7.5$ per board. The shipping cost will depend on how urgently you need your boards, the slowest option is the cheapest for 16 business days delivery time the shipping price is 3$ and for 2-5 day delivery you will pay from 20-50$. 