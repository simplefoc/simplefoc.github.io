---
layout: default
title: Generic sensor
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /generic_sensor
nav_order: 4
parent: Position Sensors
grand_parent: Writing the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---



# Implementing a custom sensor

`GenericSensor` is a new class of the  <span class="simple">Simple<span class="foc">FOC</span>library</span> that simplifies the implementation of new sensors. With this class you are able to add a custom sensor to your code and link it to the motor in one arduino file.


## Step 1. Implement the function reading your sensor
Basically all that you need to do in your arduino code is implement a function that reads your sensor and returns an angle in radians between 0 and 2π:
```cpp
float readMySensorCallback(){
 // read my sensor
 // return the angle value in radians in between 0 and 2PI
 return ...;
}
```

additionally you can optionally implement a function that initializes your sensor 
```cpp
void initMySensorCallback(){
  // do the init
}
```

## Step 2. Instantiate `GenericSensor` class
To initialize the sensor class you need to provide it the pointer to your function reading the sensor, and optionally the pointer to the function that initializes your sensor. 
```cpp
// GenericSensor class constructor
//  - readCallback pointer to the function reading the sensor angle
//  - initCallback pointer to the function initializing the sensor (optional)
GenericSensor sensor = GenericSensor(readMySensorCallback, initMySensorCallback);
```

And you will be able to access the angle and velocity of the motor using the motor instance:
```cpp
motor.shaft_angle; // motor angle
motor.shaft_velocity; // motor velocity
```

or through the sensor instance:
```cpp
sensor.getAngle(); // motor angle
sensor.getVelocity(); // motor velocity
```


## Step 3. Using your sensor in real-time

There are two ways to use sensors implemented within this library:
- As motor position sensor for FOC algorithm
- As standalone position sensor

### Standalone sensor 
You can use your sensor as a standalone sensor. To get the sensor angle and velocity at any given time you can use the public methods:
```cpp
class GenericSensor{
 public:
    // shaft velocity getter
    float getVelocity();
	  // shaft angle getter
    float getAngle();
}
```

<blockquote markdown="1" class="info">
<p class="heading" markdown="1">Calling `getVelocity` multiple times</p>
When calling `getVelocity` it will only calculate the velocity if the elapsed time from the previous call is longer than the time specified in teh variable `min_elapsed_time` (default 100us). If the elapsed time from the last call is shorter than `min_elapsed_time` the function will return previously calculated value. Variable `min_elapsed_time` can be changed easily if necessary:

```cpp
sensor.min_elapsed_time = 0.0001; // 100us by default
```
</blockquote>

Here is a quick example:
```cpp
#include <SimpleFOC.h>

float readMySensorCallback(){
 // read my sensor
 // return the angle value in radians in between 0 and 2PI
 return ...;
}

void initMySensorCallback(){
  // do the init
}

// create the sensor
GenericSensor sensor = GenericSensor(readMySensorCallback, initMySensorCallback);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // initialize sensor hardware
  sensor.init();

  Serial.println("My sensor ready");
  _delay(1000);
}

void loop() {
  // IMPORTANT - call as frequently as possible
  // update the sensor values 
  sensor.update();
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```

### Position sensor for FOC algorithm

To use the sensor with the foc algorithm implemented in this library, once when you have initialized `sensor.init()`, you just have to link it to the BLDC motor by executing:
```cpp
motor.linkSensor(&sensor);
```

So in general your code will look something like this:
```cpp
#include <SimpleFOC.h>

float readMySensorCallback(){
 // read my sensor
 // return the angle value in radians in between 0 and 2PI
 return ...;
}

void initMySensorCallback(){
  // do the init
}

// create the sensor
GenericSensor sensor = GenericSensor(readMySensorCallback, initMySensorCallback);

....
BLDCMotor motor = ....
...

void setup() {
   ....
  // initialize sensor hardware
  sensor.init();
  // link to the motor
  motor.linkSensor(&sensor);
  ...
  motor.initFOC();
  ...
}
void loop() {
  ....
}
```

## New sensor support full example - ESP32 hardware encoder

Here is an example code implementing a hardware counter based encoder implementation based on the ESP32 architecture that the <span class="simple">Simple<span class="foc">FOC</span>library</span> doesn't support by default. 
To setup the counters and all the hardware parameters here we use the library [ESP32Encoder](https://github.com/madhephaestus/ESP32Encoder) and the full code of the example is as follows:
```cpp
#include <SimpleFOC.h>
#include <ESP32Encoder.h>

// create the ESP32Encoder class
ESP32Encoder encoder;
// define the sensor cpr (500x4)
int64_t cpr = 2000;
// function initializing the sensor
void initMySensorCallback(){
  // use pin 25 and 26 (Arduino pins 2,3) for the encoder
  encoder.attachFullQuad(25, 26);
}
// function reading the encoder 
float readMySensorCallback(){
  // return the value in between 0 - 2PI
  float a = ((float)(encoder.getCount()%cpr)*_2PI/((float)cpr));
  return a > 0 ? a : a + _2PI;
}
// create the generic sensor
GenericSensor sensor = GenericSensor(readMySensorCallback, initMySensorCallback);

// BLDC motor & driver instance
BLDCMotor motor = BLDCMotor(11);
BLDCDriver3PWM driver = BLDCDriver3PWM(16, 27, 5, 12); // (Arduino pins 5,6,10,8)


// commander communication instance
Commander command = Commander(Serial);
void doMotor(char* cmd){ command.motor(&motor, cmd); }

void setup() {

  // initialize sensor hardware
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

  // use monitoring with serial for motor init
  // monitoring port
  Serial.begin(115200);
  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  motor.init();
  // align encoder and start FOC
  motor.initFOC();

  // subscribe motor to the commander
  command.add('M', doMotor, "motor");

  _delay(1000);
}


void loop() {
  // iterative setting FOC phase voltage
  motor.loopFOC();

  // iterative function setting the outer loop target
  motor.move();

  // user communication
  command.run();
}
```