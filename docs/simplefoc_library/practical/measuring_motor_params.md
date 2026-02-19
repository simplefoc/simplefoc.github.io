---
layout: default
title: Automatic Motor Characterisation
nav_order: 6
description: "How to use characteriseMotor() to automatically measure motor parameters in SimpleFOC"
permalink: /motor_characterisation
parent: Practical guides
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# Automatic Motor Characterisation with characteriseMotor()

The `characteriseMotor()` function in <span class="simple">Simple<span class="foc">FOC</span>library</span> provides an automated way to measure critical motor parameters: **phase resistance** and **phase inductance** (both D-axis and Q-axis). This guide explains how to use it effectively.

## What Parameters Does characteriseMotor() Measure?

### Phase Resistance
- The DC resistance of motor windings (in Ohms)
- Measured by: Applying small amplitude high-frequency voltage and measuring current response
- Essential for: Current control, torque estimation, and FOC algorithms

### Phase Inductance (D and Q axis)
- The inductive reactance of motor windings (in Henry)
- **D-axis inductance** ($$L_d$$): Inductance along the direct axis (aligned with magnets)
- **Q-axis inductance** ($$L_q$$): Inductance along the quadrature axis (perpendicular to magnets)
- Important for: High-speed operation, lag compensation, and advanced control
- Note: D and Q axis inductances may differ in some motor types

## Requirements

To use `characteriseMotor()`, you **must** have:

1. **Current sensor set up and linked** to the motor:
   - Inline current sensing (recommended)
   - Low-side current sensing
   - High-side current sensing
   - [See current sensing guide](current_sense){: .btn .btn-docs}

2. **Driver initialized and linked** to the motor

3. **Motor initialized** (but NOT in control mode)

4. **Serial connection** for output monitoring (optional but recommended)

<blockquote class="warning">
<strong>Important:</strong> The motor must remain still during characterisation. Ensure the motor shaft cannot move freely, or hold it steady by hand.
</blockquote>

## Basic Usage

```cpp
#include <SimpleFOC.h>

// Your motor, driver, and current sensor setup here
// ...

void setup() {
  Serial.begin(115200);
  
  // Initialize driver, motor, and current sensor
  driver.init();
  motor.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);
  motor.init();
  
  // Run characterisation with 2.0V test voltage
  motor.characteriseMotor(2.0f);
  
  // Motor parameters are now automatically set
}

void loop() {
  // Your control code here
}
```

### Function Signature

```cpp
void characteriseMotor(float test_voltage);
```

**Parameters:**
- `test_voltage` (float): The voltage amplitude used during measurement, in Volts
  - Should be high enough to produce measurable current (typically 1-5V)
  - Should NOT be so high as to damage the motor
  - Start with 2-3V for most hobby motors
  - Use lower values (0.5-1V) for high-KV drone motors
  - Use higher values (3-5V) for low-KV gimbal motors

## Understanding the Output

The function prints detailed results to the serial monitor:

```sh
MOT: Init
MOT: Enable driver.
MOT: Measuring phase to phase resistance, keep motor still...
MOT: Estimated phase to phase resistance: 5.94
MOT: Measuring inductance, keep motor still...
MOT: Inductance measurement complete!
MOT: Measured D-inductance in mH: 0.50
MOT: Measured Q-inductance in mH: 0.59
```

### Interpreting the Results

| Parameter | Units | Typical Range | Notes |
|-----------|-------|---------------|-------|
| Phase resistance | Ω | 0.05 - 50 | Gimbal: 3-10Ω, Drone: 0.05-1Ω, Stepper: 1-50Ω |
| D-inductance | mH | 0.1 - 10 | Usually measured value |
| Q-inductance | mH | 0.1 - 10 | May differ from D-inductance depending on motor type |

## Accessing Measured Values

After `characteriseMotor()` completes, the measured parameters are automatically stored in the motor object:

```cpp
// After characteriseMotor() has run:

// Access phase resistance (already stored)
float R = motor.phase_resistance;

// Access inductance values (v2.4.0+)
float L_d = motor.axis_inductance.d;  // D-axis inductance
float L_q = motor.axis_inductance.q;  // Q-axis inductance

Serial.print("Resistance: ");
Serial.print(R);
Serial.println(" Ohms");

Serial.print("D-axis inductance: ");
Serial.print(L_d * 1000);  // Convert H to mH
Serial.println(" mH");

Serial.print("Q-axis inductance: ");
Serial.print(L_q * 1000);  // Convert H to mH
Serial.println(" mH");
```

