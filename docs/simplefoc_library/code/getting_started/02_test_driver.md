---
layout: default
title: Test Your Driver
nav_order: 2
description: "Verify your motor driver is working correctly"
permalink: /test_driver
parent: Getting Started Guide
grand_parent: Writing the Code
great_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---

# Test Your Driver

⏱️ **~10 minutes**

Once your sensor is working, test that your motor driver can initialize and output PWM signals correctly.

## Standalone Driver Test

First, test the driver without a motor connected (safest approach).

### Select Your Driver Type

<a href="javascript:show('3pwm','driver');" class="btn btn-driver btn-3pwm btn-primary">3PWM BLDC Driver</a> 
<a href ="javascript:show('6pwm','driver');"  class="btn btn-driver btn-6pwm">6PWM BLDC Driver</a> 
<a href ="javascript:show('2pwm','driver');"  class="btn btn-driver btn-2pwm">2PWM Stepper Driver</a>
<a href ="javascript:show('4pwm','driver');"  class="btn btn-driver btn-4pwm">4PWM Stepper Driver</a>

<div class="driver driver-3pwm"  markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDCDriver3PWM(pwmA, pwmB, pwmC, (en optional))
BLDCDriver3PWM driver = BLDCDriver3PWM(TODO,TODO,TODO, TODO); // set your pins

void setup() {
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // power supply voltage [V]
  driver.voltage_power_supply = TODO;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = TODO;

  // driver init
  if (!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }

  // enable driver
  driver.enable();
  Serial.println("Driver ready!");
  _delay(1000);
}

void loop() {
    // setting pwm
    // phase A: 3V
    // phase B: 6V
    // phase C: 5V
    driver.setPwm(3,6,5);
}
```

</div>

<div class="driver driver-6pwm hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDCDriver6PWM(pwmAh, pwmAl, pwmBh, pwmBl, pwmCh, pwmCl, (en optional))
BLDCDriver6PWM driver = BLDCDriver6PWM(TODO, TODO, TODO, TODO, TODO, TODO, TODO); // set your pins

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // power supply voltage [V]
  driver.voltage_power_supply = TODO;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = TODO;
  // dead_zone [0,1] - default 0.02f - 2%
  driver.dead_zone = 0.05f;

  // driver init
  if (!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }

  // enable driver
  driver.enable();
  Serial.println("Driver ready!");
  _delay(1000);
}

void loop() {
    // setting pwm
    // phase A: 3V
    // phase B: 6V
    // phase C: 5V
    driver.setPwm(3,6,5);
}
```

</div>

<div class="driver driver-2pwm hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// StepperDriver2PWM(pwm1, dir1, pwm2, dir2,(en1, en2 optional))
StepperDriver2PWM driver = StepperDriver2PWM(TODO, TODO, TODO, TODO, TODO, TODO); // set your pins

void setup() {
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // power supply voltage [V]
  driver.voltage_power_supply = TODO;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = TODO;
  
  // driver init
  if (!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }

  // enable driver
  driver.enable();
  Serial.println("Driver ready!");
  _delay(1000);
}

void loop() {
    // setting pwm
    // phase A: 3V
    // phase B: 6V
    driver.setPwm(3,6);
}
```

</div>

<div class="driver driver-4pwm hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// StepperDriver4PWM(ph1A, ph1B, ph2A, ph2B, (en1, en2 optional))
StepperDriver4PWM driver = StepperDriver4PWM(TODO, TODO, TODO, TODO, TODO, TODO); // set your pins

void setup() {
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // power supply voltage [V]
  driver.voltage_power_supply = TODO;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = TODO;
  
  // driver init
  if (!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }

  // enable driver
  driver.enable();
  Serial.println("Driver ready!");
  _delay(1000);
}

void loop() {
    // setting pwm
    // phase A: 3V
    // phase B: 6V
    driver.setPwm(3,6);
}
```

</div>

## Setup Steps

1. **Copy the code** for your driver type
2. **Update pin numbers** to match your setup:
   - implement TODOs in the code
   - PWM pins for each phase
   - Enable pin (optional but recommended)
3. **Set power supply voltage** to match your hardware
4. **Upload** and open Serial Monitor (115200 baud)

## Verify Driver Initialization

<blockquote class="info" markdown="1"> <p class="heading">✓ What to expect</p> 
You should see:
- `Driver ready!` message in the serial terminal
- PWM signals on all phase pins (even if no motor connected)
- No `Driver init failed!` error
</blockquote>

### Check PWM Outputs

You can verify without a multimeter or by connecting small LEDs between each phase and ground. They should light up with different brightness levels.

If you connect LEDs you can do a quick test by changing the PWM values in the `loop()` function and observing the brightness change.
```cpp
float angle = 0;
float set_voltage = 3; // Start at 3V
void loop() {
    // setting pwm
    angle = fmod(angle + 0.01, TWO_PI); // Rotate angle
    float pwmA = set_voltage*(sin(angle) + 1) / 2 ;              // Sinusoidal pattern for testing          
    float pwmB = set_voltage*(sin(angle + TWO_PI/3) + 1) / 2 ;   // Phase shifted by 120 degrees
    float pwmC = set_voltage*(sin(angle + 2*TWO_PI/3) + 1) / 2 ; // Phase shifted by 240 degrees
    driver.setPwm(pwmA, pwmB, pwmC);
    delay(100);
}
```

Alternatively you could even connect your motor and observe it rotating, just make sure to set the voltage `set_voltage` to a very low value (e.g., 1V) to prevent damage.

## Troubleshooting

**Driver init failed?**
- Verify pin numbers match your hardware
- Check power supply is connected and powered on
- Ensure pins you're using support PWM (check your MCU datasheet)
- See [Choosing PWM Pins guide](choosing_pwm_pins)

**Wrong driver class selected?**
- Check your driver datasheet for number of PWM pins
- Refer to [Motor Drivers documentation](drivers_config)
- Check [SimpleFOC examples](https://github.com/simplefoc/Arduino-FOC/tree/master/examples/hardware_specific_examples) for your specific hardware

**Dead zone parameter?**
- Only needed for 6PWM drivers
- Prevents overlapping phase switching; default is usually fine
- Increase if you hear buzzing

## Next Step

Once driver initializes successfully, proceed to 

[Motor + Driver Test](test_motor_driver){: .btn .btn-step}
