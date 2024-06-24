---
layout: default
title: Soldering Pads v3.x
parent: Soldering pads
grand_parent: Getting Started
description: "Configuring your SimpleFOCShield by soldering the pads."
nav_order: 3
permalink: /pads_soldering_v3
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
grand_grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
---

<style>
.inline_table {
  margin: 1em;
  display:inline-block;
}
.tight_table th,td {
  min-width: 100px !important;  
}
</style>

# Hardware configuration using soldering pads <br> <span class="simple">Simple<span class="foc">FOC</span>Shield</span> <small>v3</small>
One of the very important features of the Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span> is the hardware configuration. 

<img src="extras/Images/v3_pads.gif" class="width40">

Each board has a set of solder pads on the bottom side which are used for configuration. These solder pads enable board to:

- Configure BLDC driver pinout (PWM pins A,B,C and enable pin) - [Read more](#customizing-pinout)
- Enable / Disable pull-up resistors for encoder A,B and Index channel - [Read more](#enabling-encoderhall-sensor-pull-up-resistors)
- Configure encoder/hall sensor connections - [Read more](#customizing-pinout)
- Enable / Disable the linear regulator to power the MCU - [Read more](#enabling-on-board-voltage-regulator-to-power-the-mcu)
- Configure digital voltage level VDD -  [Read more](#configuring-the-logic-voltage-level-vdd)
- Configure the pinout of current sensing - [Read more](#configuring-the-current-sensing-pinout)
- Configure Fault and Reset pins - [Read more](#configuring-the-fault-and-reset-pins)

## Enabling encoder/hall sensor pull-up resistors

<img src="extras/Images/v3_pads_encoder_pul.jpg" class="width30">

Each board has integrated set of three 3.3KOhm pull-up resistors for encoder channels A,B and Index (or Hall sensor U, V, W). The picture above shows how solder the pads in order to enable the Pull-up resistors.
Not all encoders need the pull-up resistors, or better said, in general, most of them don't need them.  For those of us who are looking for price optimization :slight_smile: , a lot of cheap Ebay/Aliexpress encoders will need them such as [600P ebay encoder <i class="fa fa-external-link"></i>](https://www.ebay.com/itm/360-600P-R-Photoelectric-Incremental-Rotary-Encoder-5V-24V-AB-Two-Phases-Shaft/254214673272?hash=item3b30601378:g:AZsAAOSwu~lcxosc) and similar.

<blockquote class="info" markdown="1">
<span class="simple">Simple<span class="foc">FOC</span>Shield</span> version v3.1 has only one solder pad for enabling the pull-up resistors for all three channels. Whereas <span class="simple">Simple<span class="foc">FOC</span>Shield</span> v3.2 has a triple solder pad. Either way, if you wish to use the pull-ups solder all the pads together.  
</blockquote>

## Enabling I2C pull-up resistors

<img src="extras/Images/v3_pads_i2c_pul.jpg" class="width30">

From the shield [<i class="fa fa-tag"></i>version 1.3.2](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases) the boards come with with 4.7KOhm pull-up resistors for I2C communication pins. The picture above shows how solder the pads in order to enable the Pull-up resistors.
Not all I2C devices (especially Magnetic sensors) need the pull-up resistors, or better said, in general, most of them don't need them, especially with Arduino UNO. But it is very common to have problems interfacing these sensors with STM32 boards such as Nucleo-64. There you will need to enable the pullups or provide them yourself externally.  
<blockquote class="warning"><p class="heading">BEWARE: Stacking</p>
If you are stacking the shields and you wish to use the I2C pull-ups, make sure you solder these pads on only board at a time!
</blockquote>

<blockquote class="info" markdown="1">
<span class="simple">Simple<span class="foc">FOC</span>Shield</span> version v3.1 has only one solder pad for enabling the pull-up resistors for both SDA and SCL. The v3.2 has a double solder pad.
</blockquote>

## Enabling on-board voltage regulator to power the MCU

<img src="extras/Images/v3_pads_ldo.jpg" class="width30">

The boards from  [<i class="fa fa-tag"></i>version 2.0](https://github.com/simplefoc/Arduino-SimpleFOCShield/releases) have integrated linear regulator in order to provide a way to power the MCU on which the board has been stacked on with power combing form boards terminals. Board version v3.x has a 8V regulator on-board connected to the VIN pin of the shield which will enable powering most of the boards with Arduino UNO R3 headers. Most of the manufacturers have a voltage regulator connected to the VIN pin that can accept up to 12V (so 8V is well within their limits). The picture above shows how to solder the pads in order to enable the linear regulator. 

<blockquote class="warning"><p class="heading">BEWARE: Stacking</p>
If you are stacking the shields and you wish to use the linear regulator, make sure to enable it on only one board!
</blockquote>

## Configuring the logic voltage level (VDD)

<img src="extras/Images/v3_pads_vdd.gif" class="width30">

If your microcontroller has 5V logic the chances are that its ADC operates in 5V range nad if your mcu works on 3.3V it will most probably have a 3.3V ADC range. Please check the datasheet before soldering this pad. 

<blockquote class="info"><p class="heading">RULE OF THUMB: 3.3V or 5V</p>
Arduino UNO - 5V range<br>
stm32 (nucleo, bluepill) and esp32 chips - 3.3V range
</blockquote>

## Configuring the fault and reset pins

<img src="extras/Images/v3_pads_fault_reset.jpg" class="width30">

The fault and reset pins are used to monitor the driver status and reset the driver. Both pins have inverted logic and are used in combination with an external pull-up. 
- The fault pin is pulled up to 3.3V and is pulled down to 0V when the fault occurs.
<blockquote class="info"> There is an LED on the board that will indicate the fault state. </blockquote>
- The reset pin is pulled up to 3.3V and and needs to be pulled down to 0V to trigger the reset. 
<blockquote class="info"> The reset is the only way to recover the driver from the fault state, except for the power cycle. </blockquote>

The picture above shows how to solder the pads in order to enable the fault and reset pins, once the pads are soldered you can connect the pins to the microcontroller to the pins `A1` and `A3`.

<blockquote class="warning" markdown="1">
 As the reset and fault (once soldered) reserve pins `A1` and `A3` for the microcontroller, make sure not to use these pins for anything else (ex. current sensing). 
 </blockquote>


## Configuring the current sensing pinout

<img src="extras/Images/v3_pads_cs.gif" class="width30">

The pinout of current sensing is very simple and the only real important thing is that we do not use the same pins for something else. Therefore if stacking multiple boards make sure to configure the each board in a way to use the pair of pins that the other one does not use. 

Signal | Possible pins
--- | ---
Current phase A | `A0`, `A1`
Current phase B | `A2`, `A3`

<blockquote class="info" markdown="1">
📢 If not sure which pins to use, check our [guide to choosing ADC pins](choosing_adc_pins)!
</blockquote>

## Customizing pinout

Pinout customization of the <span class="simple">Simple<span class="foc">FOC</span>Shield</span>  enables the board to be very flexible with using different sensors and additional arduino modules. But more importantly it enables the board to be stackable. 

Here is a table of the configurable signals and their possible pin assignments:

Signal | Possible pins
--- | ---
Pwm A | `6`, `11`, `13`
Pwm B | `9`, `10`
Pwm C | `3`, `5`
Enable | `7`, `8`
Encoder A | `3`, `12`, `A5`
Encoder B | `2`, `A4` 
Encoder I | `4`, `11`, `13`

Now, there is a lot of possible pin configurations and not all of them are possible depending on the microcontroller and sensor you are using. 
For example, Arduino UNO only has 2 external interrupt pins, and they are pin  `2` and `3`. Therefore when using the board with Arduino UNO and Encoder we will try to use pin `3` for encoder channel A and not for pwm A. 

Another example is when stacking two boards with the STM32 Nucleo (variants `Stm32F4x`). Nucleo (`Stm32F4x`) board cannot have pwm generation on pins `11` and `9` at the same time, therefore you cannot combine those pins. Therefore when using Nucleo (`Stm32F4x`) board, the rule of thumb is to avoid using pin `11` but use pin `13` instead.

<blockquote class="info" markdown="1">
📢If not sure which pins to use, check our [guide to choosing PWM pins](choosing_pwm_pins)!
</blockquote>

The following text suggests some pinout configurations based on the stacking necessity and the microcontroller used.

### Suggested pinout: Single board
When using only one board with only one motor, it is much easier to choose the pinout. Basically you only need to be careful if you are using the encoder to use pin `3` with encoder channel A and not withe the driver's pin pwm A. And as well, if you are using the SPI magnetic sensor you should avoid using pins `10` and `11` because they are used with the SPI communication.  

<img src="extras/Images/v3_pads_default_single.jpg" class="width30">

With all of this in mind, probably the best pinout you can have when using one board will be:

<div class="tight_table" markdown="1">

Signal | Pwm A | Pwm B | Pwm C | Enable | Encoder A | Encoder B | Encoder I | Current A | Current B
--- | - | - | - | - | - | - | - | - | - 
Pin | `6` | `10` | `5` | `8` | `3` | `2`| `4`|`A0` | `A2` 

</div>

On the image above you can see which soldering pads you would need to solder in order to obtain this configuration.
```cpp
// driver instance configuration based on pinout above
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);
...
// Encoder sensor
Encoder sensor = Encoder(2, 3, ... );
...
// Current sensing 
InlineCurrentSense current_sense = InlineCurrentSense(0.185f, A0, A2, ...);

```

### Suggested pinout: Stacking with Arduino UNO

Arduino UNO has only 6 pwm pins which means when we stack two boards we do not have much choice which ones to use, we need all of them. It is nto important how we organize the pwm A,B,C,enable and encoder A,B,I signals as log as we use pin `3` for pwm A and as long as we don't use pin `13` for pwm C.

<img src="extras/Images/v3_pads_double_uno1.jpg" class="width30">
<img src="extras/Images/v3_pads_double_uno2.jpg" class="width30">

Here is one example of a pin assignment compatible with Arduino UNO:

<div class="tight_table" markdown="1">

Signal | Pwm A | Pwm B | Pwm C | Enable | Encoder A | Encoder B | Encoder I | Current A | Current B
--- | - | - | - | - | - | - | - | - | - 
Board #1 | `6` | `10` | `5` | `8` | `12` | `2`| `4` |`A0` | `A2` 
Board #2 | `11` | `9` | `3` | `7` | `A5` | `A4` | `13` |`A1` | `A3` 

</div>

The figure above shows how to solder the pads on both boards to obtain the desired pinout.  
```cpp
// motor instances configuration based on pinout above
BLDCDriver3PWM driver1 = BLDCDriver3PWM(6, 10, 5, 8);
BLDCDriver3PWM driver2 = BLDCDriver3PWM(11, 9, 3, 7);
...
// encoder
Encoder sensor1 = Encoder(2, 12, ... );
Encoder sensor2 = Encoder(A4, A5, ... );
...
// current sensing
InlineCurrentSense current_sense1 = InlineCurrentSense(0.185f, A0, A2, ...);
InlineCurrentSense current_sense2 = InlineCurrentSense(0.185f, A1, A3, ...);
```

### Suggested pinout: Stacking with Stm32 Nucleo

When using stacked <span class="simple">Simple<span class="foc">FOC</span>Shield</span> with stm32 Nucleo board, we just have to make sure not to use pin `11` for pwm C, but to use pin `13` instead. And as for Arduino UNO, we should not use pin `3` for encoder A, but for pwm A. But if we respect these constraints we can choose the other pins as we wish.

<img src="extras/Images/v3_pads_double_nucleo1.jpg" class="width30">
<img src="extras/Images/v3_pads_double_nucleo2.jpg" class="width30">

Here is an example of chosen pinout configuration valid for stacking with Nucleo board.

<div class="tight_table" markdown="1">

Signal | Pwm A | Pwm B | Pwm C | Enable | Encoder A | Encoder B | Encoder I | Current A | Current B
--- | - | - | - | - | - | - | - | - | - 
Board #1 | `6` | `10` | `5` | `8` | `12` | `2`| `4` |`A0` | `A2` 
Board #2 | `13` | `9` | `3` | `7` | `A5` | `A4` | `11` |`A1` | `A3` 

</div>

See the figure above to find how to solder the pads to obtain this configuration.

```cpp
// motor instances configuration based on pinout above
BLDCDriver3PWM driver1 = BLDCDriver3PWM(6, 10, 5, 8);
BLDCDriver3PWM driver2 = BLDCDriver3PWM(13, 9, 3, 7);
...
// encoder
Encoder sensor1 = Encoder(2, 12, ... );
Encoder sensor2 = Encoder(A4, A5, ... );
...
// current sensing
InlineCurrentSense current_sense1 = InlineCurrentSense(0.185f, A0, A2, ...);
InlineCurrentSense current_sense2 = InlineCurrentSense(0.185f, A1, A3, ...);
```
