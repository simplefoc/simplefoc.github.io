---
layout: default
title: Mini Fabrication
description: "Arduino SimpleFOCMini board fabrication"
parent: <span class="simple">Simple<span class="foc">FOC</span>Mini</span>
grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
nav_order: 2
permalink: /mini_fabrication
has_children: false
has_toc: false
toc: true
---


# Fabrication guide for <span class="simple">Simple<span class="foc">FOC</span>Mini</span>

The <span class="simple">Simple<span class="foc">FOC</span>Mini</span> is designed in EasyEDA, with an LCSC part number for every component, so the easiest way to make it is to order it fully assembled from JLCPCB, straight from the EasyEDA project.

[Step by step guide: ordering from JLCPCB](board_fabrication){: .btn .btn-docs}

## Mini settings

Setting | Mini v2.3 | Mini v1.1
--- | --- | ---
EasyEDA project | [simplefocmini v2](https://oshwlab.com/the.skuric/simplefocmini_copy_copy) | [simplefocmini v1](https://oshwlab.com/the.skuric/simplefocmini)
Editor | EasyEDA Standard | EasyEDA Standard
Layers | 2 | 2
Assembly side | Top | Top
Parts to unselect in the BOM | None, all the parts are SMD | The through-hole female headers `H1` and `P1`

The pin headers are not part of the assembly, solder them yourself.

The [GitHub repository](https://github.com/simplefoc/SimpleFOCMini) also has the Gerber files, the BOM and the pick and place file, if you want to order from another PCBA service.

## Fully assembled boards

You can also buy the fully assembled and tested <span class="simple">Simple<span class="foc">FOC</span>Mini</span> v2.3 from [Makerfabs](https://www.makerfabs.com/simplefocmini.html). Unless you need several boards, it's usually cheaper than ordering a batch from JLCPCB.
