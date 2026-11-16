---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span>Drivers</span>
description: "SimpleFOCDrivers library: hardware drivers, special sensors, communication and settings storage for the SimpleFOC library."
nav_order: 1
permalink: /drivers_library
parent: libraries
grand_parent: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
has_children: True
has_toc: False
toc: true
---

# <span class="simple">Simple<span class="foc">FOC</span>Drivers</span> library

![Library Compile](https://github.com/simplefoc/Arduino-FOC-Drivers/workflows/Library%20Compile/badge.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![arduino-library-badge](https://ardubadge.simplefoc.com?lib=SimpleFOCDrivers)

The <span class="simple">Simple<span class="foc">FOC</span>library</span> is kept lean on purpose: motor control algorithms, PWM support for the different MCUs, and generic sensors and communication. Everything that is specific to a particular chip, or that not everyone needs, lives in the <span class="simple">Simple<span class="foc">FOC</span>Drivers</span> library:

- **Driver ICs** with SPI or I2C configuration, like the DRV8316 or TMC6200
- **Sensor drivers** for over 20 position sensor ICs, plus special sensors like the `CalibratedSensor`
- **Sensorless control** with a flux observer, no position sensor needed
- **Communication**: CAN, binary and ASCII packet protocols, telemetry, I2C
- **Settings storage**: save the motor calibration and tuning to flash or EEPROM
- **Utilities**: STM32 CORDIC acceleration, PWM input, motion planning

[Source on GitHub](https://github.com/simplefoc/Arduino-FOC-drivers){: .btn .btn-docs}

## Guides

These modules have a dedicated page in the docs:

| Guide | What it's for |
| --- | --- |
| [DRV8316 driver](drivers_drv8316) | Configuring and diagnosing the DRV8316 integrated driver, current sensing, fault handling |
| [Calibrated sensor](drivers_calibrated_sensor) | Compensating magnet eccentricity errors with a lookup table |
| [Sensorless flux observer](drivers_sensorless) | Running FOC without a position sensor, estimating the angle from the phase currents |
| [Registers, PacketCommander and telemetry](drivers_registers) | Reading and writing any motor value, binary and ASCII protocols, monitoring |
| [CAN communication](drivers_can) | Controlling one or more motors over a CAN bus |
| [Settings storage](drivers_settings) | Saving calibration and tuning so you can skip them on startup |

For all the other modules, see the README in each module's folder, linked in the catalog below.

## Installation

The library is available in the Arduino Library Manager and PlatformIO as **Simple FOC Drivers**.

- **Arduino IDE**: `Tools > Manage Libraries...`, search for `Simple FOC drivers` and install it.
- **PlatformIO**: add it to your `platformio.ini`:

```ini
lib_deps =
    Simple FOC
    SimpleFOCDrivers
```

To get the newest features before they are released, use the `dev` branch from GitHub instead: `https://github.com/simplefoc/Arduino-FOC-drivers.git#dev`.

## Usage

Include the library header, and then the header of the module you want to use:

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>
#include "encoders/as5048a/MagneticSensorAS5048A.h"
```

The modules are designed to slot into normal <span class="simple">Simple<span class="foc">FOC</span>library</span> code: a sensor driver is a `Sensor`, a driver IC class is a `BLDCDriver`, so you link them to the motor exactly like the built-in classes.

## Catalog

### Motor and gate driver ICs

Drivers for ICs with an SPI or I2C configuration and status interface. They extend the `BLDCDriver3PWM` / `BLDCDriver6PWM` classes, so they work as drop-in replacements.

| Module | Description |
| --- | --- |
| [DRV8316](drivers_drv8316) | TI DRV8316 integrated 3-phase driver with current sensing, SPI |
| [DRV8376](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/drivers/drv8376) | TI DRV8376 integrated 3-phase driver, SPI |
| [DRV8320 / DRV8323](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/drivers/drv832x) | TI DRV832x gate drivers, SPI |
| [TMC6200](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/drivers/tmc6200) | Trinamic TMC6200 gate driver, SPI |
| [STSPIN32G4](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/drivers/stspin32g4) | ST STSPIN32G4 gate driver with integrated STM32G4 MCU, I2C |
| [SimpleFOCNano](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/drivers/simplefocnano) | Driver preconfigured for the SimpleFOCNano board |

### Position sensors

Hardware-specific sensor drivers. Compared to the generic `MagneticSensorSPI` / `MagneticSensorI2C`, they support chips the generic classes can't (SSI, half-duplex SPI, special protocols) and give access to status and configuration registers.

| Module | Description |
| --- | --- |
| [AS5047](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/as5047) | AMS AS5047P / AS5047D, SPI |
| [AS5047U](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/as5047u) | AMS AS5047U, SPI |
| [AS5048A](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/as5048a) | AMS AS5048A, SPI |
| [AS5145](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/as5145) | AMS AS5145 / AS5045, SSI |
| [AS5600](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/as5600) | AMS AS5600 / AS5600L, I2C |
| [AEAT-8800-Q24](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/aeat8800q24) | Broadcom AEAT-8800-Q24, mixed SPI/SSI |
| [A1334](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/a1334) | Allegro A1334, SPI |
| [KTH78xx](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/kth78xx) | Conntek KTH78xx series, SPI |
| [MA330](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/ma330) | MPS MagAlpha MA330, SPI |
| [MA730](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/ma730) | MPS MagAlpha MA730, SPI or SSI |
| [MA735](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/ma735) | MPS MagAlpha MA735, SPI or SSI |
| [MT6701](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/mt6701) | MagnTek MT6701, SSI |
| [MT6816](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/mt6816) | MagnTek MT6816, SPI |
| [MT6826S](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/mt6826s) | MagnTek MT6826S, SPI |
| [MT6835](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/mt6835) | MagnTek MT6835 21-bit, SPI |
| [SC60228](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/sc60228) | SemiMent SC60228, SPI |
| [TLE5012B](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/tle5012b) | Infineon TLE5012B, half-duplex SPI (STM32) |
| [STM32 hardware encoder](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/stm32hwencoder) | ABI quadrature encoders using STM32 timers, no interrupts needed |
| [STM32 low power encoder](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/stm32lpencoder) | ABI quadrature encoders using STM32 LPTIM timers |
| [ESP32 hardware encoder](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/esp32hwencoder) | ABI quadrature encoders using the ESP32 pulse counter |
| [STM32 PWM sensor](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/stm32pwmsensor) | Sensors with PWM output, measured with STM32 timers |

### Special sensors

Sensors that wrap or replace another sensor to improve or extend it.

| Module | Description |
| --- | --- |
| [CalibratedSensor](drivers_calibrated_sensor) | Compensates magnet eccentricity with a calibration lookup table |
| [SmoothingSensor](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/smoothing) | Extrapolates the angle between sensor updates, for slow or low-resolution sensors |
| [HysteresisSensor](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/hysteresis) | Adds configurable hysteresis to any sensor |
| [LinearHall](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/linearhall) | Analog (linear) hall sensors with interpolation |
| [Flux observer](drivers_sensorless) | Sensorless control: estimates the rotor angle from the phase currents, requires current sensing |

### Communication

Most communication modules are built on the **registers** abstraction: every motor value (target, PID gains, limits, measured currents...) has a register number, and can be read or written the same way over any channel. See [Registers, PacketCommander and telemetry](drivers_registers).

| Module | Description |
| --- | --- |
| [PacketCommander](drivers_registers#packetcommander) | Read and write registers over serial, in ASCII or binary |
| [Telemetry](drivers_registers#telemetry) | Stream any registers at a fixed rate, for logging and plotting |
| [CAN](drivers_can) | Register-based CAN protocol, multiple nodes and motors per bus |
| [I2CCommander](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/comms/i2c) | Register-based control over I2C |
| [STM32 speed/dir input](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/comms/stm32speeddir) | Set the target velocity with PWM speed and direction inputs |

All register-based protocols are supported by the Python library [PySimpleFOC](https://github.com/simplefoc/pysimplefoc).

### Settings storage

Save motor calibration and settings, so you can skip the calibration on startup. See [Settings storage](drivers_settings).

| Module | Description |
| --- | --- |
| [STM32 flash](drivers_settings#stm32-flash) | STM32G4 on-board flash |
| [SAMD NVM](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/settings/samd) | SAMD on-board flash or RWWEE memory |
| [CAT24 I2C EEPROM](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/settings/i2c) | External I2C EEPROMs (unfinished) |

### Utilities

| Module | Description |
| --- | --- |
| [STM32 CORDIC](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/utilities/stm32math) | Hardware-accelerated sine and cosine on STM32 MCUs with a CORDIC unit (e.g. STM32G4) |
| [STM32 PWM input](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/utilities/stm32pwm) | Measure PWM inputs with zero CPU overhead |
| [Trapezoidal planner](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/utilities/trapezoids) | Simple trapezoidal motion profiles |
| [Voltage sense](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/voltage) | Supply voltage measurement |

## How do we decide what goes there?

While working on the <span class="simple">Simple<span class="foc">FOC</span>library</span> we sometimes develop code that doesn't directly belong in the core library, or receive contributions that don't quite fit. The drivers library is their home. It holds code that is:

- hardware-specific, like a driver for a specific sensor or driver chip
- related to motor control, but not needed by everyone
- an implementation of a specific communication protocol

Keep in mind that code in the drivers library is used by fewer people than the core library, so it is less tested and less completely documented, and generally better suited to more experienced users. If you get stuck, ask on the [community forum](https://community.simplefoc.com).
