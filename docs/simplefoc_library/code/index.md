---
layout: default
title: Writing the Code
nav_order: 3
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /code
has_children: True
has_toc: False
parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---

# Writing Your Motor Control Code

Ready to write <span class="simple">Simple<span class="foc">FOC</span>library</span> code? This page is your **reference guide** to understanding how the library works and how to structure your motor control applications.

## 📚 Before You Start

Choose your path based on your experience level:

| Your Situation | Recommended Starting Point | Link |
|---|---|---|
| **First time with SimpleFOC?** | Use this page as a reference to understand all components and their configuration options | [Start here](#quick-setup-overview) |
| **Already have the hardware and <br> you want to get started quickly?** | Follow the step-by-step guide to get your motor running as quickly as possible | [Getting Started](example_from_scratch)|
| **Building from existing code?** | Jump to the specific section you need below and follow the links to detailed documentation | [Jump to here](#reference--configuration-guide) |
| **Want to understand the theory and<br> library implementation details?** | Check out the detailed documentation for each component and control strategy | [Digging Deeper](digging_deeper) |

---

## Quick Setup Overview

See how to build a complete motor control application step-by-step. Click through each tab to see the code build incrementally:

<a href="javascript:show('0','setup');" id="btn-0" class="btn btn-setup btn-primary">Sensor</a>
<a href="javascript:show('1','setup');" id="btn-1" class="btn btn-setup">+Driver</a>
<a href="javascript:show('2','setup');" id="btn-2" class="btn btn-setup">+Current Sense</a>
<a href="javascript:show('3','setup');" id="btn-3" class="btn btn-setup">+Motor</a>
<a href="javascript:show('4','setup');" id="btn-4" class="btn btn-setup">+Real-time Loop</a>
<a href="javascript:show('5','setup');" id="btn-5" class="btn btn-setup">+User Interface</a>

<div class="setup setup-0" markdown="1" style="display:block">

```cpp
#include <SimpleFOC.h>

// 1️⃣ SENSOR
Encoder sensor = Encoder(2, 3, 2048);
void doA(){sensor.handleA();}
void doB(){sensor.handleB();}

void setup() {
  sensor.init();
  sensor.enableInterrupts(doA, doB);
}

void loop() {
  sensor.update();
}
```

</div>

<div class="setup setup-1" markdown="1" style="display:none">

```cpp
#include <SimpleFOC.h>

// 1️⃣ SENSOR
Encoder sensor = Encoder(2, 3, 2048);
void doA(){sensor.handleA();}
void doB(){sensor.handleB();}

// 2️⃣ DRIVER
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

void setup() {
  sensor.init();
  sensor.enableInterrupts(doA, doB);

  driver.voltage_power_supply = 12;
  driver.init();
}

void loop() {
  sensor.update();
}
```

</div>

<div class="setup setup-2" markdown="1" style="display:none">

```cpp
#include <SimpleFOC.h>

// 1️⃣ SENSOR
Encoder sensor = Encoder(2, 3, 2048);
void doA(){sensor.handleA();}
void doB(){sensor.handleB();}

// 2️⃣ DRIVER
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// 3️⃣ CURRENT SENSE (Optional)
InlineCurrentSense current_sense = InlineCurrentSense(0.01, 50, A0, A2);

void setup() {
  sensor.init();
  sensor.enableInterrupts(doA, doB);

  driver.voltage_power_supply = 12;
  driver.init();

  current_sense.linkDriver(&driver);
  current_sense.init();
}

void loop() {
  sensor.update();
}
```

</div>

<div class="setup setup-3" markdown="1" style="display:none">

```cpp
#include <SimpleFOC.h>

// 1️⃣ SENSOR
Encoder sensor = Encoder(2, 3, 2048);
void doA(){sensor.handleA();}
void doB(){sensor.handleB();}

// 2️⃣ DRIVER
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// 3️⃣ CURRENT SENSE (Optional)
InlineCurrentSense current_sense = InlineCurrentSense(0.01, 50, A0, A2);

// 4️⃣ MOTOR
BLDCMotor motor = BLDCMotor(11);

void setup() {
  sensor.init();
  sensor.enableInterrupts(doA, doB);

  driver.voltage_power_supply = 12;
  driver.init();

  current_sense.linkDriver(&driver);
  current_sense.init();

  motor.linkSensor(&sensor);
  motor.linkDriver(&driver);
  motor.linkCurrentSense(&current_sense);
  motor.init();
  motor.initFOC();
}

void loop() {
  sensor.update();
}
```

</div>

<div class="setup setup-4" markdown="1" style="display:none">

```cpp
#include <SimpleFOC.h>

// 1️⃣ SENSOR
Encoder sensor = Encoder(2, 3, 2048);
void doA(){sensor.handleA();}
void doB(){sensor.handleB();}

// 2️⃣ DRIVER
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// 3️⃣ CURRENT SENSE (Optional)
InlineCurrentSense current_sense = InlineCurrentSense(0.01, 50, A0, A2);

// 4️⃣ MOTOR
BLDCMotor motor = BLDCMotor(11);

void setup() {
  sensor.init();
  sensor.enableInterrupts(doA, doB);

  driver.voltage_power_supply = 12;
  driver.init();

  current_sense.linkDriver(&driver);
  current_sense.init();

  motor.linkSensor(&sensor);
  motor.linkDriver(&driver);
  motor.linkCurrentSense(&current_sense);
  motor.init();
  motor.initFOC();
}

void loop() {
  // 5️⃣ REAL-TIME LOOP
  motor.loopFOC();
  motor.move();
}
```

</div>

<div class="setup setup-5" markdown="1" style="display:none">

```cpp
#include <SimpleFOC.h>

// 1️⃣ SENSOR
Encoder sensor = Encoder(2, 3, 2048);
void doA(){sensor.handleA();}
void doB(){sensor.handleB();}

// 2️⃣ DRIVER
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// 3️⃣ CURRENT SENSE (Optional)
InlineCurrentSense current_sense = InlineCurrentSense(0.01, 50, A0, A2);

// 4️⃣ MOTOR
BLDCMotor motor = BLDCMotor(11);

// 6️⃣ USER INTERFACE
Commander commander = Commander(Serial);
void onMotor(char* cmd){ commander.motor(&motor, cmd); }
void setup() {
  Serial.begin(115200);
  
  sensor.init();
  sensor.enableInterrupts(doA, doB);

  driver.voltage_power_supply = 12;
  driver.init();

  current_sense.linkDriver(&driver);
  current_sense.init();

  motor.linkSensor(&sensor);
  motor.linkDriver(&driver);
  motor.linkCurrentSense(&current_sense);
  motor.init();
  motor.initFOC();
  
  // Add motor commands to serial interface
  commander.add('M', onMotor, "motor");
  Serial.println("Motor ready! Type 'M' for motor commands");
}

void loop() {
  // 5️⃣ REAL-TIME LOOP
  motor.loopFOC();
  motor.move(motor.target);
  
  // 6️⃣ USER INTERFACE
  commander.run();
}
```

</div>

---

# Core Components Reference

## Position Sensors

Initialize and configure your position sensor. The motor cannot be controlled without knowing its current angle.

The library supports four sensor types:
 - [Encoders](encoder): Optical, Capacitive, Magnetic encoders (ABI)
 - [Magnetic sensors](magnetic_sensor): SPI, I2C, Analog or PWM
 - [Hall sensors](hall_sensors): 3xHall sensing, Magnetic sensor (UVW interface) 
 - [Generic sensors](generic_sensor) **NEW📢**: A simplified sensor implementation for adding custom sensors 

Choose a position sensor to use with this example:

<a href="javascript:show(0,'sensor');" id="btn-0" class="btn btn-sensor btn-primary">Encoder</a> 
<a href ="javascript:show(1,'sensor');" id="btn-1" class="btn btn-sensor">Magnetic sensor</a> 
<a href ="javascript:show(2,'sensor');" id="btn-2" class="btn btn-sensor">Hall sensors</a> 

<div class="sensor-0 sensor" markdown="1" style="display:block">

```cpp
#include <SimpleFOC.h>

// Encoder(pin_A, pin_B, PPR)
Encoder sensor = Encoder(2, 3, 2048);
// channel A and B callbacks
void doA(){sensor.handleA();}
void doB(){sensor.handleB();}

 
void setup() {  
  // initialize encoder hardware
  sensor.init();
  // hardware interrupt enable
  sensor.enableInterrupts(doA, doB);

}

void loop() {
  
}
```


Encoders as position sensors are implemented in the class `Encoder` and are defined by its:
  - `A` and `B` channel pin numbers: `2` and `3`
  - Encoder  `PPR` (impulses per revolution number): `2048`
  - `Index` pin number *(optional)*
   
</div>

<div class="sensor sensor-1" markdown="1" style="display:none">


```cpp
#include <SimpleFOC.h>

// SPI example
// MagneticSensorSPI(int cs, float bit_resolution, int angle_register)
MagneticSensorSPI sensor = MagneticSensorSPI(10, 14, 0x3FFF);

void setup() {
  // initialize magnetic sensor hardware
  sensor.init();
}

void loop() {

}
```

This is an example initialisation of a 14 bit SPI based magnetic sensor such as the <a href="https://www.mouser.fr/ProductDetail/ams/AS5X47U-TS_EK_AB?qs=sGAEpiMZZMve4%2FbfQkoj%252BBDLPCj82ZLyYIPEtADg0FE%3D">AS5047u <i class="fa fa-external-link"></i></a>, connected to pin `10`.<br>
Magnetic sensors using the SPI protocol are implemented in the class `MagneticSensorSPI` and are defined with their
 - `chip_select` pin: `10`
 - bit resoluion of the sensor overall `12`  the `CPR` can be calculated as `CPR = 2^14bit =16384`
 - `angle` SPI register: `0x3FFF`

</div>

<div class="sensor sensor-2" markdown="1" style="display:none">


```cpp
#include <SimpleFOC.h>

// Hall sensor instance
// HallSensor(int hallA, int hallB , int hallC , int pp)
//  - hallA, hallB, hallC    - HallSensor A, B and C pins
//  - pp                     - pole pairs
HallSensor sensor = HallSensor(2, 3, 4, 11);

// Interrupt routine initialization
// channel A and B callbacks
void doA(){sensor.handleA();}
void doB(){sensor.handleB();}
void doC(){sensor.handleC();}

void setup() {
  // initialize sensor hardware
  sensor.init();
  // hardware interrupt enable
  sensor.enableInterrupts(doA, doB, doC);
}

void loop() {

}
```

This is an example of a 3xHall position sensor connected to a `11` pole pair motor.<br>
Hall sensors are implemented in the class `HallSensors` and are defined by their
 - pins `hallA`, `hallB` and `hallC`: `2`, `3` and `4`
 - motor pole pair number: `11``

</div>


Initialize the hardware pins by running `sensor.init()`.

For full documentation of the setup and all configuration parameters please visit the <a href="sensors"> position sensors docs <i class="fa fa-external-link"></i></a>.


## Motor Drivers

Initialize the motor driver. This component translates motor control commands into PWM signals for your motor phases. 


<a href="javascript:show('0d','driver');" id="btn-0d" class="btn-driver btn btn-primary">BLDC Driver - 3PWM</a> 
<a href ="javascript:show('1d','driver');" id="btn-1d" class="btn-driver btn">Stepper Driver 4PWM</a>


<div class="driver driver-0d" markdown="1" style="display:block">

`BLDCDriver3PWM` class is instantiated by providing:
- pwm pins for phases `A`, `B` and `C` 
- `enable` pin number *(optional)*

For example:
```cpp
#include <SimpleFOC.h>

//  BLDCDriver3PWM( pin_pwmA, pin_pwmB, pin_pwmC, enable (optional))
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// instantiate sensor 

void setup() {  

  // init sensor

  // pwm frequency to be used [Hz]
  driver.pwm_frequency = 20000;
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = 12;
  // driver init
  driver.init();

}

void loop() {

}
```
</div>

<div class="driver driver-1d" markdown="1" style="display:none">

`StepperDriver4PWM` class instantiated by providing:
- pwm pins of the phase `1`: `1A`, `1B`
- pwm pins of the phase `2`: `2A`, `2B`
- per phase enable pins *(optional)*: `EN1` and `EN2`

For example:
```cpp
#include <SimpleFOC.h>

// Stepper driver instance
StepperDriver4PWM driver = StepperDriver4PWM(5, 6, 9,10, 7, 8);

// instantiate sensor 

void setup() {
  
  // init sensor

  // pwm frequency to be used [Hz]
  driver.pwm_frequency = 20000;
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = 12;
  
  // driver init
  driver.init();

}

void loop() {

}
```

</div>


For full documentation of the setup and all configuration parameters please visit the <a href="drivers_config"> driver docs <i class="fa fa-external-link"></i></a>.


## Current Sensing (Optional)

Current sensing enables true field-oriented control (FOC) with closed-loop current feedback. You can skip this if your setup doesn't include current sense hardware.

The library supports two current sense architectures: 


<a href="javascript:show('0cs','cs');" id="btn-0cs" class="btn-cs btn btn-primary">In-line current sensing</a> 
<a href ="javascript:show('1cs','cs');" id="btn-1cs" class="btn-cs btn">Low side current sensing</a>


<div  class="cs cs-0cs" markdown="1" style="display:block">

`InlineCurrentSense` class is instantiated by providing:
- shunt resistor value `shunt_resistance`
- amplifier gain `gain`
- analog pin numbers for phases `A`, `B` (and optionally `C`) 

For example:
```cpp
#include <SimpleFOC.h>

// instantiate driver
// instantiate sensor

//  InlineCurrentSense(shunt_resistance, gain, adc_a, adc_b)
InlineCurrentSense current_sense = InlineCurrentSense(0.01, 50, A0, A2);


void setup() {  

  // init sensor

  // init driver

  // link the driver with the current sense
  current_sense.linkDriver(&driver);
  // init current sense
  current_sense.init();

}

void loop() {

}
```
</div>


<div class="cs cs-1cs" markdown="1" style="display:none">

`LowsideCurrentSense` class is instantiated by providing:
- shunt resistor value `shunt_resistance`
- amplifier gain `gain`
- analog pin numbers for phases `A`, `B` (and optionally `C`) 

For example:
```cpp
#include <SimpleFOC.h>

// instantiate driver
// instantiate sensor

//  LowsideCurrentSense(shunt_resistance, gain, adc_a, adc_b, adc_c)
LowsideCurrentSense current_sense = LowsideCurrentSense(0.01, 50, A0, A1, A2);


void setup() {  

  // init sensor

  // init driver

  // link the driver with the current sense
  current_sense.linkDriver(&driver);
  // init current sense
  current_sense.init();

}

void loop() {

}
```
</div>

For full documentation of the setup and all configuration parameters please visit the <a href="current_sense"> current sense docs <i class="fa fa-external-link"></i></a>.


## Motor Instance

Create and configure your motor instance. Link it with the sensor and driver you've already initialized.

The library supports three motor types:


<a href="javascript:show('0m','motor');" id="btn-0m" class="btn-motor btn btn-primary">BLDC motor</a> 
<a href ="javascript:show('1m','motor');" id="btn-1m" class="btn-motor btn">Stepper motor</a>
<a href ="javascript:show('2m','motor');" id="btn-2m" class="btn-motor btn">HybridStepper motor</a>


<div class="motor motor-0m" markdown="1" style="display:block">

In this example we will use BLDC motor:
```cpp
#include <SimpleFOC.h>

//  BLDCMotor( pole_pairs , ( phase_resistance, KV_rating  optional) )
BLDCMotor motor = BLDCMotor(11, 9.75);
 
// instantiate driver
// instantiate sensor 
// instantiate current sensor   

void setup() {  
  // init sensor
  // link the motor to the sensor
  motor.linkSensor(&sensor);

  // init driver
  // link the motor to the driver
  motor.linkDriver(&driver);
  // link driver and the current sense
  
  // link the motor to current sense
  motor.linkCurrentSense(&current_sense);

  // set control loop type to be used
  motor.controller = MotionControlType::velocity;
  // initialize motor
  motor.init();

  // init current sense
  

}

void loop() {

}
```
</div>

<div class="motor motor-1m" markdown="1" style="display:none">

In this example we will use Stepper motor:
```cpp
#include <SimpleFOC.h>

//  StepperMotor( int pole_pairs , (phase_resistance, KV_rating optional))
StepperMotor motor = StepperMotor(50);
 
// instantiate driver
// instantiate sensor 
// instantiate current sensor   

void setup() {  
  // init sensor
  // link the motor to the sensor
  motor.linkSensor(&sensor);

  // init driver
  // link the motor to the driver
  motor.linkDriver(&driver);
  // link driver and the current sense
  
  // link the motor to current sense
  motor.linkCurrentSense(&current_sese);

  // set control loop type to be used
  motor.controller = MotionControlType::velocity;
  // initialize motor
  motor.init();
  
  // init current sense

}

void loop() {

}
```
</div>


<div class="motor motor-2m" markdown="1" style="display:none">

In this example we will use HybridStepper motor (Stepper motor coupled with a BLDC driver):
```cpp
#include <SimpleFOC.h>

//  HybridStepperMotor( int pole_pairs , (phase_resistance, KV_rating optional))
HybridStepperMotor motor = HybridStepperMotor(50);

// instantiate driver
// instantiate sensor 
// instantiate current sensor   

void setup() {  
  // init sensor
  // link the motor to the sensor
  motor.linkSensor(&sensor);

  // init driver
  // link the motor to the driver
  motor.linkDriver(&driver);
  // link driver and the current sense
  
  // link the motor to current sense
  motor.linkCurrentSense(&current_sese);

  // set control loop type to be used
  motor.controller = MotionControlType::velocity;
  // initialize motor
  motor.init();

  // init current sense

}

void loop() {

}
```
</div>

After the instance of the motor `motor` has been created we need to link the motor with the sensor `motor.linkSensor()` and link the motor class to the driver it is connected to `motor.linkDriver()`.  <br>
The next step is the configuration step, for the sake of this example we will configure only the motion control loop we will be using:
```cpp
// set control loop type to be used
motor.controller = MotionControlType::velocity;
```
And to finish the `motor` setup we run the `motor.init()` function.

For full documentation of the setup and all configuration parameters please visit the <a href="motors_config"> motor docs <i class="fa fa-external-link"></i></a>.


## FOC Initialization & Motion Control Loop

Align the motor/sensor and run the real-time control loop. This is where the FOC algorithm executes.
- `motor.loopFOC()`:  FOC algorithm execution - should be executed as fast as possible `> 1kHz`
- `motor.move(target)`: motion control routine - depends on the `motor.controller` parameter

Here is how it looks in code:

```cpp
#include <SimpleFOC.h>

// instantiate motor
// instantiate driver
// instantiate sensor 
// instantiate current sensor   

void setup() {  
  
  // init sensor
  // link motor and sensor

  // init driver
  // link motor and driver
  // link driver and the current sense

  // link motor and current sense

  // configure motor
  // init motor

  // init current sense

  // align encoder and start FOC
  motor.initFOC();
}

void loop() {
  // FOC algorithm function
  motor.loopFOC();

  // velocity control loop function
  // setting the target velocity to 2rad/s
  motor.move(2);
}
```

For full documentation of the setup and all configuration parameters for BLDC motors please visit the <a href="bldcmotor"> BLDCMotor docs  <i class="fa fa-external-link"></i></a>, and for Stepper motors please visit the <a href="steppermotor"> StepperMotor docs  <i class="fa fa-external-link"></i></a>


---

# Optional Features

## Monitoring

`BLDCMotor` and `StepperMotor` classes provide monitoring functionality. For enabling the monitoring feature make sure you call `motor.useMonitoring()` with the `Serial` port instance you want to output to. It uses `Serial` class to output motor initialization status during the `motor.init()` function, as well as in `motor.initFOC()` function.

If you are interested in outputting motors state variables in real-time (even though it will impact the performance - writing the Serial port is slow!) add the `motor.monitor()` function call to the Arduino `loop()` function. 

```cpp
#include <SimpleFOC.h>

// instantiate motor
// instantiate driver
// instantiate senor

void setup() {
  
  // init the serial port
  Serial.begin(115200);

  // init sensor
  // link motor and sensor

  // init driver
  // link motor and driver
  // link driver and the current sense


  // init current sense
  // link motor and current sense

  // use monitoring with the BLDCMotor
  Serial.begin(115200);
  // monitoring port
  motor.useMonitoring(Serial);
  
  // configure motor
  // init motor

  // init current sense
  
  // align encoder and start FOC
}

void loop() {
  
  // FOC execution
  // motion control loop

  // monitoring function outputting motor variables to the serial terminal 
  motor.monitor();
}
```
For more docs on the `BLDCMotor` and `StepperMotor` monitoring see the <a href="monitoring"> Monitoring docs</a>.


## Debugging Output


<span class="simple">Simple<span class="foc">FOC</span>library</span> provides an informative debugging interface that can be enabled by calling `SimpleFOCDebug::enable(&Serial)` function. This function enables the debugging output of the library to the `Serial` port. 
This debugging interface will output a more detailed information about:
- driver initialization (during the `driver.init()` function)
- current sense initialization (during the `current_sense.init()` function)
- motor initialization (during the `motor.init()` function)
- motor FOC initialization (during the `motor.initFOC()` function)

The debugging output will provide more information about the state of the motor, driver and current sense during and after the initialization process and will help you to debug your setup. 
It will also provide MCU architecture specific information such as which Timers and channels are used for PWM generation, which ADC is used for current sensing, did the TIME-ADC synchronisation work, etc.

<blockquote class="info"> 
📢 We strongly advise to use the debugging mode when starting with the <span class="simple">Simple<span class="foc">FOC</span>library</span>. 
It provides much more information than the standard monitoring output and can help troubleshooting potentially problems, even MCU architecture specific ones.
</blockquote>

<blockquote class="warning">  
<p class="heading"> Memory usage </p>
Debugging outputs are strings which can take a considerable amount of memory space, so it's not recommended to use it in the final application.
</blockquote>

Debugging output is disabled by default and can be enabled by calling the `SimpleFOCDebug::enable(&Serial)` function before any of the `driver`, `sensor`, `current_sense` or `motor` initalisation (`init` calls). Preferably put the `SimpleFOCDebug::enable(&Serial)` function call at the beginning of the `setup()` function.

```cpp
#include <SimpleFOC.h>

// instantiate motor
// instantiate driver
// instantiate senor

void setup() {
  
  // init the serial port
  // enable the debugging output
  SimpleFOCDebug::enable(&Serial);

  // init sensor
  // link motor and sensor

  // init driver
  // link motor and driver
  // link driver and the current sense


  // init current sense
  // link motor and current sense

  // enable monitoring
  
  // configure motor
  // init motor

  // init current sense
  
  // align encoder and start FOC
}

void loop() {
  
  // FOC execution
  // motion control loop
  // monitor variables
}
```
For more docs on the debugging capabilities of the <span class="simple">Simple<span class="foc">FOC</span>library</span> see the <a href="debugging"> Debugging docs</a>.


## Commander Interface

Finally, in order to configure the control algorithm, set the target values and get the state variables in the user-friendly way, (not just dumping as when using `motor.monitor()`) <span class="simple">Simple<span class="foc">FOC</span>library</span> provides you with a g-code like communication interface in form of the `Commander` class. 



<a href="javascript:show('0c','commander');" id="btn-0c" class="btn-commander btn btn-primary">Full motor commander</a> 
<a href ="javascript:show('1c','commander');" id="btn-1c" class="btn-commander btn">Only motor target value</a>
<a href ="javascript:show('2c','commander');" id="btn-2c" class="btn-commander btn">Motion control target + Led control</a>


<div class="commander commander-0c" markdown="1" style="display:block">

The following code is one basic implementations of the full communication interface with the user:

```cpp
#include <SimpleFOC.h>

// instantiate motor
// instantiate senor

//instantiate commander
Commander commander = Commander(Serial);
void doMotor(char* cmd){commander.motor(&motor, cmd);}

void setup() {  
  
  // init the serial port
  // enable the debugging output

  // init sensor
  // link motor and sensor

  // init driver
  // link motor and driver
  // link driver and the current sense


  // init current sense
  // link motor and current sense
  
  // enable monitoring
  
  // subscribe motor to the commands
  commander.add('M',doMotor,"motor");

  // init motor

  // init current sense
  
  // align encoder and start FOC
}

void loop() {
  
  // FOC execution
  // motion control loop
  // monitor variables

  // read user commands
  commander.run();
}
```
</div>

<div class="commander commander-1c" markdown="1" style="display:none">

The following code is one basic implementation of using commander to set the motor's target value:

```cpp
#include <SimpleFOC.h>

// instantiate motor
// instantiate senor

//instantiate commander
Commander commander = Commander(Serial);
void doTarget(char* cmd){commander.scalar(&motor.target, cmd);}

void setup() {  
  
  // init the serial port
  // enable the debugging output

  // init sensor
  // link motor and sensor

  // init driver
  // link motor and driver
  // link driver and the current sense


  // init current sense
  // link motor and current sense
  
  // enable monitoring
  
  // subscribe motor to the commands
  commander.add('T',doTarget,"target");

  // init motor

  // init current sense
  
  // align encoder and start FOC
}

void loop() {
  
  // FOC execution
  // motion control loop
  // monitor variables

  // read user commands
  commander.run();
}
```
</div>

<div class="commander commander-2c" markdown="1" style="display:none">

The following code is one basic example of using the commander interface for motion control in combination with turning a led light on and off.  

```cpp
#include <SimpleFOC.h>

// instantiate motor
// instantiate senor

//instantiate commander
Commander commander = Commander(Serial);
void doMotion(char* cmd){commander.motion(&motor, cmd);}
void doLed(char* cmd){
  if(cmd == '0')
    digitalWrite(13,LOW);
  else
    digitalWrite(13,HIGH);
}

void setup() {  
  
  // init the serial port
  // enable the debugging output

  // init sensor
  // link motor and sensor

  // init driver
  // link motor and driver
  // link driver and the current sense


  // init current sense
  // link motor and current sense
  
  // enable monitoring
  
  // subscribe motor to the commands
  commander.add('M',doMotion,"motion control");
  pinMode(13,OUTPUT);
  commander.add('L',doLed,"led control");

  // init motor

  // init current sense
  
  // align encoder and start FOC
}

void loop() {
  
  // FOC execution
  // motion control loop
  // monitor variables

  // read user commands
  commander.run();
}
```
</div>



For full documentation of the setup and all configuration parameters please visit the <a href="commander_interface"> Communication docs</a>. 

---

# Next Steps & Resources

## Hands-on Learning

Ready to test your setup? Work through the complete step-by-step guide with testing procedures, troubleshooting, and hardware validation:

[📖 Getting Started: Step-by-Step Walkthrough](example_from_scratch){: .btn .btn-docs}

## Reference & Configuration Guide

Once your motor is running, you can optimize and extend it:

| Feature | Purpose | Documentation |
|---------|---------|---|
| **Sensors** | Support for encoders, magnetic sensors, hall sensors, and generic sensors | [Position Sensors](sensors) |
| **Drivers** | Support for various BLDC and stepper drivers | [Motor Drivers](drivers_config) |
| **Current Sensing** | In-line and low-side current sensing options | [Current Sensing](current_sense) |
| **Motors** | Support for BLDC, stepper, and hybrid stepper motors | [Motors](motors_config) |
| **Motion Controllers** | Change between velocity, angle, torque modes | [Motion Control](motion_control) |
| **Torque/FOC Controllers** | Change between voltage and current modes | [Torque/FOC Control](torque_control) |
| **Communication** | Real-time parameter adjustment via serial | [Commander Interface](commander_interface) |
| **Debugging** | Detailed initialization diagnostics | [Debugging](debugging) |
| **Monitoring** | Output real-time state variables | [Monitoring](monitoring) |

---

## Library Source Code

If you are interested in extending and adapting the <span class="simple">Simple<span class="foc">FOC</span>library</span> source code you can find full documentation in the <a href="source_code">library source docs</a>
