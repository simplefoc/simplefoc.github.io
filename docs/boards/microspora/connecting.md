---
layout: default
title: Microspora Hardware
parent: <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>
grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "Microspora board showcase and getting started information."
nav_order: 1
permalink: /microspora_connection
has_children: false
has_toc: false
toc: true
---


# Connecting the hardware to <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>

<span class="simple">Simple<span class="foc">FOC</span>-microspora</span> is an all-in-one motor controller. It combines an STM32G431CBU6 microcontroller, a DRV8316 6-PWM motor driver, an MT6701 magnetic encoder, current sensing, voltage sensing, USB-C, CAN, SPI, and a multifunction 5-pin header.

<img src="./extras/Images/microspora_connection_all.png" class="width50" alt="Microspora all connection"/>

## Motor connection

The board supports both BLDC motors and stepper motors through the hybrid stepper mode. 

<img src="./extras/Images/microspora_m1.png" class="width40" alt="Microspora motor connection"/>

Using BLDC motors is easy with this board, simply connect the three motor phases to the corresponding outputs. 

<img src="./extras/Images/microspora_m2.png" class="width40" alt="Microspora motor connection"/>

For steppers, first identify the phases A and B of the motor. Then connect the A+ and B+ to the outside motor terminals, while the A- and B- are connected together to the inside motor terminal.

[Read a deeper dive in Hybrid Stepper mode](hybrid_stepper_theory){: .btn .btn-docs}

<blockquote class="warning"> <p class="heading">Motor phase resistance considerations.</p> 
This driver is not suitable for very low resistance BLDC motors, typically those below 0.1Ω. The RDS(on) of the DRV8316 MOSFETs is around 100mΩ which is comparable to the high-power BLDC motors and can cause significant heating and power loss.
This driver's max current is around 8A, so take this in consideration when selecting motors and operating conditions.
</blockquote>

## Magnet and mounting

The MT6701 magnetic encoder sits on the **bottom side** of the board, in the centre. The board mounts on the back of the motor with the sensor facing the motor shaft, and a magnet on the end of the shaft gives the position of the rotor.

### The magnet

Use a **6mm diametrically magnetized magnet**: the north and south poles are on the two halves of the round face, so the field rotates as the shaft turns.

<blockquote class="warning" markdown="1"><p class="heading">Diametric, not axial</p>
Axially magnetized magnets (poles on the two flat faces) look exactly the same, but they won't work: the field doesn't change as the magnet turns, so the sensor can't measure the angle. Always check the magnetization when buying magnets.
</blockquote>

Fix the magnet to the end of the motor shaft, for example with a drop of glue or with a 3D-printed holder. Steel shafts hold the magnet magnetically, but glue is still recommended so it can't move during operation.

### Air gap and centring

- **Air gap**: keep **1-2mm** between the magnet and the sensor chip. Too far, and the field is too weak for a reliable reading. Too close, and the magnet may touch the board when the motor heats up or vibrates.
- **Centring**: the magnet should be centred right under the sensor. A small offset adds a periodic error to the measured angle, which shows up as torque ripple and noise in velocity control.

