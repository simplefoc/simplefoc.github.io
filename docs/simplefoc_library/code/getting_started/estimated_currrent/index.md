---
layout: default
title: Estimated Current Control
nav_order: 7
description: "Model-based current control without sensors"
permalink: /estimated_current_guide
parent: Getting Started Guide
grand_parent: Writing the Code
great_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---

# Estimated Current Control Setup

⏱️ **~30-60 minutes**

This guide shows how to use **estimated current control** (model-based current) when you don’t have current sensors. It lets you command torque in Amps by estimating current from motor parameters.

[Read more about when to use estimated current control](estimated_current_mode){: .btn .btn-docs}

## Prerequisites

Before starting, make sure you already have:
- Working **sensor** and **driver** setup - [Sensor setup guide](test_sensor) and [Driver setup guide](test_driver)
- Closed-loop FOC working in **voltage torque mode** - [Closed-loop voltage control guide](test_closedloop)


### Motor parameters

Estimated current control relies on accurate motor parameters to estimate current. In order of importance, you should know or measure:

1. **Phase resistance** (**Required**) - [How to measure](phase_resistance)
2. **KV rating** (**Optional but Recommended**) - [How to measure](kv_rating_measure)
3. **Inductance** (**Optional and not critical for basic use, but improves high-speed performance**) 


Phase resistance and KV rating are the most important parameters for basic estimated current control. They are often specified in the datasheet or can be measured with simple tests. Inductance is less critical for basic use but can improve performance at higher speeds by compensating for back-EMF. If not available, it can be omited for basic use, but consider adding it later for better performance.

[Read more about estimated current control parameters](estimated_current_mode){: .btn .btn-docs}

<blockquote class="info" markdown="1"> **If you have current sensing already set up**, you can use it to measure the motor parameters directly - [See here for automatic motor characterisation guide](motor_characterisation){: .btn .btn-docs-middle}

</blockquote>

## Step 1: Set motor parameters

The motor parameters are set as properties of the motor object and can be provided either in the constructor 


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> Hybrid Stepper motors</a>

<div class="type type-b"  markdown="1">
```cpp
// In constructor
// BLDCMotor motor = BLDCMotor(pole_pairs, phase_resistance [Ohm], KV_rating[rpm/V], Lq[H], Ld[H]);
BLDCMotor motor = BLDCMotor(11, 0.4f);// only R  
// or
BLDCMotor motor = BLDCMotor(11, 0.4f, 120.0f); // R and KV
// or
BLDCMotor motor = BLDCMotor(11, 0.4f, 120.0f, 0.001f); // R, KV, and inductance
```
</div>

<div class="type type-s hide"  markdown="1">
```cpp
// In constructor
// StepperMotor motor = StepperMotor(pole_pairs, phase_resistance [Ohm], KV_rating[rpm/V], Lq[H], Ld[H]);
StepperMotor motor = StepperMotor(50, 1.5f); // only R 
// or
StepperMotor motor = StepperMotor(50, 1.5f, 120.0f); // R and KV
// or
StepperMotor motor = StepperMotor(50, 1.5f, 120.0f, 0.001f); // R, KV, and inductance
```
</div>

<div class="type type-h hide"  markdown="1">
```cpp
// In constructor
// HybridStepperMotor motor = HybridStepperMotor(pole_pairs, phase_resistance [Ohm], KV_rating[rpm/V], Lq[H], Ld[H]);
HybridStepperMotor motor = HybridStepperMotor(50, 1.5f);// only R 
// or
HybridStepperMotor motor = HybridStepperMotor(50, 1.5f, 40.0f); // R and KV
// or
HybridStepperMotor motor = HybridStepperMotor(50, 1.5f, 40.0f, 0.001f); 
```
</div>


or by modifying the properties directly. For example:

```cpp
motor.phase_resistance = 0.4f;     // Ohms
motor.KV_rating = 120.0f;          // rpm/V (or KV from datasheet)
motor.axis_inductance.q = 0.001f;  // Henries (optional)
```

## Step 2: Enable estimated current mode

Switch torque control from voltage to estimated current:

```cpp
motor.controller = MotionControlType::torque;
motor.torque_controller = TorqueControlType::estimated_current;

// Set safe limits
motor.updateCurrentLimit(1.0);   // Amps
motor.target = 0.0;              // Amps (torque command)
```

The example of the full code is shown below. You can also copy your working closed-loop code and just change the torque controller type to get started with estimated current control.

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>
<a href ="javascript:show('h','type');" class="btn btn-type btn-h"> Hybrid Stepper motors</a>

<div class="type type-b"  markdown="1">

