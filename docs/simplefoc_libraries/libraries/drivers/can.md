---
layout: default
title: CAN communication
description: "Controlling SimpleFOC motors over CAN with the CANCommander from the SimpleFOCDrivers library."
nav_order: 5
permalink: /drivers_can
parent: <span class="simple">Simple<span class="foc">FOC</span>Drivers</span>
grand_parent: libraries
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
toc: true
---

# CAN communication

CAN is a robust bus for connecting several motor controllers with just two wires, which makes it the natural choice for robots and multi-axis machines. The `CANCommander` from the <span class="simple">Simple<span class="foc">FOC</span>Drivers</span> library gives every <span class="simple">Simple<span class="foc">FOC</span></span> node an address on the bus, and lets you read and write any motor value using the same [registers](drivers_registers) as the serial protocols.

[Source and protocol README](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/comms/can){: .btn .btn-github} [Examples](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/examples/comms/can){: .btn .btn-github}

## Requirements

- A microcontroller with a CAN peripheral and a CAN transceiver, for example an STM32 with FDCAN (like the STM32G4) and a TJA1051, SIT1042 or similar transceiver.
- The [SimpleCANio](https://github.com/simplefoc/SimpleCANio) library, which provides the hardware layer.
- 120Ω termination resistors at both physical ends of the bus.

On STM32 with the Arduino framework, the FDCAN HAL module has to be enabled, e.g. in PlatformIO:

```ini
build_flags = -D HAL_FDCAN_MODULE_ENABLED
```

## Usage

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>
#include "comms/can/CANCommander.h"
#include "SimpleCANio.h"

BLDCMotor motor = BLDCMotor(7);

CANio can(CAN_RX_PIN, CAN_TX_PIN);  // see SimpleCANio for the optional pins
CANCommander commander(can, 0x12);  // this node's address on the bus

void setup() {
  // ... motor setup, motor.init(), motor.initFOC()
  commander.init();
  commander.addMotor(&motor);
}

void loop() {
  motor.loopFOC();
  motor.move();
  commander.run();
}
```

Every node needs a **unique address** from 0x00 to 0xFE. Address 0xFF is the broadcast address: messages sent to it are received by all nodes.

## The protocol

The protocol uses 29-bit extended CAN IDs. The address is in the most significant bits, so each node can filter its messages in hardware:

```
Bits [27:20]  Node address    (8 bits)
Bits [19:16]  Packet type     (4 bits)
Bits [15:8]   Register number (8 bits)
Bits [7:0]    Motor index     (8 bits)
```

| Packet type | Value | Data |
| --- | --- | --- |
| Read request | 0x1 | empty |
| Write request | 0x2 | the new value |
| Read response | 0x3 | the register value |
| Sync | 0xF | optional |

Values are sent as raw little-endian binary: `float` as 4 bytes IEEE 754, integers as 1 or 4 bytes. A frame carries at most 8 bytes, so up to two floats. Larger values are split into separate register reads.

For example, to set the target of motor 0 on node 0x12 to 2.0, send a write request with ID `0x12 << 20 | 0x2 << 16 | REG_TARGET << 8 | 0` and the 4 bytes of the float `2.0f` as data. If `echo` is enabled on the node, it answers with a read response containing the new value.

The full register list is in the [register reference](https://github.com/simplefoc/Arduino-FOC-drivers/blob/master/src/comms/REGISTERS.md).

## Controlling nodes

### From a computer

With a USB-CAN adapter, you can control the nodes from Python using [PySimpleFOC](https://github.com/simplefoc/pysimplefoc), which implements the protocol on top of `python-can`. On Linux, most USB-CAN adapters (e.g. CANable) show up as a SocketCAN interface:

```bash
sudo slcand -o -c -s8 /dev/ttyACM0 can0   # -s8 = 1Mbps
sudo ip link set can0 up
```

### From another microcontroller

`CANNodeInteractor` is the counterpart of `CANCommander` for a node that sends requests, for example a main controller coordinating several motor nodes:

```cpp
CANio can(CAN_RX_PIN, CAN_TX_PIN);
CANNodeInteractor interactor(can, 0x01); // this node's own address

// read the status of motor 0 on node 0x12
interactor.sendRead(0x12, REG_STATUS, 0);
if (interactor.waitForResponse()) {
  uint8_t status = 0;
  interactor.decodeResponseUint8(status);
}

// set the target of motor 0 on node 0x12
interactor.sendWriteFloat(0x12, REG_TARGET, 1.2f, 0, true);
```

A node can run both a `CANCommander` and a `CANNodeInteractor`, to have motors of its own and talk to other nodes at the same time. The interactor handles one request at a time, with a response timeout.

## Custom registers

You can expose your own values over CAN by adding registers from `REG_CUSTOM_START` (0xE0) upwards, after `commander.init()`:

```cpp
float my_value = 0;

bool readMyValue(RegisterIO& io, FOCMotor* motor) { io << my_value; return true; }
bool writeMyValue(RegisterIO& io, FOCMotor* motor) { io >> my_value; return true; }

void setup() {
  // ...
  commander.init();
  commander.addCustomRegister(0xE0, 4, readMyValue, writeMyValue); // 4 bytes
}
```

Keep custom registers to 8 bytes or less, so they fit in a single CAN frame.