Small leftover centring errors can be compensated in software with the `CalibratedSensor` from the SimpleFOCDrivers library, see [calibrating the sensor](microspora_code#calibrating-the-sensor).

If the sensor reads nothing or `initFOC()` fails, check the magnet first: wrong magnetization, too large a gap or a badly centred magnet are the most common causes. Use the [sensor test](microspora_code#step-1-testing-the-sensor) to check the readings before running the motor.

### Mounting the board

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/holes.png" class="width50" alt="Microspora mounting holes"/>

The board can be mounted using the three mounting slots, which accept M2 and M3 screws. They allow the board to be attached directly to the back of many common motors, at a distance that sets the air gap to the magnet. If the hole distances do not match your motor, a simple 3D-printed adapter can be used instead.

The minimal hole distance is 22mm (corresponding to the holes of GM2804 and GM5208 motors), and the maximal hole distance is 28mm (corresponding to the holes of GM4108 motors).

<img style="height:200px" src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/gim_big.jpg" alt="Microspora on a large gimbal motor"/><img style="height:200px" src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/gim_med.jpg" alt="Microspora on a medium gimbal motor"/><img style="height:200px" src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/nema.jpg" alt="Microspora on a NEMA17 stepper"/>

When designing an adapter or enclosure, you can use the [3D model of the board](https://github.com/simplefoc/SimpleFOC-microspora/tree/main/3d%20model) (STEP format) to get the sensor position and the air gap right.

## Power supply

The board allows a range of power-supply voltage from **5-35V DC**. 
The DRV8316 provides an onboard 5V buck output for the board and supported peripherals. But the maximal current it can supply is limited to around 200mA. Which is typically sufficient for sensors and low-power peripherals. Only 3.3V output is available for peripherals using the JST connectors.

## CAN connection


<img src="./extras/Images/microspora_connection_can.png" class="width50" alt="Microspora can connection"/>

The board has two 3-pin JST connectors for CAN daisy chaining. They are electrically connected so additional Microspora boards can be connected in a chain.

| Pin | Signal |
| --- | --- |
| 1 | GND |
| 2 | CAN_H |
| 3 | CAN_L |

Use a 120Ω termination resistor at the physical ends of the CAN network. To disable the termination resistor, use a sharp hobby knife (e.g. X-Acto) to cut the trace connecting the resistor. You can always re-enable it by soldering the solder pad back together.

The firmware examples use a 1Mbps CAN bus and include Python scripts for scanning the bus and sending motor commands. See the [CAN scripts](https://github.com/simplefoc/microspora_simplefoc_firmware/tree/main/can_scripts).

The CAN transceiver uses the following MCU signals:

| Signal | Pin | Description |
| --- | --- | --- |
| CAN_RX | PB8 | CAN receive |
| CAN_TX | PB9 | CAN transmit |
| CAN_ENABLE | PC13 | CAN transceiver enable |

## 6-pin SPI header

<img src="./extras/Images/microspora_connection_6pin.png" class="width30" alt="Microspora 6-pin connection"/>

The 6-pin JST header exposes SPI3. This bus is shared with the DRV8316 driver, so configure the driver before using the header for another SPI device.

| Pin | Signal | Function |
| --- | --- | --- |
| 1 | GND | Ground |
| 2 | 3.3V | 3.3V output |
| 3 | PB5 | MOSI |
| 4 | PB4 | MISO |
| 5 | PC10 | SCK |
| 6 | PC11 | CS |

## 5-pin multifunction header

<img src="./extras/Images/microspora_connection_5pin.png" class="width30" alt="Microspora 5-pin connection"/>

The 5-pin JST header can be used for I2C, UART, an external encoder, PWM, step/dir, or general-purpose I/O. It is compatible with many Qwiic and STEMMA QT cables; when using a 4-pin cable, leave the fifth signal unconnected as appropriate for the selected function.

| Pin | Signal | I2C | UART | Encoder / timer | PWM | Step/dir |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | GND | - | - | - | - | - |
| 2 | 3.3V | - | - | - | - | - |
| 3 | PA14 | SDA (I2C1) | RX (UART2) | TIM8_CH2 | PWM input (TIM8_CH2) | Direction (PA14) |
| 4 | PA15 | SCL (I2C1) | TX (UART2) | TIM8_CH1 | PWM input (TIM8_CH1) | Direction (PA15) |
| 5 | PB6 | - | - | TIM8_ETR | Direction (PB6) | Step input (TIM8_ETR) |

## LEDs

The board has the following LEDs:
- **Power LED**: indicates the power-supply is connected.
- **User LED**: A LED on PC6, can be controlled by the user.

But you can also add many more using auxiliary JST connectors, especially if using STEMMA QT or Qwiic compatible devices.

## Programming the board

The board is programmed over USB-C in DFU mode, no external programmer needed. See the toolchain guide for PlatformIO and Arduino IDE setup and the DFU steps.

[Toolchain and flashing](microspora_setup){: .btn .btn-docs} [Write your own firmware](microspora_code){: .btn .btn-docs}