```cpp
#include <SimpleFOC.h>

#define POLE_PAIRS        TODO
#define PHASE_RESISTANCE  TODO
#define KV_RATING         TODO
#define INDUCTANCE_Q      NOT_SET  // optional - can be left as NOT_SET for basic use

BLDCMotor motor = BLDCMotor(POLE_PAIRS, PHASE_RESISTANCE, KV_RATING, INDUCTANCE_Q);
// TODO instance driver
BLDCDriverXPWM driver = BLDCDriverXPWM(); 

// TODO instance sensor
Sensor sensor = Sensor();

Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  encoder.init();
  encoder.enableInterrupts(doA, doB);
  motor.linkSensor(&encoder);

  driver.voltage_power_supply = TODO; // set voltage [V]
  driver.init();
  motor.linkDriver(&driver);

  // Estimated current control
  motor.controller = MotionControlType::torque;
  motor.torque_controller = TorqueControlType::estimated_current;

  motor.updateCurrentLimit(1.0); // A
  motor.target = 0.0;            // A - zero torque command to start

  if(!motor.init()) { Serial.println("Motor init failed"); return; }
  if(!motor.initFOC()) { Serial.println("FOC init failed"); return; }

  command.add('M', doMotor, "Motor");
  _delay(1000);
}

void loop() {
  motor.loopFOC();
  motor.move();
  command.run();
}
```
</div>


<div class="type type-s hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

#define POLE_PAIRS        TODO
#define PHASE_RESISTANCE  TODO
#define KV_RATING         TODO
#define INDUCTANCE_Q      NOT_SET  // optional - can be left as NOT_SET for basic use

StepperMotor motor = StepperMotor(POLE_PAIRS, PHASE_RESISTANCE, KV_RATING, INDUCTANCE_Q);
// TODO instance driver
StepperDriverXPWM driver = StepperDriverXPWM(); 

// TODO instance sensor
Sensor sensor = Sensor();

Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  encoder.init();
  encoder.enableInterrupts(doA, doB);
  motor.linkSensor(&encoder);

  driver.voltage_power_supply = TODO; // set voltage [V]
  driver.init();
  motor.linkDriver(&driver);

  // Estimated current control
  motor.controller = MotionControlType::torque;
  motor.torque_controller = TorqueControlType::estimated_current;

  motor.updateCurrentLimit(1.0); // A
  motor.target = 0.0;            // A - zero torque command to start

  if(!motor.init()) { Serial.println("Motor init failed"); return; }
  if(!motor.initFOC()) { Serial.println("FOC init failed"); return; }

  command.add('M', doMotor, "Motor");
  _delay(1000);
}

void loop() {
  motor.loopFOC();
  motor.move();
  command.run();
}
```
</div>


<div class="type type-h hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

#define POLE_PAIRS        TODO
#define PHASE_RESISTANCE  TODO
#define KV_RATING         TODO
#define INDUCTANCE_Q      NOT_SET  // optional - can be left as NOT_SET for basic use

HybridStepperMotor motor = HybridStepperMotor(POLE_PAIRS, PHASE_RESISTANCE, KV_RATING, INDUCTANCE_Q);
// TODO instance driver
BLDCDriverXPWM driver = BLDCDriverXPWM(); 

// TODO instance sensor
Sensor sensor = Sensor();

Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  encoder.init();
  encoder.enableInterrupts(doA, doB);
  motor.linkSensor(&encoder);

  driver.voltage_power_supply = TODO; // set voltage [V]
  driver.init();
  motor.linkDriver(&driver);

  // Estimated current control
  motor.controller = MotionControlType::torque;
  motor.torque_controller = TorqueControlType::estimated_current;

  motor.updateCurrentLimit(1.0); // A
  motor.target = 0.0;            // A - zero torque command to start

  if(!motor.init()) { Serial.println("Motor init failed"); return; }
  if(!motor.initFOC()) { Serial.println("FOC init failed"); return; }

  command.add('M', doMotor, "Motor");
  _delay(1000);
}

void loop() {
  motor.loopFOC();
  motor.move();
  command.run();
}
```
</div>

### Validate torque response

1. Open Serial Monitor (115200)
2. Type `M0` - motor should feel free to move (almost as disconnected)
3. Type `M0.5` - Motor should accelerate and reach the max velocity smoothly
4. If stopped by hand, motor should resist with a force proportional to the current command (0.5 Amps in this case)
4. Type `M0.8` - you should feel stronger torque
5. Type `M-0.8` - motor should reverse the force against your hand with the same strength
6. Type `M0` again - motor should feel free to move again
7. Release motor and type `M1.0` 
   - motor should accelerate to the same speed as before (approximately)
   - The higher the current the higher the acceleration, but the speed should be consistent regardless of load
8. When motor is at max speed type `M0` - motor should decelerate smoothly to a stop

<blockquote class="info" markdown="1"> <p class="heading">⚠️ Troubleshooting: Motor Parameters Not Tuned Correctly</p>

If any of these steps fail, this probably means that some of the motor parameters is wrong. In that case the best way to proceed is to do a step by step validation of each one of the parameters. 

