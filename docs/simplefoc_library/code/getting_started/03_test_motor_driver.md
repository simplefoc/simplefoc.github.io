---
layout: default
title: Open-Loop (Motor + Driver)
nav_order: 3
description: "Test motor and driver together with open-loop control"
permalink: /test_motor_driver
parent: Getting Started Guide
grand_parent: Writing the Code
great_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---

# Motor + Driver Test (Open-Loop)

⏱️ **~15 minutes**

Test your motor and driver together using open-loop control. This step determines your motor's pole pair count and validates that the driver can control your specific motor.

<blockquote class="warning" markdown="1"> <p class="heading">⚠️ Important Safety Notice</p>
- **Actual current will flow** through your motor and electronics
- Motor may spin unexpectedly if parameters are wrong
- **Secure your motor** to prevent injury
- Set power supply current limit appropriately
- Be ready to power off if something seems wrong
</blockquote>

## Select Your Motor + Driver Combination
<a href="javascript:show('template','open');" class="btn btn-open btn-template btn-primary">Sketch Template</a>

<a href="javascript:show('bldc3','open');" class="btn btn-open btn-bldc3">BLDC Motor + 3PWM Driver</a>
<a href="javascript:show('bldc6','open');" class="btn btn-open btn-bldc6">BLDC Motor + 6PWM Driver</a> 

<a href ="javascript:show('stepper2','open');"  class="btn btn-open btn-stepper2">Stepper Motor + 2PWM Driver</a> 
<a href ="javascript:show('stepper4','open');"  class="btn btn-open btn-stepper4">Stepper Motor + 4PWM Driver</a> 
<a href ="javascript:show('hybrid3','open');"  class="btn btn-open btn-hybrid3">Hybrid Stepper Motor + 3PWM Driver</a> 
<a href ="javascript:show('hybrid6','open');"  class="btn btn-open btn-hybrid6">Hybrid Stepper Motor + 6PWM Driver</a> 


<div class="open open-template" markdown="1">

```cpp
#include <SimpleFOC.h>

// TODO: instantiate your motor
// TODO: instantiate your driver

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = TODO: //set your power supply voltage;
  // limit the maximal dc voltage the driver can set
  driver.voltage_limit = TODO: //set your voltage limit; Usually half of power supply voltage is a good starting point
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = TODO: //set your voltage limit;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // loop FOC algorithm, should be called as 
  // frequently as possible for best 
  // performance (e.g. 1kHz+)
  motor.loopFOC();

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="open open-bldc3 hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDCMotor(pole pair number);
BLDCMotor motor = BLDCMotor(TODO);
// BLDCDriver3PWM(pwmA, pwmB, pwmC, Enable(optional));
BLDCDriver3PWM driver = BLDCDriver3PWM(TODO, TODO, TODO, TODO); // set your pins

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = TODO: //set your power supply voltage;
  // limit the maximal dc voltage the driver can set
  driver.voltage_limit = TODO: //set your voltage limit; Usually half of power supply voltage is a good 
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = TODO: //set your voltage limit in volts;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // loop FOC algorithm, should be called as 
  // frequently as possible for best 
  // performance (e.g. 1kHz+)
  motor.loopFOC();

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="open open-bldc6 hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDCMotor(pole pair number);
BLDCMotor motor = BLDCMotor(TODO);
// BLDCDriver6PWM(pwmAh, pwmAl, pwmBh, pwmBl, pwmCh, pwmCl, (en optional))
BLDCDriver6PWM driver = BLDCDriver6PWM(TODO, TODO, TODO, TODO, TODO, TODO, TODO); // set your pins

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = TODO: //set your power supply voltage;
  // limit the maximal dc voltage the driver can set
  driver.voltage_limit = TODO: //set your voltage limit; Usually half of power supply voltage is a good 
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = TODO: //set your voltage limit in volts;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // loop FOC algorithm, should be called as 
  // frequently as possible for best 
  // performance (e.g. 1kHz+)
  motor.loopFOC();

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="open open-stepper2 hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// StepperMotor(pole pair number);
StepperMotor motor = StepperMotor(TODO);
// StepperDriver2PWM(pwm1, dir1, pwm2, dir2,(en1, en2 optional))
StepperDriver2PWM driver = StepperDriver2PWM(TODO, TODO, TODO, TODO); // set your pins

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = TODO: //set your power supply voltage;
  // limit the maximal dc voltage the driver can set
  driver.voltage_limit = TODO: //set your voltage limit; Usually half of power supply voltage is a good 
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = TODO: //set your voltage limit in volts;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // loop FOC algorithm, should be called as 
  // frequently as possible for best 
  // performance (e.g. 1kHz+)
  motor.loopFOC();

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="open open-stepper4 hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// StepperMotor(pole pair number);
StepperMotor motor = StepperMotor(TODO);
// StepperDriver4PWM(ph1A, ph1B, ph2A, ph2B, (en1, en2 optional))
StepperDriver4PWM driver = StepperDriver4PWM(TODO, TODO, TODO, TODO, TODO, TODO); // set your pins

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = TODO: //set your power supply voltage;
  // limit the maximal dc voltage the driver can set
  driver.voltage_limit = TODO: //set your voltage limit; Usually half of power supply voltage is a good 
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = TODO: //set your voltage limit in volts;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // loop FOC algorithm, should be called as 
  // frequently as possible for best 
  // performance (e.g. 1kHz+)
  motor.loopFOC();

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```
</div>

