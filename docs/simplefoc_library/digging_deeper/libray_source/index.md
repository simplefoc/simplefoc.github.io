---
layout: default
title: Library Source
nav_order: 1
parent: Digging deeper
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /source_code
has_children: True
has_toc: false
---

# Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> source code
The arduino library code is organized into the standard [Arduino library structure](https://github.com/arduino/Arduino/wiki/Library-Manager-FAQ). 
The library contains main BLDC motor class `BLDCMotor` and  three sensor classes `Encoder`, `MagneticSensorSPI` and `MagneticSensorI2C` implementing the `Sensor` interface. Furthermore it has `FOCutils` file with all the necessary utility functions and `defaults.h` with all the default configuration variables.

## Arduino library source structure
```sh
| src
| ├─ SimpleFOC.h               # SimpleFOC library include file
| |
| ├─ BLDCMotor.cpp/h           # BLDCMotor class implementing all the FOC operations
│ │ 
│ ├─ Sensor.h                     # Abstract Sensor class that all the sensors implement
│ ├─ Encoder.cpp/h                # Encoder class implementing the Quadrature encoder operations
│ ├─ MagneticSensorSPI.cpp/h      # class implementing SPI communication for Magnetic sensors
│ ├─ MagneticSensorI2C.cpp/h      # class implementing I2C communication for Magnetic sensors
│ ├─ MagneticSensorAnalog.cpp/h   # class implementing Analog output for Magnetic sensors
│ ├─ HallSensor.cpp/h             # class implementing Hall sensor 
| |
| ├─ FOCutils.cpp/h            # Utility functions 
| |
  └─ defaults.h                # Default configuration values 
```

<blockquote class="info">For more info visit <a href="http://source.simplefoc.com/" target="_blank"> full source code documentation <i class="fa fa-external-link fa-sm"></i></a></blockquote>

### `BLDCMotor.cpp/h`
BLDCMotor class implementation
- BLDC driver hardware configuration: `init()`, `pwmA,pwmB,pwmC,enable`
- FOC algorithm functions and configuration: `loopFOC()`, `initFOC`,`foc_modulation`
- PID controllers for the velocity and angle control: `PID_s`, `controllerPID()`,`velocityPID()`,`positionP`
- Monitoring functions: `useMonitoring()`,`monitor()`,`monitor_port`
- Communication interface with user in form of motor commands: `command()`  

<blockquote class="info"><a href="foc_implementation"><i class="fa fa-copy"></i> FOC implementation details</a> - Documentation of the procedures and detailed explanations of the code implementing FOC algorithm 
</blockquote>
<blockquote class="info">
     <a href="motion_control_implementation"><i class="fa fa-copy"></i> Motion control implementation details</a> - Documentation of the motion control algorithms and code implementation choices
</blockquote><blockquote class="info">
     <a href="commands_source"><i class="fa fa-copy"></i> Motor commands implementation </a> - Documentation of the motor commands functionality
</blockquote>


### `Sensor.h`
Abstract sensor class that every sensor needs to implement in order to be connectable to the motor (`BLDCMotor` class). 
If you want to implement your own version of the sensor, jut extend this class and implement the virtual functions and you will be able to run the FOC algorithm with it.
You will be abele to link motor and the sensor by doing `motor.linkSensor(your sensor)`
```cpp
class Sensor{
public:
    // get current angle (rad) 
    virtual float getAngle();
    // get current angular velocity (rad/s)
    virtual float getVelocity();
    // set current angle as zero angle 
    // return the angle [rad] difference
    virtual float initRelativeZero();
    // set absolute zero angle as zero angle
    // return the angle [rad] difference
    virtual float initAbsoluteZero();

    // returns 0 if it has no absolute 0 measurement
    // 0 - incremental encoder without index
    // 1 - encoder with index & magnetic sensors
    virtual int hasAbsoluteZero();
    // returns 0 if it does need search for absolute zero
    // 0 - magnetic sensor (& encoder with index which is found)
    // 1 - encoder with index (with index not found yet)
    virtual int needsAbsoluteZeroSearch();
}
```

### `Encoder.cpp/h`
Quadrature Encoder class implementation  
- Extends `Sensor` class
- Encoder interrupt functions and configuration: `enableInterrupt()`, `handleA()`, `handleB()`, `handleIndex()`...
- Calculates motor angle and velocity ( using the [Mixed Time Frequency Method](https://github.com/askuric/Arduino-Mixed-Time-Frequency-Method)). 
- Support any type of optical and magnetic encoder. 

### `MagneticSensorSPI.cpp/h` 
Absolute Magnetic sensor(encoder) class implementation
- Extends `Sensor` class
- SPI communication 
- Calculates motor angle and velocity
- Supports magnetic position sensors such as AS5048A, AS5047 and similar. 

### `MagneticSensorI2C.cpp/h`
Absolute Magnetic sensor(encoder) class implementation
- Extends `Sensor` class
- I2C communication
- Calculates motor angle and velocity
- Supports magnetic position sensors such as AS5048B, AS5600 and similar 
   
### `MagneticSensorAnalog.cpp/h`
Absolute Magnetic sensor(encoder) class implementation
- Extends `Sensor` class
- Analog value reading
- Calculates motor angle and velocity
- Supports magnetic position sensors such as AS5047, AS5600 and similar 

### `HallSensor.cpp/h`
Hall sensor class implementation  
- Extends `Sensor` class
- Hall sensor interrupt functions and configuration: `enableInterrupt()`, `handleA()`, `handleB()`, `handleC()`...
- Calculates motor angle and velocity. 
- Support any type of hall sensor - even magnetic sensor AS5047 (UVW interface). 


### `FOCutils.cpp/h`
This is implementation of all the necessary hardware specific and FOC utility functions.
```cpp
// High PWM frequency setting function
// - hardware specific
// pinA,pinB,pinC  hardware pin numbers
void _setPwmFrequency(int pinA, int pinB, int pinC);

// Function implementing delay() function in milliseconds 
// - blocking function
// - hardware specific
void _delay(unsigned long ms);

//Function implementing timestamp getting function in microseconds
// hardware specific
unsigned long _micros();

//Function setting the duty cycle to the pwm pin (ex. analogWrite())
// hardware specific
// dc_a, dc_b, dc_c - duty cycle [0, 1]
// pinA,pinB,pinC   - hardware pin number 
void _writeDutyCycle(float dc_a,  float dc_b, float dc_c, int pinA, int pinB, int pinC );

// Function approximating the sine calculation by using fixed size array
// - execution time ~40us (Arduino UNO)
float _sin(float a);
// Function approximating cosine calculation by using fixed size array
// - execution time ~50us (Arduino UNO)
float _cos(float a);
```


### `defaults.h`
Header file containing all the default configuration variables
```cpp
// default configuration values
// change this file to optimal values for your application

#define DEF_POWER_SUPPLY 12.0 //!< default power supply voltage
// velocity PI controller params
#define DEF_PID_VEL_P 0.5 //!< default PID controller P value
#define DEF_PID_VEL_I 10 //!<  default PID controller I value
#define DEF_PID_VEL_D 0 //!<  default PID controller D value
#define DEF_PID_VEL_U_RAMP 1000 //!< default PID controller voltage ramp value
// angle P params
#define DEF_P_ANGLE_P 20 //!< default P controller P value
#define DEF_VEL_LIM 20 //!< angle velocity limit default
// index search 
#define DEF_INDEX_SEARCH_TARGET_VELOCITY 1 //!< default index search velocity
// align voltage
#define DEF_VOLTAGE_SENSOR_ALIGN 6.0 //!< default voltage for sensor and motor zero alignemt
// low pass filter velocity
#define DEF_VEL_FILTER_Tf 0.005 //!< default velocity filter time constant
```

### `SimpleFOC.h`
The main library include file, its contents is:
```cpp
#include "FOCutils.h"
#include "Sensor.h"
#include "Encoder.h"
#include "MagneticSensorSPI.h"
#include "MagneticSensorI2C.h"
#include "MagneticSensorAnalog.h"
#include "HallSensor.h"
#include "BLDCMotor.h"
```


## Digging deeper

For more info about the FOC procedures and detailed explanations of the code implementing FOC algorithm please visit: <a href="foc_implementation"> FOC implementation details <i class="fa fa-external-link fa-sm"></i></a>

For documentation of the motion control algorithms and code implementation choices, visit <a href="motion_control_implementation"> Motion control implementation details <i class="fa fa-external-link fa-sm"></i></a>

The library comes with a lot of motor control examples for different microcontrollers. See more on [library examples <i class="fa fa-external-link"></i>](library_examples)

To dig deeper in the source code please visit <a href="http://source.simplefoc.com/" target="_blank"> Doxygen generated code documentation <i class="fa fa-external-link fa-sm"></i></a>

<div class="image_icon width80" >
    <a href="http://source.simplefoc.com/" target="_blank">
        <img src="extras/Images/source_docs.jpg" >
        <i class="fa fa-external-link-square fa-2x"></i>
    </a>
</div>

