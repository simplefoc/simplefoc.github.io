---
layout: default
title: BLDCDriver 3PWM
nav_order: 1
permalink: /bldcdriver3pwm
parent: BLDCDriver
grand_parent: Driver code
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---


# BLDC driver 3 PWM - `BLDCDriver3PWM`

This is the class which provides an abstraction layer of most of the common 3PWM bldc drivers out there. Basically any BLDC driver board that can be run using 3PWM signals can be represented with this class.
Examples:
- Arduino <span class="simple">Simple<span class="foc">FOC</span>Shield</span>
- Arduino <span class="simple">Simple<span class="foc">FOC</span> <span class="power">Power</span>Shield</span>
- L6234 breakout board
- HMBGC v2.2
- DRV830x ( can be run in 3 PWM or 6 PWM mode )
- X-NUCLEO-IHM07M1
- etc.

<a href="javascript:show('bldc','motor');" class="btn btn-bldc btn-motor btn-primary">BLDCMotor</a> 
<a href="javascript:show('stepper','motor');" class="btn btn-stepper btn-motor">HybridStepperMotor</a> 



<div class="motor motor-bldc"  markdown="1">
<img src="extras/Images/3pwm_driver.png" class="width40">
</div>

<div class="motor motor-stepper hide"  markdown="1">
<img src="extras/Images/hybrid_3pwm.jpg" class="width40">

<blockquote class="warning" markdown="1">
⚠️ **Note:** When using the 3PWM BLDC driver with a stepper motor, ensure that the common phase `Uo` is connected to the driver's C phase pin.
</blockquote>

</div>





## Step 1. Hardware setup
To create the interface to the BLDC driver you need to specify the 3 `pwm` pin numbers for each motor phase and optionally `enable` pin.
```cpp
//  BLDCDriver3PWM( int phA, int phB, int phC, int en)
//  - phA, phB, phC - A,B,C phase pwm pins
//  - enable pin    - (optional input)
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 10, 11, 8);
```

Additionally this bldc driver class enables the user to provide enable signal for each phase if available. <span class="simple">Simple<span class="foc">FOC</span>library</span> will then handle enable/disable calls for each of the enable pins and if using modulation type `Trapezoidal_120` or `Trapezoidal_150` using these pins the library will be able to set high impedance to motor phases, which is very suitable for Back-EMF control for example:
```cpp
//  BLDCDriver3PWM( int phA, int phB, int phC, int enA, int enB, int enC )
//  - phA, phB, phC - A,B,C phase pwm pins
//  - enA, enB, enC - enable pin for each phase (optional)
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 10, 11, 8, 7, 6);
```

<blockquote class="info"> 📢 Here is a quick guide to choosing appropriate PWM pins for different MCU architectures <a href="choosing_pwm_pins">see in docs</a>.</blockquote>

<blockquote class="warning" markdown="1">
<p class="heading">⚠️ <b>Note:</b> When using the 3PWM BLDC driver with a stepper motor, ensure that the common phase `Uo` is connected to the driver's C phase pin.</p>

Even if the common phase `Uo` is physically connected to some other driver output (`A` or `B`), please provide it as the `C` phase pin in the driver constructor. This is important for the correct operation of the stepper motor. 

Consider an example of the driver connected to the MCU pins as follows:

```cpp
#define PIN_A 9
#define PIN_B 10
#define PIN_C 11
#define ENABLE 8
```

If the common phase `Uo` is connected to the driver pin `A`, you should still provide it as the `C` phase pin in the driver constructor:
```cpp
// common phase `Uo` connected to driver pin `A` so it is provided as the `C` phase pin
BLDCDriver3PWM driver = BLDCDriver3PWM(PIN_C, PIN_B, PIN_A, ENABLE);
```

If the common phase `Uo` is connected to the driver pin `B`, you should provide it as the `C` phase pin in the driver constructor:
```cpp
// common phase `Uo` connected to driver pin `B` so it is provided as the `C` phase pin
BLDCDriver3PWM driver = BLDCDriver3PWM(PIN_A, PIN_C, PIN_B, ENABLE);
```

