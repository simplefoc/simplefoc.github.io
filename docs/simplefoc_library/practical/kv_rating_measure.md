---
layout: default
title: Measuring Motor KV Rating
nav_order: 10
description: "How to measure the KV rating (rpm/V) of your motor using SimpleFOC."
permalink: /kv_rating_measure
parent: Practical guides
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# How to Measure Motor KV Rating with SimpleFOC

The **KV rating** of a motor (in rpm/V) tells you how fast the motor spins per volt applied, under no load. Knowing your motor's KV is important for accurate torque control and current estimation in FOC applications.

## What is KV Rating?
- **KV** ("velocity constant") is the number of revolutions per minute (rpm) the motor will turn per volt applied.
- Example: A 100 KV motor spins at 100 rpm for every 1V applied (no load).

## Why Measure KV Yourself?
- Datasheet values are often inaccurate or missing.
- Real-world values can differ due to winding, magnets, or assembly.
- Accurate KV is needed for advanced torque/current control modes in SimpleFOC.

## How to Measure KV with SimpleFOC

1. **Wire up your motor and sensor** (encoder, magnetic, or Hall sensor) and driver as usual.
2. **Set up SimpleFOC in voltage torque mode**:
   - Set the control mode to torque: `motor.controller = MotionControlType::torque;`
   - Set the torque control mode to voltage `motor.torque_controller = TorqueControlType::voltage;`
   - Set a target voltage (typically 1V, but you can try other values for confirmation).
3. **Run the motor and read the velocity**:
   - Let the motor spin up to steady speed.
   - Read the measured velocity (in rad/s) from the serial monitor or via code.
4. **Convert velocity to KV**:
   - Use the formula:  
     $$\text{KV} = \text{velocity (rad/s)} \times \frac{30}{\pi}$$
   - Example: If you measure 20 rad/s at 1V, then KV = 20 × 30/π ≈ 191 rpm/V.

> **Tip:** The KV value should not change much with voltage. Try 1V, 2V, etc. and average the results.

## Example: Serial Command to Calculate KV
SimpleFOC provides example sketches that let you set the voltage and calculate KV directly from the serial terminal. See `examples/utils/calibration/find_kv_rating/` in the Arduino-FOC repo.

---

## Example Code for BLDC, Stepper, and Hybrid Stepper

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> Hybrid Stepper motors</a>

Below are minimal examples for each motor type. Replace the sensor and driver with your hardware as needed.

<div class="type type-b"  markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor & driver instance
BLDCMotor motor = BLDCMotor(11); // 11 pole pairs example
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);
// Sensor instance (e.g., encoder)
Encoder sensor = Encoder(2, 3, 500);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }

float target_voltage = 1; // 1V for KV measurement
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&target_voltage, cmd); }
void calcKV(char* cmd) {
  // Print KV: velocity (rad/s) / voltage (V) * 30/pi
  Serial.print("KV = ");
  Serial.println(motor.shaft_velocity / target_voltage * 30.0f / PI);
}

void setup() {
  Serial.begin(115200);
  sensor.init();
  sensor.enableInterrupts(doA, doB);
  motor.linkSensor(&sensor);
  driver.voltage_power_supply = 12;
  driver.init();
  motor.linkDriver(&driver);
  motor.controller = MotionControlType::torque;
  motor.init();
  motor.initFOC();
  command.add('T', doTarget, "target voltage");
  command.add('K', calcKV, "calculate KV rating");
  Serial.println(F("Set voltage: T [value], Calculate KV: K"));
}
void loop() {
  motor.loopFOC();
  motor.move(target_voltage);
  command.run();
}
```

</div>
<div class="type type-s hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// Stepper motor & driver instance
StepperMotor motor = StepperMotor(50); // 50 pole pairs example
StepperDriver4PWM driver = StepperDriver4PWM(9, 5, 10, 6, 8);
// Sensor instance (e.g., encoder)
Encoder sensor = Encoder(2, 3, 8192);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }

float target_voltage = 1;
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&target_voltage, cmd); }
void calcKV(char* cmd) {
  Serial.print("KV = ");
  Serial.println(motor.shaft_velocity / target_voltage * 30.0f / PI);
}

void setup() {
  Serial.begin(115200);
  sensor.init();
  sensor.enableInterrupts(doA, doB);
  motor.linkSensor(&sensor);
  driver.voltage_power_supply = 12;
  driver.init();
  motor.linkDriver(&driver);
  motor.controller = MotionControlType::torque;
  motor.init();
  motor.initFOC();
  command.add('T', doTarget, "target voltage");
  command.add('K', calcKV, "calculate KV rating");
  Serial.println(F("Set voltage: T [value], Calculate KV: K"));
}
void loop() {
  motor.loopFOC();
  motor.move(target_voltage);
  command.run();
}
```

</div>
<div class="type type-h hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// Hybrid Stepper motor & driver instance
HybridStepperMotor motor = HybridStepperMotor(50); // 50 pole pairs example
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);
// Sensor instance (e.g., encoder)
Encoder sensor = Encoder(2, 3, 8192);
void doA() { sensor.handleA(); }
void doB() { sensor.handleB(); }

float target_voltage = 1;
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&target_voltage, cmd); }
void calcKV(char* cmd) {
  Serial.print("KV = ");
  Serial.println(motor.shaft_velocity / target_voltage * 30.0f / PI);
}

void setup() {
  Serial.begin(115200);
  sensor.init();
  sensor.enableInterrupts(doA, doB);
  motor.linkSensor(&sensor);
  driver.voltage_power_supply = 12;
  driver.init();
  motor.linkDriver(&driver);
  motor.controller = MotionControlType::torque;
  motor.init();
  motor.initFOC();
  command.add('T', doTarget, "target voltage");
  command.add('K', calcKV, "calculate KV rating");
  Serial.println(F("Set voltage: T [value], Calculate KV: K"));
}
void loop() {
  motor.loopFOC();
  motor.move(target_voltage);
  command.run();
}
```
</div>

---

## Practical Notes
- The measured KV should be stable across voltages (try 1V, 2V, 3V).
- If the value changes a lot, check your wiring, sensor, and supply voltage.
- For best results, run the test with no load on the motor.

For more details, see the [SimpleFOC documentation](https://docs.simplefoc.com/) and the example sketches in the Arduino-FOC library.

