---
layout: default
title: Current Sense Setup
nav_order: 1
description: "Setup current sensing and validate d/q current feedback"
permalink: /test_current
parent: FOC Current Control
grand_parent: Writing the Code
great_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---

# Current Sense Testing and Validation

⏱️ **~20-40 minutes**

This guide shows how to setup **hardware current sensing** and validate that d/q current measurements are working correctly in closed-loop FOC.

[Read more about current sensing in SimpleFOC](current_sense){: .btn .btn-docs}

## Prerequisites

Before starting, make sure you already have:
- Working **sensor** and **driver** setup - [Sensor setup guide](test_sensor) and [Driver setup guide](test_driver)
- Closed-loop FOC working in **voltage torque mode** - [Closed-loop voltage control guide](test_closedloop)

### Current sensing hardware

You also need current sensing hardware:
- **Inline current sensors** (recommended for beginners) - measures phase currents directly
- **Lowside current sensors** (requires precise timing) - measures currents through low-side shunts

[Read more about choosing current sensing hardware](current_sense){: .btn .btn-docs}

## Step 1: Configure current sensing

Add current sensing to your working closed-loop FOC code.

### Select Your Motor + Driver + Sensor + Current Sense Combo

Now validate that **d/q currents** are visible and behaving as expected in closed-loop FOC.

<a href="javascript:show('temp','current');" class="btn btn-current btn-temp btn-primary">Sketch Template</a>
<a href="javascript:show('bldc','current');" class="btn btn-current btn-bldc">BLDC + 3PWM + Encoder + Lowside CS</a> 
<a href ="javascript:show('stepper','current');"  class="btn btn-current btn-stepper">Stepper + 2PWM + Encoder + Inline CS</a> 

<div class="current current-temp" markdown="1">

Template with `TODO` entries:

```cpp
#include <SimpleFOC.h>

// TODO: motor instance 
// TODO: driver instance
// TODO: sensor instance
// TODO: current sense instance

Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  // init the sensor
  sensor.init();
  motor.linkSensor(&sensor);

  // init the driver
  driver.voltage_power_supply = TODO; // set your power supply voltage
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  motor.linkDriver(&driver);
  // IMPORTANT: link driver to current sense
  current_sense.linkDriver(&driver);

  // init current sense
  if(!current_sense.init()){
    Serial.println("Current sense init failed!");
    return;
  }
  // link current sense to motor
  motor.linkCurrentSense(&current_sense);

  motor.voltage_sensor_align = 3;
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // enable current monitoring
  motor.useMonitoring(Serial);
  motor.monitor_downsampling = 100;
  motor.monitor_variables = _MON_CURR_Q | _MON_CURR_D;

  // initialize motor
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }
  // align sensor and start FOC
  if(!motor.initFOC()){
    Serial.println("FOC init failed!");
    return;
  }

  motor.target = 0;
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready - Current sensing active."));
  _delay(1000);
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>

<div class="current current-bldc hide" markdown="1">

An example code for **BLDC + 3PWM driver + Encoder + Lowside Current Sense:**

```cpp
#include <SimpleFOC.h>

// Motor & driver
BLDCMotor motor = BLDCMotor(TODO); // set pole pairs
BLDCDriver3PWM driver = BLDCDriver3PWM(TODO, TODO, TODO, TODO); // set PWM pins

// Sensor
Encoder encoder = Encoder(TODO, TODO, TODO);
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// Current sense (0.01 Ohm shunt, 50 gain amplifier)
LowsideCurrentSense current_sense = LowsideCurrentSense(TODO, TODO, TODO, TODO);

Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);
  
  // Encoder init
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  motor.linkSensor(&encoder);

  // Driver init
  driver.voltage_power_supply = TODO;
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  motor.linkDriver(&driver);
  // IMPORTANT: Link driver to current sense for synchronization
  current_sense.linkDriver(&driver);

  // Current sense init
  if(!current_sense.init()){
    Serial.println("Current sense init failed!");
    return;
  }
  motor.linkCurrentSense(&current_sense);

  // FOC config (still using voltage control)
  motor.voltage_sensor_align = TODO;
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // Monitoring
  motor.useMonitoring(Serial);
  motor.monitor_downsampling = 100;
  motor.monitor_variables = _MON_CURR_Q | _MON_CURR_D;

  // Init motor
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }
  if(!motor.initFOC()){
    Serial.println("FOC init failed!");
    return;
  }

  motor.target = 0;
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready - Current sensing active."));
  _delay(1000);
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>