Or if the common phase `Uo` is connected to the driver pin `C`, you should provide it as the `C` phase pin in the driver constructor:
```cpp
// common phase `Uo` connected to driver pin `C` so it is provided as the `C` phase pin
BLDCDriver3PWM driver = BLDCDriver3PWM(PIN_A, PIN_B, PIN_C, ENABLE);
``` 
</blockquote>

### Low-side current sensing considerations

As ADC conversion has to be synchronised with the PWM generated on ALL the phases, it is important that all the PWM generated for all the phases have aligned PWM. Since the microcontrollers usually have more than one timer for PWM generation on its pins, different architectures of microcontrollers have different degrees of alinement in between the PWM generated from different timers.


<blockquote class="info">
<p class="heading">RULE OF THUMB: PWM timer pins</p>
In order to maximise your chances for the low-side current sensing to work well we suggest to make sure that the PWM pins chosen for your driver all belong to the same Timer.

Finding out which pins belong to different timers might require some time to be spent in the MCU datasheet 😄
You can also always ask the community for help - <a href="https://community.simplefoc.com/">community link</a>!
</blockquote>

## Step 2.1 PWM Configuration
```cpp
// pwm frequency to be used [Hz]
// for atmega328 either 4k or 32kHz
// esp32/stm32/teensy configurable
driver.pwm_frequency = 20000;
```

Here is a list of different microcontrollers and their PWM frequency and resolution used with the  Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>.

MCU | default frequency | MAX frequency | PWM resolution | Center-aligned | Configurable freq
--- | --- | --- | --- | ---
Arduino UNO(Atmega328) | 32 kHz | 32 kHz | 8bit | yes | yes (either 4kHz or 32kHz)
STM32 | 25kHz | 50kHz | 14bit | yes | yes
ESP32 | 30kHz | 50kHz | 10bit | yes | yes
Teensy | 25kHz | 50kHz | 8bit | yes | yes

All of these settings are defined in the `drivers/hardware_specific/x_mcu.cpp/h` of the library source. 

### Low-side current sensing considerations
 
As the ADC conversion takes some time to finish and as this conversion has to happen only during the specific time window ( when all the phases are grounded  - low-side mosfets are ON ) it is important to use an appropriate PWM frequency. PWM frequency will determine how long each period of the PWM is and in term how much time the low-side switches are ON. Higher PWM frequency will leave less time for the ADC to read the current values. 

On the other hand, having higher PWM frequency will produce smoother operation, so there is definitely a tradeoff here.

<blockquote class="info">
<p class="heading">RULE OF THUMB: PWM frequency</p>
The rule of thumb is to stay arround 20kHz.

<code class="highlighter-rouge">
driver.pwm_frequency = 20000;
</code>
</blockquote>



## Step 2.2 Voltages
Driver class is the one that handles setting the pwm duty cycles to the driver output pins and it is needs to know the DC power supply voltage it is plugged to.
Additionally driver class enables the user to set the absolute DC voltage limit the driver will be set to the output pins.  
```cpp
// power supply voltage [V]
driver.voltage_power_supply = 12;
// Max DC voltage allowed - default voltage_power_supply
driver.voltage_limit = 12;
```

<a href="javascript:show('bldc','motor');" class="btn btn-bldc btn-motor btn-primary">BLDCMotor</a> 
<a href="javascript:show('stepper','motor');" class="btn btn-stepper btn-motor">HybridStepperMotor</a> 

<div class="motor motor-bldc"  markdown="1">

<img src="extras/Images/limits.png" class="width60">
</div>
<div class="motor motor-stepper hide"  markdown="1">
<img src="extras/Images/hybrid_limits.jpg" class="width60">
</div>

This parameter is used by the `BLDCMotor` class as well. As shown on the figure above the once the voltage limit `driver.voltage_limit` is set, it will be communicated to the FOC algorithm in `BLDCMotor` class and the phase voltages will be centered around the `driver.voltage_limit/2`.

Therefore this parameter is very important if there is concern of too high currents generated by the motor. In those cases this parameter can be used as a security feature. 

