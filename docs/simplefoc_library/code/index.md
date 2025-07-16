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

# Getting to know the <span class="simple">Simple<span class="foc">FOC</span>library</span> code

Once you have your <span class="simple">Simple<span class="foc">FOC</span>library</span> [installed](installation) and you have all the necessary [hardware](supported_hardware), we can finally start to get familiar with the Arduino code that will run your motor. Here are all the most important steps when writing the code!

## Step 0. Include the library
Let's start by including the library header file:
```cpp
#include <SimpleFOC.h>
```

## Step 1. <a href="sensors" class="remove_dec">Position sensor setup</a>

First step when writing the code is initializing and configuring the position sensor.
The library supports these position sensors:
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


## Step 2. <a href="drivers_config" class="remove_dec"> Driver setup</a>
After setting up the position sensor we proceed to initializing and configuring the driver. The library supports [BLDC drivers](bldcdriver) handled by `BLDCDriver3PWM` and `BLDCDriver6PWM`  classes as well as [stepper drivers](stepperdriver) handled by the `StepperDriver2PWM` and `StepperDriver4PWM` classes. 


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


## Step 3. <a href="current_sense" class="remove_dec"> Current sense setup</a>
After the position sensor and the driver we can proceed to initializing and configuring the current sense, if available of course. If current sense is not available you can skip this step. The library supports two types of current sense architecture:
- in-line current sensing `InlineCurrentSense`. 
- low-side current sensing `LowsideCurrentSense`. 


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



## Step 4. <a href="motors_config" class="remove_dec"> Motor setup </a>
After the position sensor and the driver we proceed to initializing and configuring the motor. The library supports BLDC motors handled by the `BLDCMotor` class as well as stepper motors handled by the `StepperMotor` and `HybridStepperMotor` classes. Both classes are instantiated by providing just the `pole_pairs` number of the motor and optionally the motors'   phase resistance and the KV rating.


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


## Step 5. [FOC routine and real-time motion control](motion_control)
When we have initialized the position sensor, driver and the motor, and before we can run the FOC algorithm, we need to align the motor and sensor. This is done by calling `motor.initFOC()`. 
After this step we have a functional position sensor, we have configured the motor and our FOC algorithm knows how to set the appropriate voltages based on position sensor measurements.

For the real-time routine of the FOC algorithm we need to add the `motor.loopFOC()` and `motor.move(target)` functions in the Arduino `loop()`.
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


## Step 6. <a href="monitoring" class="remove_dec"> Monitoring</a>

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


## Step 7. <a href="debugging" class="remove_dec"> Debugging output</a>


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


## Step 8. <a href="communication" class="remove_dec"> Commander Interface</a>

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

## Step 9. [Getting started step by step guide](example_from_scratch)

Now that you are familiar with the structure of the <span class="simple">Simple<span class="foc">FOC</span>library</span> code you can finally start writing your own applications. In order to make this step less complicated, we have provided you a detailed step by step guide. Make sure to go through our step by step getting started guide when dealing with the library for the first time.  

## 🎨 Full Arduino code of the example 

Above, you have learned about all the parts of the Arduino program and what they are used for. Here is the full code example with some additional configuration. Please go through the code to better understand how to integrate all previously introduced parts into one. This is the code of the library example `motor_full_control_serial_examples/magnetic_sensor/full_control_serial.ino`. 

```cpp
#include <SimpleFOC.h>

// magnetic sensor instance - SPI
MagneticSensorSPI sensor = MagneticSensorSPI(AS5147_SPI, 10);

// BLDC motor & driver instance
BLDCMotor motor = BLDCMotor(11);
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// commander interface
Commander command = Commander(Serial);
void onMotor(char* cmd){ command.motor(&motor, cmd); }

void setup() {
  // monitoring port
  Serial.begin(115200);
  // enable the debugging output
  SimpleFOCDebug::enable(&Serial);

  // initialise magnetic sensor hardware
  sensor.init();
  // link the motor to the sensor
  motor.linkSensor(&sensor);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link driver
  motor.linkDriver(&driver);

  // set control loop type to be used
  motor.controller = MotionControlType::torque;

  // contoller configuration based on the control type 
  motor.PID_velocity.P = 0.2;
  motor.PID_velocity.I = 20;
  motor.PID_velocity.D = 0;
  // default voltage_power_supply
  motor.voltage_limit = 12;

  // velocity low pass filtering time constant
  motor.LPF_velocity.Tf = 0.01;

  // angle loop controller
  motor.P_angle.P = 20;
  // angle loop velocity limit
  motor.velocity_limit = 50;

  // use monitoring with serial for motor init
  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialise motor
  motor.init();
  // align encoder and start FOC
  motor.initFOC();

  // set the inital target value
  motor.target = 2;

  // define the motor id
  command.add('A', onMotor, "motor");

  // Run user commands to configure and the motor (find the full command list in docs.simplefoc.com)
  Serial.println(F("Motor commands sketch | Initial motion control > torque/voltage : target 2V."));
  
  _delay(1000);
}


void loop() {
  // iterative setting of the FOC phase voltage
  motor.loopFOC();

  // iterative function setting the outter loop target
  // velocity, position or voltage
  // if target not set in parameter uses motor.target variable
  motor.move();
  
  // user communication
  command.run();
}
```

## Library source code
If you are interested in extending and adapting the <span class="simple">Simple<span class="foc">FOC</span>library</span> source code you can find full documentation in the <a href="source_code">library source docs</a>
