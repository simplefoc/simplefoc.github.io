---
layout: default
title: Closing the Loop Test
nav_order: 4
description: "Test closed-loop control with voltage-based torque mode"
permalink: /test_closedloop
parent: Getting Started Guide
grand_parent: Writing the Code
great_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---

# Closed-Loop Control (Voltage-Based Torque)

⏱️ **~15 minutes**

Now that you have a working motor and sensor, test closed-loop Field Oriented Control (FOC). This is the simplest closed-loop mode: torque control using voltage.

## What's Different From Open-Loop?

- **Open-loop**: Spins motor at estimated angle, no feedback
- **Closed-loop**: Uses sensor feedback to control torque precisely
- **Advantage**: Stable, responsive, works across speed ranges

[See motion control documentation](motion_control) for more details on control types and how/when to switch between them.

## Select Your Motor + Driver + Sensor Combination

<a href="javascript:show('temp','cl');" class="btn btn-cl btn-temp btn-primary">Sketch Template</a>
<a href="javascript:show('bldc','cl');" class="btn btn-cl btn-bldc">BLDC + 3PWM Driver + Encoder</a> 
<a href ="javascript:show('stepper','cl');"  class="btn btn-cl btn-stepper">Stepper + 2PWM Driver + Encoder</a> 

<div class="cl cl-temp" markdown="1">

Here is a template with `TODO` entries you can customize:

```cpp
#include <SimpleFOC.h>

// TODO: motor instance 
// TODO: driver instance
// TODO: sensor instance

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // TODO: sensor config
  // init the sensor
  sensor.init();
  // link the motor to the sensor
  motor.linkSensor(&sensor);

  // TODO: driver config
  // init the driver
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link driver
  motor.linkDriver(&driver);

  // TODO: motor voltage alignment config
  // This voltage is used to align sensor with motor
  motor.voltage_sensor_align = 3; // start conservatively

  // set motion control loop to be used
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // use monitoring with serial
  // comment out if not needed
  motor.useMonitoring(Serial);

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

  // set the initial motor target
  motor.target = 2; // Volts 

  // add target command M
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target using serial terminal and command M:"));
  _delay(1000);
}

void loop() {
  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="cl cl-bldc hide" markdown="1">

**BLDC motor** with **3PWM driver** and **Encoder** sensor:

```cpp
#include <SimpleFOC.h>

// BLDC motor & driver instance
BLDCMotor motor = BLDCMotor(TODO);
BLDCDriver3PWM driver = BLDCDriver3PWM(TODO, TODO, TODO, TODO);

// encoder instance
Encoder encoder = Encoder(TODO, TODO, TODO);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_limit = TODO;
  // driver init
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link driver
  motor.linkDriver(&driver);

  // aligning voltage
  motor.voltage_sensor_align = 5;

  // set motion control loop to be used
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // comment out if not needed
  motor.useMonitoring(Serial);

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

  // set the initial motor target
  motor.target = 2; // Volts 

  // add target command M
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target using serial terminal and command M:"));
  _delay(1000);
}

