---
layout: default
title: Connecting the hardware
parent: Starting with Shield
description: "Connecting SimpleFOCShield with your hardware."
nav_order: 2
permalink: /foc_shield_connect_hardware
grand_parent: <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
toc: true
---

# Connecting the hardware

<p>
<img src="extras/Images/connection.gif" class="width50">
</p>

Connecting the Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> to the microcontroller, BLDC or stepper motor, power-supply and the sensors is very straight forward. 

## Microcontroller
- Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> is designed to be easily stackable on all the devices featuring arduino headers, such as: [Arudino UNO](arduino_simplefoc_shield), Arudino MEGA, [Stm32 Nucleo](nucleo_connection) and similar.
- Bit it can also be used as a standalone BLDC driver as shown in combination with [Stm32 Bluepill](bluepill_connection).

## BLDC motor
- Motor phases `a`, `b` and `c` are connected directly the motor terminal connector `TB_M1`

<blockquote class="warning" markdown="1"><p class="heading">BEWARE: Power limitations</p>

Version | Continuous current | Peak current | Suitable motors
--- | --- | --- | ---
v4.x (DRV8316) | 3-5A | 8A | Gimbal and small/mid-size drone motors
v1.x - v3.x | 2A | 3-5A | Gimbal motors with phase resistance R > 10Ω

The continuous current depends on cooling and on the PWM frequency. With low-resistance motors (R < 1Ω), always limit the current or voltage in the code. For more about choosing a motor, see the [BLDC motor docs](bldc_motors).
</blockquote>

## Stepper motor
The Shield can also drive a 2-phase stepper motor, such as a NEMA 17, using the `HybridStepperMotor` class of the library. A stepper has two coils with two wires each. Connect them in the order `A+`, `A-`/`B-`, `B+`: one wire of each coil to the outer terminals, and the other wire of both coils together to the middle terminal:

Motor terminal `TB_M1` | Stepper wires
--- | ---
`A` | `A+`
`B` | `A-` and `B-` together (common)
`C` | `B+`

- It doesn't matter which wire of a coil is `+` or `-`, as long as both coils share the common terminal.
- To find which wires belong to the same coil, measure with a multimeter: the two wires of a coil have a few Ω between them, wires of different coils are not connected.
- Steppers have a low phase resistance, typically 1-3Ω, so always limit the current or voltage in the code to the rated current of the motor.

As terminal B is the common, the PWM pins are given to the driver in the order A, C, B, e.g. `BLDCDriver3PWM(6, 5, 10, 8)`. See [using a stepper motor](foc_shield_code#using-a-stepper-motor) for the code.

## Power supply
- Power supply cables are connected directly to the terminal `TB_PWR`
- The maximum power supply voltage is **35V**. The recommended range is 12V to 24V.
- If you power the microcontroller through the [on-board regulator](pads_soldering), the supply has to be at least about 10V.

## Encoder
- Channels `A` and `B` are connected to the encoder connector `P_ENC`, terminals `A` and `B`. 
- If your encoder has `index` signal you can connect it to the encoder connector as well, terminal `I`.
- The `P_ENC` connector also provides `VDD` and `GND` to power the encoder. `VDD` is the [logic voltage](pads_soldering) of the board, 3.3V or 5V.

<img src="extras/Images/foc_shield_v13_enc.png" class="">

## Magnetic sensor SPI
- Magnetic sensor's SPI interface signals `SCK`, `MISO` and `MOSI` are connected to the Arduino's `SPI` pins (Arduino UNO `13`,`12` and `11`). 
  - If the application requires more than one sensor all of them are connected to the same pins of the Arduino.
- The `chip select` pin is connected to the desired pin. Each sensor connected to the same Arduino has to have unique chip select pin.

<img src="extras/Images/foc_shield_v13_magSPI.png" class="">

## SPI connector

<blockquote class="info">📢 New in v4</blockquote>
<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/v4.1/images/motor_uno.jpg" style="height:300px">

The v4 has a 6-pin JST SH (1mm pitch) connector for SPI sensors, such as the AS5047, AS5048A or MT6835:

Pin | 1 | 2 | 3 | 4 | 5 | 6
--- | --- | --- | --- | --- | --- | ---
Signal | `GND` | `VDD` | `11` (MOSI) | `12` (MISO) | `13` (SCK) | `4` (CS)

The chip select is fixed to pin `4`, so when using this connector don't use pins `4`, `11`, `12` and `13` for anything else (encoder I, PWM A, encoder A). As both stacked boards would share these pins, only one board can use its SPI connector.

```cpp
MagneticSensorSPI sensor = MagneticSensorSPI(AS5047_SPI, 4);
```

## QWIIC/STEMMA connector

<blockquote class="info"> 📢 New in v4</blockquote>

<img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/master/images/qwiic.jpg" style="height:300px">

The v4 has a 4-pin JST SH QWIIC/STEMMA QT connector for I2C sensors and other I2C modules:

Pin | 1 | 2 | 3 | 4
--- | --- | --- | --- | ---
Signal | `GND` | `VDD` | `SDA` | `SCL`

The connector is wired to the `SDA` and `SCL` pins of the Arduino header. The [I2C pull-ups](pads_soldering) can be enabled with the solder pads.

<blockquote class="warning" markdown="1"><p class="heading">QWIIC and 5V</p>
QWIIC/STEMMA QT modules are usually made for 3.3V. The connector's power pin is the board's `VDD`, so if VDD is set to 5V (e.g. for Arduino UNO), check that your module accepts 5V.
</blockquote>

## Magnetic sensor I2C
- Magnetic sensor's I2C interface signals `SCL` and `SDA` are connected to the Arduino's `I2C` pins (Arduino UNO `A4` and `A5`). 
  - If the application requires more than one sensor all of them are connected to the same pins of the Arduino.
- It is possible that you will need to use additional pull-up resistors for the `SDA` and `SCL` lines.

<img src="extras/Images/foc_shield_v13_magI2C.png" class="">


## Magnetic sensor Analog output
- Magnetic sensor's Analog output is connected directly to any analog input pin, on a figure below we use `A0`
  - If the application requires more than one sensor each of them is connected to one of the analog input pins.

<img src="extras/Images/foc_shield_v13_analog.png" class="">


## Hall sensors
- Channels `A`, `B` and `C` ( `U`, `V` and `W` ) are connected to the encoder connector `P_ENC`. 

<img src="extras/Images/foc_shield_v13_hall.png" class="">