<div class="open open-hybrid3 hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// StepperMotor(pole pair number);
HybridStepperMotor motor = HybridStepperMotor(TODO);
// BLDCDriver3PWM(pwmA, pwmB, pwmC, Enable(optional));
BLDCDriver3PWM driver = BLDCDriver3PWM(TODO, TODO TODO, TODO); // set your pins

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = TODO: //set your power supply voltage;
  // limit the maximal dc voltage the driver can set
  driver.voltage_limit = TODO: //set your voltage limit; Usually half of power supply voltage is a good 
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = TODO: //set your voltage limit in volts;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // loop FOC algorithm, should be called as 
  // frequently as possible for best 
  // performance (e.g. 1kHz+)
  motor.loopFOC();

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="open open-hybrid6 hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// HybridStepperMotor(pole pair number);
HybridStepperMotor motor = HybridStepperMotor(TODO);
// BLDCDriver6PWM(pwmAh, pwmAl, pwmBh, pwmBl, pwmCh, pwmCl, (en optional))
BLDCDriver6PWM driver = BLDCDriver6PWM(TODO, TODO, TODO, TODO, TODO, TODO, TODO); // set your pins

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = TODO: //set your power supply voltage;
  // limit the maximal dc voltage the driver can set
  driver.voltage_limit = TODO: //set your voltage limit; Usually half of power supply voltage is a good 
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = TODO: //set your voltage limit in volts;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // loop FOC algorithm, should be called as 
  // frequently as possible for best 
  // performance (e.g. 1kHz+)
  motor.loopFOC();

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```

</div>

## Setup Steps

1. **Copy the code**: Find your motor + driver combination above and copy the code into a new Arduino sketch
2. **Update parameters:**
   - Implement the TODOs in the code:
   - Pin numbers (PWM pins, enable pin, direction pins)
   - Power supply voltage
   - **Pole pair count** - start with a guess from your motor datasheet
   - Voltage limit (see guidance below)
3. **Upload** and open Serial Monitor (115200 baud)

## Voltage Limit Guide

The `motor.voltage_limit` directly determines current: **current = voltage / phase_resistance**

### For gimbal motors (high resistance ~10Ω)
- Examples work well with `motor.voltage_limit = 3-5V`
- These motors are low-power and safe

### For higher power motors
- **Find phase resistance** from datasheet or [measure it](phase_resistance)
- Set limit to keep current below 2A: `motor.voltage_limit = 2 * phase_resistance`
- Better yet: set `motor.phase_resistance` in code, then use `motor.current_limit` instead

### If you don't know phase resistance
- Start very conservatively: `motor.voltage_limit = 0.5V`
- Slowly increase if motor doesn't spin

## Testing

<blockquote class="info" markdown="1"> <p class="heading">✓ What to expect</p>
1. See `Motor ready!` in serial terminal (not `Motor init failed!`)
2. Motor spins once you set a target velocity
3. In serial terminal, type `T6.28` to set target to one rotation/sec
   - Motor should spin at exactly 1 rotation per second
   - If it spins faster/slower, your pole pair count is wrong
</blockquote>

## Finding Correct Pole Pair Count

This is why this test is valuable! If your motor doesn't spin at exactly one rotation per second when you command 6.28 rad/s:

1. **Motor spins too slowly?** → Try a **lower** pole pair number
2. **Motor spins too fast?** → Try a **higher** pole pair number
3. **Adjust and test** until exactly 1 rotation = `T6.28` command

Once you find the right pole pair number, note it for the next step!

## Troubleshooting

**Motor doesn't spin?**
- Check initialization messages in serial terminal
- Verify pole pair number is reasonable (usually 2-15)
- Increase `motor.voltage_limit` gradually (start at 1V)
- Check motor is mechanically free to spin

**Motor spins but is noisy/unstable?**
- Check the motor connection to the driver - are all phases connected correctly?
- Is driver entering fault state - maybe the current is too high? Try lowering `motor.voltage_limit`
- Check that your power supply can provide enough current for the motor - is it limiting current and causing voltage drop?

**Current too high?**
- Reduce `motor.voltage_limit`
- Verify you're using right motor type (BLDC vs Stepper)
- Check phase resistance of your motor

## Next Step

Once you've confirmed:
- ✓ Motor spins smoothly
- ✓ Correct pole pair count found (spins 1 rotation per second at 6.28 rad/s)
- ✓ Current is reasonable

Proceed to:

[Closed-Loop Control](test_closedloop){: .btn .btn-step}