<blockquote class="info">
<strong>Version Note:</strong> Starting from v2.4.0, <span class="simple">Simple<span class="foc">FOC</span>library</span> measures both D-axis and Q-axis inductance separately. Earlier versions measure only a single inductance value.
</blockquote>

## Example Code


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> Hybrid Stepper motors</a>

<div class="type type-b" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor
BLDCMotor motor = BLDCMotor(11);
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);

InlineCurrentSense current_sense = InlineCurrentSense(185.0f, A0, A2);

void setup() {
  // Serial for output
  Serial.begin(115200);
  
  // Enable debug output
  SimpleFOCDebug::enable(&Serial);
  
  // Configure driver
  driver.voltage_power_supply = 20;  // 20V power supply
  driver.init();
  motor.linkDriver(&driver);
  
  // Link and initialize current sensor
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);
  
  // Initialize motor
  motor.init();
  
  // Run characterisation with 3.5V test voltage
  motor.characteriseMotor(3.5f);
}

void loop() {
  // Your control code here
  delay(1000);
}
```
</div>

<div class="type type-s hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// Stepper motor
StepperMotor motor = StepperMotor(50);
StepperDriver2PWM driver = StepperDriver2PWM(6, 10, 5, 8);

InlineCurrentSense current_sense = InlineCurrentSense(50.0f, A0, A2);

void setup() {
  // Serial for output
  Serial.begin(115200);
  
  // Enable debug output
  SimpleFOCDebug::enable(&Serial);
  
  // Configure driver
  driver.voltage_power_supply = 20;  // 20V power supply
  driver.init();
  motor.linkDriver(&driver);
  
  // Link and initialize current sensor
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);
  
  // Initialize motor
  motor.init();
  
  // Run characterisation with 3.5V test voltage
  motor.characteriseMotor(3.5f);
}

void loop() {
  // Your control code here
  delay(1000);
}
```
</div>

<div class="type type-h  hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// Hybrid Stepper motor
HybridStepperMotor motor = HybridStepperMotor(50);
BLDCDriver3PWM driver = BLDCDriver3PWM(6, 10, 5, 8);

InlineCurrentSense current_sense = InlineCurrentSense(50.0f, A0, A2);

void setup() {
  // Serial for output
  Serial.begin(115200);
  
  // Enable debug output
  SimpleFOCDebug::enable(&Serial);
  
  // Configure driver
  driver.voltage_power_supply = 20;  // 20V power supply
  driver.init();
  motor.linkDriver(&driver);
  
  // Link and initialize current sensor
  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);
  
  // Initialize motor
  motor.init();
  
  // Run characterisation with 3.5V test voltage
  motor.characteriseMotor(3.5f);
}

void loop() {
  // Your control code here
  delay(1000);
}
```
</div>
## Tips for Successful Characterisation

### 1. Choose the Right Test Voltage
- **Too low:** Current measurement may be noisy or at the edge of sensor accuracy
- **Too high:** May stress the motor or saturate the current sensor
- **Rule of thumb:** Start with 2-3V and adjust based on measurement quality

### 2. Keep the Motor Still
- Any movement will corrupt the inductance measurement
- The motor is stationary during the entire process

### 3. Ensure Good Current Sensing
- Verify current sensor is properly calibrated
- Check that current sensor bandwidth is sufficient (typically >5 kHz recommended)
- Ensure solid electrical connections

### 4. Multiple Measurements
- If results seem inconsistent, run `characteriseMotor()` multiple times
- Results should be repeatable within ±5%
- If variation is large, check mechanical coupling and sensor stability

## Troubleshooting

### "Measurement Failed" or No Output
- Verify current sensor is working (`motor.current_sense` is not null)
- Check serial connection and baud rate (typically 115200)
- Ensure power supply is adequate for test voltage
- Verify driver and motor initialisation completed successfully

### Results Seem Wrong
- Phase resistance should typically be 0.05Ω to 50Ω
- If much higher or lower, check mechanical connection to motor
- Check the gains and shunt values of the current sensor
- D and Q inductances should be similar for isotropic motors, but may differ for salient-pole motors
- Compare with datasheet values if available

### High Variability in Measurements
- Ensure motor is completely stationary
- Check for vibration from power supply or mechanical coupling
- Verify current sensor hasn't saturated
- Try different test voltage values

---

## Related Guides

- [Measuring KV rating](kv_rating_measure){: .btn .btn-docs}
- [Manual phase resistance measurement](phase_resistance){: .btn .btn-docs}
- [Motor parameter testing](motor_params_test){: .btn .btn-docs}
- [Current sensing methods](current_sense){: .btn .btn-docs}
- [Current PI tuning](tuning_current_loop){: .btn .btn-docs}
  