<div class="current current-stepper hide" markdown="1">


An example code for **Stepper + 2PWM driver + Encoder + Inline Current Sense:**

```cpp
#include <SimpleFOC.h>

// Motor & driver
StepperMotor motor = StepperMotor(TODO); // set pole pairs
StepperDriver2PWM driver = StepperDriver2PWM(TODO, TODO, TODO, TODO); // set PWM pins

// Sensor
Encoder encoder = Encoder(TODO, TODO, TODO);
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// Current sense
InlineCurrentSense current_sense = InlineCurrentSense(TODO, TODO, TODO, TODO);

Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);
  
  // Encoder init
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  motor.linkSensor(&encoder);

  // Driver init
  driver.voltage_power_supply = TODO;
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  motor.linkDriver(&driver);
  // IMPORTANT: Link driver to current sense
  current_sense.linkDriver(&driver);

  // Current sense init
  if(!current_sense.init()){
    Serial.println("Current sense init failed!");
    return;
  }
  motor.linkCurrentSense(&current_sense);

  // FOC config
  motor.voltage_sensor_align = TODO;
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // Monitoring
  motor.useMonitoring(Serial);
  motor.monitor_downsampling = 100;
  motor.monitor_variables = _MON_CURR_Q | _MON_CURR_D;

  // Init motor
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }
  if(!motor.initFOC()){
    Serial.println("FOC init failed!");
    return;
  }

  motor.target = 0;
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready - Current sensing active."));
  _delay(1000);
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

</div>

Copy this code to your application and fill in the `TODO` entries with your hardware parameters. Then upload and open the serial monitor to validate that current sensing is working correctly.

## Key Configuration: Current Sense Parameters

There are two current sense strategies implemented in SimpleFOC:
- **Inline Current Sense** (`InlineCurrentSense`) - For inline current sensing techniques
- **Lowside Current Sense** (`LowsideCurrentSense`) -  For lowside current sensing techniques (requires precide ADC & PWM timing)


Both `InlineCurrentSense` and `LowsideCurrentSense` require accurate configuration of the current sense parameters to work correctly. Either shunt resistance and amplifier gain, or the overall current sense ratio can be used.

<a href="javascript:show('inline','cs');" class="btn bts-cs btn-inline btn-primary">Inline Current Sense Parameters</a>
<a href="javascript:show('lowside','cs');" class="btn btn-cs btn-lowside ">Lowside Current Sense Parameters</a>

<div class="cs cs-inline" markdown="1">
```cpp
InlineCurrentSense(shunt_resistance, amplifier_gain, pin_A, pin_B, pin_C);
// or
InlineCurrentSense(current_sense_ratio_mV_per_A, pin_A, pin_B, pin_C);
```
</div>
<div class="cs cs-lowside hide" markdown="1">
```cpp
LowsideCurrentSense(shunt_resistance, amplifier_gain, pin_A, pin_B, pin_C);
// or 
LowsideCurrentSense(current_sense_ratio_mV_per_A, pin_A, pin_B, pin_C);
```
</div>


[See current sensing documentation](current_sense) for detailed parameter selection based on your hardware.


### Common Configurations

| Sensor Type | Shunt Resistance [Ω] | Gain [V/V] | Overall Gain [mV/V] |  Range @ 5V | Example |
|---|---|---|---|---|
| [Allegro ACS712](https://www.allegromicro.com/-/media/files/datasheets/acs712-datasheet.ashx) (30A) | - | - | 66 |±30A |`InlineCurrentSense(66.0f, A0, A1)` |
| Custom Shunt (0.01Ω) + [INA240A2](https://www.ti.com/product/INA240)| 0.01 Ω | 50 | 500 | ±5A | `InlineCurrentSense(0.01f, 50.0f, A0, A2)` |
| Custom Shunt (0.005Ω) + [INA240A1](https://www.ti.com/product/INA240) | 0.005 Ω | 20 | 100 | ±25A | `LowsideCurrentSense(0.005f, 20.0f, A0, A2)` |

Consult [Current Sensing docs](current_sense) for your specific configuration.

### Critical: ADC Pin Selection

Current sense requires **ADC pins** (analog input). Not all pins have ADC:

✅ Use ADC pins like: A0, A1, A2, A3, A4, A5 (Arduino like boards)

❌ Avoid digital-only pins: D0, D1, etc.

See [Choosing ADC Pins guide](choosing_adc_pins) for your microcontroller.

## Step 2: Validate d/q current measurements

Once current sensing is configured, test that d/q currents are measured correctly in closed-loop operation.

### What to expect

- Serial terminal shows motor current q and d current values in milliamps  (`Iq` and `Id`)
   > TIP: Visualize currents in **Arduino Serial Plotter** for easier analysis
- Current values update smoothly with motor torque changes
- You can see `Iq` (torque current) change as you adjust target
- `Id` (field current) should stay near zero at low speeds

### Validation procedure

Test that current measurements respond correctly to motor commands:

1. **Static motor test:**
   - Set target voltage `M0`
   - Hold motor firmly in place
   - Set `M1` (1 Volts)
   - Current `Iq` should be proportional to the voltage command (2x voltage should give ~2x current)
   - Current `Id` should be close to 0

2. **Rotating motor test:**
   - Release motor, set `M2`
   - Motor spins steadily
   - Current `Iq` should drop as back-EMF increases with speed 
   - Current `Id` may rise slightly at higher speeds but should remain much lower than `Iq`
     - It drops to zero if motor stalled by hand

### Troubleshooting

**Current sense init failed?**
- Check pins are actual **ADC pins** 
- Verify pins are not used for PWM
- See [Choosing ADC Pins guide](choosing_adc_pins)

**Motor movement is jittery or unstable?**
- This probably means that there is too much serial output or the current values are too noisy
- Try increasing `motor.monitor_downsampling` to 500 or 1000 to reduce serial output frequency
- You can also use the Commander interface without re-uploading (ex $MMD500$ to set monitor downsampling to 500)

**Current values are noisy but respond correctly?**
- Check motor connections and wiring
- If current noicy a bit, but responds to commands correctly, try increasing current filtering:
```cpp
motor.LPF_current_q.Tf = 0.001; // increase low-pass filter for Iq - default is 0.0005s
motor.LPF_current_d.Tf = 0.001; // increase low-pass filter for Id - default is 0.0005s
```
or in commander:
```sh
MQF0.001 # set Iq filter time constant to 1ms
MDF0.001 # set Id filter time constant to 1ms
``` 

**Current reads wrong values ?**
- If current values cannot correspond the motor parameters (too high or low)
  > BEWARE: Current is shown in milliamps (mA), not amps (A)
- Check current sense parameters (shunt resistance, gain) are correct
  - Verify amplifier gain (check sensor datasheet)

**Current doesn't change with motor commands?**
- Check that you're measuring on the correct pins!

**Currents are too noisy and do not respond to target changes?**
- Make sure you're not using Inline current sensing with a driver that requires Lowside current sensing (e.g., DRV8302)

**D and q currents are not static but sinusoidally changing?**
- This usually means the current sense is not well aligned with the motor - did the `initFOC()` function complete successfully?
- Try raising `motor.voltage_sensor_align` parameter (e.g., 2, 3, 4, etc.) and re-run `initFOC()` until currents stabilize
- Verify your pins, gains and shunt values are correct and skip the current sense align if you're sure in your parameters:
```cpp
current_sense.skip_align = true; // before initFOC()
```

## Next steps

Once you've validated that current sensing works and d/q currents are visible:

[Start the step 2: Full FOC Current Control](test_foc_current){: .btn .btn-step}

## Related references

- [Current sensing hardware guide](current_sense)
- [Choosing ADC pins](choosing_adc_pins)
- [Torque/FOC control](torque_control)
