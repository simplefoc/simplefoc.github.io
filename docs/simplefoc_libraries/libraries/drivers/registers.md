---
layout: default
title: Registers and telemetry
description: "SimpleFOC registers, PacketCommander and Telemetry: reading, writing and monitoring any motor value over ASCII or binary protocols."
nav_order: 4
permalink: /drivers_registers
parent: <span class="simple">Simple<span class="foc">FOC</span>Drivers</span>
grand_parent: libraries
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
toc: true
---

# Registers, PacketCommander and telemetry

The communication modules of the <span class="simple">Simple<span class="foc">FOC</span>Drivers</span> library are all built on one idea: **registers**. Every value of a motor that you might want to read or change has a register number: the target, the enable state, the angle and velocity, the PID gains, the limits, the measured currents and so on.

Once you can read and write registers, the channel doesn't matter. The same register numbers are used by:

- [PacketCommander](#packetcommander), over serial or any Arduino `Stream`, in ASCII or binary
- [Telemetry](#telemetry), to stream register values at a fixed rate
- [CAN](drivers_can), over a CAN bus
- [I2CCommander](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/comms/i2c), over I2C
- [Settings storage](drivers_settings), to save registers to flash

and by the Python library [PySimpleFOC](https://github.com/simplefoc/pysimplefoc) on the computer side.

<blockquote class="info" markdown="1"><p class="heading">Registers or Commander?</p>
The [Commander interface](commander_interface) in the main library is simpler to start with and is what the [web controller](webcontroller) uses. The register-based protocols are better when a program, not a person, talks to the motor: they are compact, have a binary option, support multiple motors cleanly, and work the same over serial, CAN and I2C.
</blockquote>

## Registers

The most important registers:

| ID | Register | Access | Description |
| --- | --- | --- | --- |
| 0x00 | `REG_STATUS` | read | Motor status |
| 0x01 | `REG_TARGET` | read/write | Target, meaning depends on the control mode |
| 0x04 | `REG_ENABLE` | read/write | Enable or disable the motor |
| 0x05 | `REG_CONTROL_MODE` | read/write | Motion control mode (torque, velocity, angle, ...) |
| 0x06 | `REG_TORQUE_MODE` | read/write | Torque control type (voltage, DC current, FOC current) |
| 0x09 | `REG_ANGLE` | read | Shaft angle |
| 0x11 | `REG_VELOCITY` | read | Shaft velocity |
| 0x22 | `REG_CURRENT_Q` | read | Q-axis current |
| 0x30-0x35 | `REG_VEL_PID_P` ... `REG_VEL_LPF_T` | read/write | Velocity PID gains, limit, ramp and filter |
| 0x36-0x3B | `REG_ANG_PID_P` ... `REG_ANG_LPF_T` | read/write | Angle PID gains, limit, ramp and filter |

The complete list, with data types, is in the [register reference](https://github.com/simplefoc/Arduino-FOC-drivers/blob/master/src/comms/REGISTERS.md). The authoritative definition is `SimpleFOCRegisters.h`.

## PacketCommander

`PacketCommander` receives register reads and writes as packets, and sends back the responses. Combine it with `TextIO` for a human-readable ASCII protocol, or `BinaryIO` for a compact binary one.

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>
#include "comms/streams/PacketCommander.h"
#include "comms/streams/TextIO.h"

BLDCMotor motor = BLDCMotor(7);
TextIO comms = TextIO(Serial);
PacketCommander packetCommander = PacketCommander();

void setup() {
  Serial.begin(115200);
  // ... motor setup
  packetCommander.addMotor(&motor);
  packetCommander.echo = true; // respond to writes with the new value
  packetCommander.init(comms);
}

void loop() {
  motor.loopFOC();
  motor.move();
  packetCommander.run();
}
```

In ASCII mode, `R` followed by a register number reads a register, and `R<reg>=<value>` writes it. Register numbers are written in **decimal**, so `REG_VELOCITY` (0x11) is `R17`. Each packet ends with a newline:

```
R1=10.0     set the target to 10.0        -> r1=10.0000
R4=1        enable the motor              -> r4=1
R0          read the status register      -> r0=4   (4 = motor ready)
```

For a binary protocol, replace `TextIO` with `BinaryIO`. It is more compact and faster to parse, but not human-readable, so it is meant to be used from software such as [PySimpleFOC](https://github.com/simplefoc/pysimplefoc).

## Telemetry

Telemetry sends a chosen set of registers automatically at a regular rate, for monitoring and logging. It can include registers from several motors, and the register list can be changed at runtime.

```cpp
#include "comms/telemetry/Telemetry.h"

TextIO io = TextIO(Serial);
Telemetry telemetry = Telemetry();
uint8_t telemetry_registers[] = { REG_TARGET, REG_ANGLE, REG_VELOCITY };

void setup() {
  // ... motor setup
  telemetry.addMotor(&motor);
  telemetry.setTelemetryRegisters(3, telemetry_registers);
  telemetry.downsample = 100; // send every 100th call of run(), 0 switches it off
  telemetry.init(io);
}

void loop() {
  motor.loopFOC();
  motor.move();
  telemetry.run();
}
```

The telemetry first sends a header packet describing the registers (e.g. `H0=0:1,0:9,0:17`: telemetry 0, registers 1, 9 and 17 (target, angle, velocity) of motor 0), followed by the data packets (e.g. `T0=2.0000,17.0982,1.1026`).

There are two variants for plotting tools, used the same way:

| Class | For |
| --- | --- |
| `SimpleTelemetry` | The Arduino IDE Serial Plotter: values on one line, separated by tabs |
| `TeleplotTelemetry` | The [Teleplot](https://github.com/nesnes/teleplot) extension for VS Code |

Telemetry and PacketCommander can share the same `TextIO` or `BinaryIO`, so you can control the motor and receive the telemetry over one serial port. The telemetry itself can also be configured through registers (`REG_TELEMETRY_REG`, `REG_TELEMETRY_DOWNSAMPLE`, ...).

<blockquote class="warning" markdown="1"><p class="heading">New code</p>
The telemetry is still marked as new and not fully tested in the library. Feedback on the [forum](https://community.simplefoc.com) is welcome.
</blockquote>

## Custom registers

You can add your own registers, for values specific to your application. Use register numbers from `REG_CUSTOM_START` (0xE0) and provide read and write handlers:

```cpp
bool readMyValue(RegisterIO& io, FOCMotor* motor) {
  io << my_value;
  return true;
}
bool writeMyValue(RegisterIO& io, FOCMotor* motor) {
  io >> my_value;
  return true;
}
```

See the [CAN page](drivers_can#custom-registers) for registering them with the commander.

[Communication sources on GitHub](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/comms){: .btn .btn-docs}
