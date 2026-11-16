---
layout: default
title: CAN communication
parent: Microspora Code
grand_parent: <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "Controlling Microspora boards over the CAN bus."
nav_order: 3
permalink: /microspora_can
has_children: false
has_toc: false
toc: true
---

# CAN communication

Every <span class="simple">Simple<span class="foc">FOC</span>-microspora</span> has a CAN transceiver and two daisy-chain connectors, so several boards can share one bus. See the [hardware guide](microspora_connection) for wiring and the termination resistor.

## Setting up the CAN commander

CAN uses the [SimpleCANio](https://github.com/simplefoc/SimpleCANio) library and the `CANCommander` from the [SimpleFOCDrivers](drivers_library) library. Each board needs a **unique address** on the bus.

```cpp
#include "comms/can/CANCommander.h"
#include "SimpleCANio.h"

CANio can(PB8, PB9, NC, PC13);     // RX, TX, (not used), transceiver enable
CANCommander command_can(can, 15); // this board's address on the bus

void setup() {
  // ... motor setup
  command_can.init();
  command_can.addMotor(&motor);
}

void loop() {
  command_can.run();
}
```

The firmware uses a **1Mbps** bus. With PlatformIO, add `-D HAL_FDCAN_MODULE_ENABLED` to your build flags (see [toolchain setup](microspora_setup)).

<blockquote class="warning" markdown="1"><p class="heading">CAN and the BOOT button</p>
The CAN RX pin (PB8) is shared with the BOOT button. Pressing BOOT while CAN is running will disturb the bus.
</blockquote>

## Controlling the boards from a computer

The firmware repository includes Python [CAN scripts](https://github.com/simplefoc/microspora_simplefoc_firmware/tree/main/can_scripts) built on [pysimplefoc](https://github.com/simplefoc/pysimplefoc). Install the dependencies:

```bash
pip install pyserial rx python-can
pip install git+https://github.com/simplefoc/pysimplefoc.git
```

On Linux, bring up a USB-CAN adapter (e.g. CANable on `/dev/ttyACM2`) as a SocketCAN interface at 1Mbps:

```bash
sudo slcand -o -c -s6 /dev/ttyACM2 can0
sudo ip link set can0 type can bitrate 1000000
sudo ip link set can0 up
```

Then:

- `python can_scripts/scan_bus.py` finds the boards on the bus
- `python can_scripts/move.py` sends motion commands to a board

[Back to writing the code](microspora_code){: .btn .btn-docs}