## Step 2.3 Initialisation
Once when all the necessary configuration parameters are set the driver function `init()` is called. This function uses the configuration parameters and configures all the necessary hardware and software for driver code execution.
```cpp
// driver init
driver.init();
```

This function is responsible for:
- determining and configuring the hardware timer for PWM generation
- verifying that all provided pins can be used to generate PWM
- configuring the PWM channels

If for some reason the driver configuration fails this function will return `0` if everything went well the function will return `1`. So we suggest you to check if the init function was executed successfully before continuing
```cpp
Serial.print("Driver init ");
// init driver
if (driver.init())  Serial.println("success!");
else{
  Serial.println("failed!");
  return;
}
```

### Enable debugging output
If you wish to see a more verbose debugging output of the driver configuration during the `driver.init()` and see more details about the driver configuration and possible errors, you can use the `SimpleFOCDebug` class. 
In order to enable the verbose debugging mode make sure to enable debugging before the `driver.init()` call, preferably at the top of the `setup()` function.
```cpp
Serial.begin(115200); // to output the debug information to the serial
SimpleFOCDebug::enable(&Serial);
```
See more in the [SimpleFOCDebug documentation](debugging).

<blockquote class="info"> 
📢 We strongly advise to use the debugging mode when starting with the <span class="simple">Simple<span class="foc">FOC</span>library</span>. 
It provides much more information than the standard monitoring output and can help troubleshooting potentially problems, even MCU architecture specific ones.
</blockquote>

## Step 3. Using `BLDCDriver3PWM` in real-time

BLDC driver class was developed to be used with the <span class="simple">Simple<span class="foc">FOC</span>library</span> and to provide the abstraction layer for FOC algorithm implemented in the `BLDCMotor` class. But the `BLDCDriver3PWM` class can used as a standalone class as well and once can choose to implement any other type of control algorithm using the bldc driver.  

## FOC algorithm support
In the context of the FOC control all the driver usage is done internally by the motion control algorithm and all that is needed to enable is is just link the driver to the `BLDCMotor` class.
```cpp
// linking the driver to the motor
motor.linkDriver(&driver)
```

## Standalone driver 
If you wish to use the bldc driver as a standalone device and implement your-own logic around it this can be easily done. Here is an example code of a very simple standalone application.
```cpp
// BLDC driver standalone example
#include <SimpleFOC.h>

// BLDC driver instance
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

void setup() {
  
  // pwm frequency to be used [Hz]
  driver.pwm_frequency = 20000;
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = 12;

  // driver init
  driver.init();

  // enable driver
  driver.enable();

  _delay(1000);
}

void loop() {
    // setting pwm
    // phase A: 3V, phase B: 6V, phase C: 5V
    driver.setPwm(3,6,5);
}
```

An example code of the BLDC driver with three enable pins, one for each phase. This code will put one phase at the time to the high-impedance mode and put 3 and 6 Volts on the remaining two. 
```cpp
// BLDC driver standalone example
#include <SimpleFOC.h>

// BLDC driver instance
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 10, 11, 8, 7, 6);

void setup() {
  
  // pwm frequency to be used [Hz]
  driver.pwm_frequency = 20000;
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = 12;

  // driver init
  driver.init();

  // enable driver
  driver.enable();

  _delay(1000);
}

void loop() {
    // phase (A: 3V, B: 6V, C: high impedance )  
    // set the phase C in high impedance mode - disabled or open
    driver.setPhaseState(_ACTIVE , _ACTIVE , _HIGH_Z); // _HIGH_Z or _HIGH_IMPEDANCE
    driver.setPwm(3, 6, 0); 
    _delay(1000);

    // phase (A: 3V, B: high impedance, C: 6V )  
    // set the phase B in high impedance mode - disabled or open
    driver.setPhaseState(_ACTIVE , _HIGH_IMPEDANCE, _ACTIVE);
    driver.setPwm(3, 0, 6);
    _delay(1000);

    // phase (A: high impedance, B: 3V, C: 6V )  
    // set the phase A in high impedance mode - disabled or open
    driver.setPhaseState(_HIGH_IMPEDANCE, _ACTIVE, _ACTIVE);
    driver.setPwm(0, 3, 6);
    _delay(1000);
}
```