void loop() {
  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="cl cl-stepper hide" markdown="1">

**Stepper motor** with **2PWM driver** and **Encoder** sensor:

```cpp
#include <SimpleFOC.h>

// Stepper motor & driver instance
StepperMotor motor = StepperMotor(TODO);
StepperDriver2PWM driver = StepperDriver2PWM(TODO, TODO, TODO, TODO);

// encoder instance
Encoder encoder = Encoder(TODO, TODO, TODO);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_limit = TODO;
  // driver init
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link driver
  motor.linkDriver(&driver);

  // aligning voltage
  motor.voltage_sensor_align = 5;

  // set motion control loop to be used
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  motor.init();
  // align sensor and start FOC
  if(!motor.initFOC()){
    Serial.println("FOC init failed!");
    return;
  }

  // set the initial motor target
  motor.target = 2; // Volts 

  // add target command M
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target using serial terminal and command M:"));
  _delay(1000);
}

void loop() {
  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // user communication
  command.run();
}
```

</div>

## Key Concepts

### Sensor Alignment (`motor.voltage_sensor_align`)
During `motor.initFOC()`, the motor briefly spins to determine the sensor's electrical zero position. The voltage used for this determines the alignment current.

- **Too low** (~0.5V): May not rotate enough to align
- **Safe range** (2-5V): Works for gimbal and small motors
- **Too high**: Can draw excessive current
- **Start with 3V** and adjust if needed

### Motion Control Types
Once closed-loop works, you can switch modes with serial commands:

```sh
MC0  = Torque mode
MC1  = Velocity mode  
MC2  = Angle mode
```

Then use `M<value>` to set target:
- Torque: `M2` = 2 Volts
- Velocity: `M6.28` = 1 rotation/second
- Angle: `M3.14` = half rotation

## Testing

<blockquote class="info" markdown="1"> <p class="heading">✓ What to expect</p>
1. During setup, motor will briefly spin (sensor alignment)
2. Serial output: `Motor ready.` (not `FOC init failed!`)
3. Motor smoothly responds to voltage changes via serial
4. Motor feels firm (resistance) when you try to hold it
</blockquote>

### Basic Test
1. Open serial terminal (115200 baud)
2. Type `M0` - motor should go to zero torque (no resistance)
3. Type `M1` - motor should produce light torque (you can feel it)
4. Type `M2` - motor should produce more torque
5. Type `M0` again - torque disappears

### Velocity Control Test
1. Type `MC1` to switch to velocity mode
> If motor unstable (or very noisy) it will need velocity PI tuning (see troubleshooting below)
2. Type `M6.28` to spin 1 rotation/second
3. Type `M0` to stop
4. Motor should accelerate smoothly and maintain speed

### Angle Control Test
1. Type `MC2` to switch to angle mode
> If motor unstable (or very noisy) it will need angle P tuning (see troubleshooting below)
2. Type `M3.14` to rotate half turn
3. Motor should move to that angle and hold it

## Troubleshooting

**FOC init failed?**
- Check sensor is working (from previous test)
- Sensor alignment voltage too low: try increasing `motor.voltage_sensor_align`
- Pole pair number wrong: verify from open-loop test
- Sensor not aligned: see serial debug output for hints

**Motor spins but is unstable/noisy?**
- Motor/driver connection loose: verify wiring
- Velocity PI tuning needed: decrease `motor.PID_velocity.P` and `motor.PID_velocity.I` - see full tuning guide in [Motion Control documentation](tuning_velocity_loop)
- Angle P tuning needed: decrease `motor.P_angle`  - see full tuning guide in [Motion Control documentation](angle_cascade_control#angle-loop-outer-loop-guidance)

**Motor doesn't spin at all?**
- Check that driver initialized (look for `Driver init failed!`)
- Verify sensor is reading (check debug output)
- Pole pair number wrong: double-check from previous test
- Try increasing `motor.voltage_sensor_align` to 1-2V minimum

**Current too high during alignment?**
- Reduce `motor.voltage_sensor_align`
- Try setting it with the phase resistance in mind:
   - If you motor has a phase resistance of 0.5 Ohms, then 3V will draw 6A during alignment (I = V/R). If this is too high for your motor or driver, reduce the voltage accordingly.
- If resistance unknown, try measuring it - [measuring guide](phase_resistance)

**In torque mode, motor spins faster in one direction than the other?**
- This is usually the issue with the sensor alignment. Try increasing `motor.voltage_sensor_align` to ensure it fully aligns.


**Some specific issues**
- AS5600 magnet polarisation issue - [example issue](https://community.simplefoc.com/t/drv8302-tarot-motor-control-type-voltage-magnet-polarization-problem/401?u=antun_skuric) - need to replace the magnet


<blockquote class="info" markdown="1"> <p class="heading">Still having issues?</p> 
Browse throught the [SimpleFOC community forum](https://community.simplefoc.com/), there might already be a solution for your specific hardware or issue. Also don't hesitate to ask for help there!
</blockquote>



## What's Next?

You now have a fully functional voltage based control system! If the perfrormance of this control mode is sufficient for your application, you can start building on top of it with motion profiles, trajectory planning, and higher level feedback loops.

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


## More advanced control schemes

With voltage-based torque control working, you have a solid foundation! For more advanced control, current sensing or motor model based current estimation can improve overall performance a lot.

Once this works:
- **You don't have current sensing?** : <br>
[Estimated current control setup guide](estimated_current_guide){:.btn .btn-step}
- **You have current sensing?** : <br>
[FOC control setup guide](test_current){:.btn .btn-step}

## Relevant Documentation

- [Motion control documentation](closed_loop_motion_control)
- [Torque control documentation](torque_control)
- [Current sensing documentation](current_sense)
- [Practical guides](practical_guides)
- [Digging deeper](digging_deeper)