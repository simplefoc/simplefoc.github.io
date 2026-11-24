---
layout: default
title: Ordering from JLCPCB
parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "How to order any SimpleFOC board, fully assembled, from JLCPCB using the open-source fabrication files from GitHub."
nav_order: 7
permalink: /board_fabrication
toc: true
---

# Ordering a <span class="simple">Simple<span class="foc">FOC</span></span> board from JLCPCB

All <span class="simple">Simple<span class="foc">FOC</span></span> boards are open source, and their GitHub repositories contain everything needed to have them made: the Gerber files, the bill of materials (BOM) with an [LCSC](https://www.lcsc.com/) part number for every component, and the pick and place (CPL) file. This means you can order any of them fully assembled from [JLCPCB](https://jlcpcb.com/) in about 10 minutes.

This page shows the process once, with the QtFOC as the example, followed by the settings that are specific to each board.

<blockquote class="info" markdown="1"><p class="heading">Make it or buy it?</p>
JLCPCB makes at least 2 assembled boards per order (usually 5), and you pay a setup fee for each order. Unless you need several boards, or you want to modify the design, buying an assembled and tested board from [Makerfabs](https://www.makerfabs.com/open-hardware/cooperator/simplefoc.html) is usually cheaper and quicker.
</blockquote>

## Board settings at a glance

Board | GitHub repository | Layers | Assembly side | Notes
--- | --- | --- | --- | ---
[SimpleFOCMini v2.3](simplefocmini) | [SimpleFOCMini](https://github.com/simplefoc/SimpleFOCMini) | 2 | Top | [Mini fabrication](mini_fabrication)
[QtFOC v1.6](qtfoc) | [QtFOC](https://github.com/simplefoc/QtFOC) | 2 | Top | -
[SimpleFOCShield v4.1](arduino_simplefoc_shield_showcase) | [Arduino-SimpleFOCShield](https://github.com/simplefoc/Arduino-SimpleFOCShield) | 4 | Top | Don't place parts on the `SC...` solder pads, see [below](#simplefocshield-v4)
[SimpleFOC DriveShield v1.8](driveshield) | [SimpleFOC-DriveShield](https://github.com/simplefoc/SimpleFOC-DriveShield) | 4 | Top | Choose **2oz outer copper**, see [below](#simplefoc-driveshield)
[SimpleFOC-microspora v1.7](microspora_landing) | [SimpleFOC-microspora](https://github.com/simplefoc/SimpleFOC-microspora) | 4 | **Both sides** | CAN transceiver often needs a replacement, see the [microspora fabrication guide](microspora_fabrication)

All the boards use fine-pitch parts (the DRV8316 or DRV8320H in a 0.5mm pitch QFN package with a thermal pad), so they are not well suited for hand soldering. The steps below use the JLCPCB **PCB assembly (PCBA)** service.

## Step 1: Download the files from GitHub

Open the board's GitHub repository from the table above, click **Code > Download ZIP**, and unzip it.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber_download.png" class="width60" alt="Downloading the repository as a ZIP file from GitHub">

You will need three things from it:

- **The Gerber files**: the `Gerber` folder. JLCPCB wants them as a single zip file, so **zip the `Gerber` folder** (for example `Gerber.zip`).
- **The BOM**: the `BOM_....csv` file (or `.xlsx`).
- **The pick and place file**: the `PickAndPlace_....csv` file (or `.xlsx`), in the pick and place folder.

## Step 2: Upload the Gerber files

Go to [jlcpcb.com](https://jlcpcb.com/) and click **Get Instant Quote**.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber1.png" class="width60" alt="JLCPCB home page with the Get Instant Quote button">

Click **Add gerber file** and select the `Gerber.zip` you made.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber2.png" class="width40" alt="JLCPCB quote page with the Add gerber file button"> <img src="extras/Images/qtfoc_fab/qtfoc_gerber3.png" class="width40" alt="Selecting the Gerber zip file">

## Step 3: PCB options

JLCPCB shows the board and detects its size and number of layers. Check that the **layers** match the table above.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber4.png" class="width60" alt="JLCPCB quote page with the detected 2 layer board and the PCB options">

The defaults are fine for the rest, with one exception: for the **DriveShield**, set the **outer copper weight to 2oz** (in the PCB specifications further down).

## Step 4: PCB assembly

Scroll to the bottom of the page and enable **PCB Assembly**. Then:

- **PCBA type**: Standard
- **Assembly side**: Top Side, or **Both Sides** for the microspora
- **PCBA Qty**: how many of the PCBs should be assembled, 2 at least

<img src="extras/Images/qtfoc_fab/qtfoc_gerber5.png" class="width60" alt="JLCPCB PCB assembly options: standard PCBA, top side, 5 boards">

Click **Next**, and **Next** again on the PCB preview.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber6.png" class="width40" alt="Clicking Next after setting the PCB assembly options"> <img src="extras/Images/qtfoc_fab/qtfoc_gerber7.png" class="width40" alt="JLCPCB PCB preview">

## Step 5: Upload the BOM and the pick and place file

Click **Add BOM File** and select the `BOM_....csv` file, then **Add CPL File** and select the `PickAndPlace_....csv` file.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber8.png" class="width40" alt="JLCPCB Add BOM File button"> <img src="extras/Images/qtfoc_fab/qtfoc_gerber9.png" class="width40" alt="Selecting the BOM file">

<img src="extras/Images/qtfoc_fab/qtfoc_gerber10.png" class="width40" alt="JLCPCB Add CPL File button"> <img src="extras/Images/qtfoc_fab/qtfoc_gerber11.png" class="width40" alt="Selecting the pick and place file">

Then click **Process BOM & CPL**.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber12.png" class="width60" alt="JLCPCB Process BOM and CPL button">

JLCPCB may warn that some parts won't be assembled because they are not in the BOM. These are the through-hole headers and terminals (for the QtFOC: `P2`, `H1`, `TB_PWR1` and `U4`, the XIAO / QT Py footprint), which you solder yourself, so click **Continue**.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber13.png" class="width60" alt="JLCPCB warning about the parts missing from the BOM">

## Step 6: Check the BOM

JLCPCB lists all the components, matched to its parts library, and checks that they are in stock. All the parts should be selected.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber14.png" class="width40" alt="JLCPCB BOM review, top of the list"> <img src="extras/Images/qtfoc_fab/qtfoc_gerber15.png" class="width40" alt="JLCPCB BOM review, bottom of the list with the Next button">

- **A part is out of stock**: click the search button next to it and choose an equivalent part. The board pages list the known replacements, for example the CAN transceiver on the [microspora](microspora_fabrication).
- Never unselect the driver, the current sense amplifiers, or the passive components.

Click **Next**.

## Step 7: Check the placement

JLCPCB shows a preview of the assembled board. Check that all the parts are there, and pay attention to the orientation of the chips (the pin 1 dot), the LEDs and the diodes. If you're not sure, you can ask JLCPCB to confirm the placement before production, with **Confirm Parts Placement: Yes** in step 4.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber16.png" class="width60" alt="JLCPCB component placement preview of the QtFOC">

Click **Next**.

## Step 8: Order

Choose the product description, for example **Research/Education/DIY/Entertainment > DIY**, and click **Save to Cart**. Then go through the payment and shipping in the cart.

<img src="extras/Images/qtfoc_fab/qtfoc_gerber17.png" class="width40" alt="Choosing the product description"> <img src="extras/Images/qtfoc_fab/qtfoc_gerber18.png" class="width40" alt="JLCPCB order summary with the Save to Cart button">

<img src="extras/Images/qtfoc_fab/qtfoc_gerber19.png" class="width60" alt="JLCPCB shopping cart with the PCB and the PCB assembly">

For example, 5 assembled QtFOC boards cost about $110 in November 2026 ($4 for the PCBs, $106 for the assembly), plus about $23 for shipping. That is about $25 per board, most of it the setup and feeder fees, which are the same whatever the number of boards.

## Ordering from EasyEDA instead

You can also send a board to JLCPCB straight from its EasyEDA project, without downloading anything. Open the project linked on the board's page, open the PCB file, and click **Fabrication > One-click Order PCB/SMT** (EasyEDA Standard: Mini, QtFOC, DriveShield) or **Export > PCB/FPC One-Click Prototyping** (EasyEDA Pro: Shield, microspora). JLCPCB opens with the board already uploaded, and you continue from [step 3](#step-3-pcb-options). The [microspora fabrication guide](microspora_fabrication) shows this way step by step.

## Board specific notes

### SimpleFOCMini v2

- 2-layer board with all the parts on the top side.
- The pin headers are not in the BOM, solder them yourself.
- More details in the [Mini fabrication guide](mini_fabrication).

### QtFOC

- 2-layer board with all the parts on the top side, including the SPI JST connector.
- The XIAO / QT Py headers and the motor and power terminals are not in the BOM, solder them yourself.

### SimpleFOCShield v4

- 4-layer board, assembled on the top side.
- The pick and place file also lists the configuration solder pads (`SC...` designators) on the bottom side. They are only copper pads, not components, so don't place any parts for them.
- The Arduino stacking headers, the encoder header and the terminals are through-hole parts, solder them yourself.
- More details in the [Shield v4 fabrication guide](arduino_simplefoc_shield_fabrication_v4).

### SimpleFOC DriveShield

- 4-layer board, assembled on the top side.
- Choose **2oz outer copper**. The board runs noticeably cooler with it, see the [thermal measurements](driveshield#thermal-performance). The boards made by Makerfabs use 2oz too.
- The Arduino headers and the terminals are through-hole parts, solder them yourself.

### SimpleFOC-microspora

- 4-layer board, assembled on **both sides**: the MT6701 magnetic encoder is on the bottom.
- The CAN transceiver is often out of stock. The [microspora fabrication guide](microspora_fabrication) shows the whole process step by step, with the replacement part.

## Other PCB assembly services

Any PCBA service works with the Gerber, BOM and pick and place files from the GitHub repositories. The part numbers in the BOMs are LCSC numbers, so with other services you may need to look up the manufacturer part numbers (also in the BOM) or choose replacements.

Questions about fabricating a board? Ask on the [SimpleFOC community forum](https://community.simplefoc.com).