[See this guide for how to validate and tune motor parameters](motor_params_test#troubleshooting-motor-parameters){: .btn .btn-docs-middle}.

</blockquote>

### Quick Troubleshooting

The best way to troubleshoot is to test each motor parameter systematically. Here are the key diagnostics:

**Motor doesn't move or moves very slowly?**
- Phase resistance is probably **too low**
- Increase `motor.phase_resistance`
- [See phase resistance testing guide](motor_params_test#phase-resistance-testing)

**Motor and driver get hot at low currents?**
- Phase resistance is probably **too high**
- Decrease `motor.phase_resistance`

**Motor feels strange when idle (target = 0)?**
- KV rating is probably **incorrect**
- Motor should feel like it's disconnected when `motor.target = 0`
- Increase KV if motor resists rotation; decrease if it spins freely on its own
- [See KV rating testing guide](motor_params_test#kv-rating-testing)

**Motor is unstable or jerky at high speeds?**
- Add or increase inductance: `motor.axis_inductance.q = 0.001`
- [See inductance testing guide](motor_params_test#inductance-value-testing)

For detailed diagnostics of each parameter, see the [full motor parameter testing guide](motor_params_test#troubleshooting-motor-parameters).


<blockquote class="info" markdown="1"> <p class="heading">Still having issues?</p> 
Browse throught the [SimpleFOC community forum](https://community.simplefoc.com/), there might already be a solution for your specific hardware or issue. Also don't hesitate to ask for help there!
</blockquote>


## Step 3: Velocity control and tuning

Now that you have torque control working, you can switch to other motion control types like velocity or position. The estimated current controller will still be used under the hood to achieve the commanded velocity or position with the correct torque. For example, to switch to velocity control:

```cpp
motor.controller = MotionControlType::velocity;
```
or using the [command interface](command_interface):
```sh
MC1
```

Now to a quick test:
1. `M0` - motor should be static and hold position
2. `M6.28` - motor should accelerate and reach approximately 1 rotation per second (6.28 rad/s)
3. `M-6.28` - motor should reverse and reach -1 rotation per second
4. `M0` - motor should decelerate smoothly to a stop


### Troubleshooting

**Motor oscilates and vibrates at low speeds?**
- This usually means that the velocity PID needs to be tuned. See the [velocity control tuning guide](tuning_velocity_loop) for how to do that.
- If you want to move quickly without tuning, you can also try lowering the velocity PID P and I gains to reduce oscillations. 
  - Read the gains, type `MVP` and `MVI` to the terminal to see the current values
  - Try lowering them gradually until the oscilations stop

**Motor doesn't reach the target velocity?**
- Current limit is probably too low. 
   - Increase it with `motor.updateCurrentLimit(3.0);` 
   - Using comand interface: `MCL3.0`
- Voltage limit too low (if changed but the user)
   - Increase `motor.voltage_limit` to 50-60% of your supply voltage
   - Using command interface: ex. `MLU5.0` to set the `motor.voltage_limit` to 5V
- Power supply too low
   - Try to increase the supply voltage or use a more powerful supply

**Motor and driver heat up at low speeds?**
- This usually means that the phase resistance is too low, causing high current draw at low speeds
- Increase `motor.phase_resistance` to reduce current and heat, but be careful not to set it too high or the motor won't move well
- [See phase resistance testing guide](motor_params_test#phase-resistance-testing)

**Motor feels weak?**
- This usually means that the phase resistance is too high, limiting current and torque
  - Decrease `motor.phase_resistance` to allow more current and torque, but be careful not to set it too low or the motor and driver may overheat
  - [See phase resistance testing guide](motor_params_test#phase-resistance-testing)
- It can also be the current limit is set too low, or the supply voltage is insufficient
   - Increase `motor.current_limit`
   - Using command interface: `MCL3.0` for setting it to 3 Amps

## Step 4: Position control and tuning

Now you can also switch to angle control:

```cpp
motor.controller = MotionControlType::angle;
```
or using the [command interface](command_interface):
```sh
MC2
```

Then you can test it by sending angle commands in radians:
1. `M3.14` - motor should rotate to half a rotation (180 degrees)
2. `M6.28` - motor should rotate to one full rotation (360 degrees)
3. `M0` - motor should rotate back to the starting position (0 degrees)

### Troubleshooting
**Motor oscilates and vibrates at low speeds?**
- This usually means that the angle PID needs to be tuned. See the [angle control tuning guide](tuning_angle_loop) for how to do that.
- If you want to move quickly without tuning, you can also try lowering the angle PID P and I gains to reduce oscillations. 
  - Read the P gain (typically the only one to set) with `MAP` command, and lower it gradually until the oscilations stop

<blockquote class="info" markdown="1"> <p class="heading">Still having issues?</p> 
Browse throught the [SimpleFOC community forum](https://community.simplefoc.com/), there might already be a solution for your specific hardware or issue. Also don't hesitate to ask for help there!
</blockquote>


## What's Next?

You now have a fully functional model-based FOC control system! Depending on your needs:

### For Robotics/Automation
- See [Motion Control docs](closed_loop_motion_control)
- Implement feedback loops in your application code

### For Precision Applications
- Tune PID parameters for your motor
- Use angle mode for position control
- See [PID tuning guide](practical_guides#pid-tuning)

### For Learning
- Explore the [SimpleFOC library examples](https://github.com/simplefoc/Arduino-FOC/tree/master/examples)
- Read about [FOC theory and source code](digging_deeper)
- For debugging, monitoring, and communication docs, see [Communication](communication) and [Debugging](debugging) sections

