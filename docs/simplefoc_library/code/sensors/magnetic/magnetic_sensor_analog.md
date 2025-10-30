---
layout: default
title: Magnetic sensor Analog
parent: Magnetic sensor
grand_parent: Position Sensors
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 3
permalink: /magnetic_sensor_analog
toc: true
---



# Analog output Magnetic sensor setup


## Step 1. Instantiate `MagneticSensorAnalog` class

In order to use your Analog output magnetic position sensor with <span class="simple">Simple<span class="foc">FOC</span>library</span> first create an instance of the `MagneticSensorAnalog` class:
```cpp
// MagneticSensorAnalog(uint8_t _pinAnalog, int _min, int _max)
//  pinAnalog     - the pin that is reading the analog output from magnetic sensor
//  min_raw_count - the smallest expected reading.  
//  max_raw_count - the largest value read.  
MagneticSensorAnalog sensor = MagneticSensorAnalog(A1, 14, 1020);
```

The parameters of the class are
- `pinAnalog` - the pin that is reading the analog output from magnetic sensor , 
- `min_raw_count` - the smallest expected reading. Whilst you might expect it to be 0 it is often ~15.  Getting this wrong results in a small click once per revolution
- `max_raw_count` - the largest value read. Whilst you might expect it to be 2^10 = 1023 it is often ~ 1020. Note: For ESP32 (with 12bit ADC the value will be nearer 4096)

<blockquote class="info"> <p class="heading"> 💡 Find out min and max</p>
Every mcu is a bit different and every sensor as well so we advise you to use the provided example in the <code class="highlighter-rouge">examples/sensor_test/magnetic_sensor_analog_example/find_raw_min_max</code> to find out the maximal and minimal values of your sensor.
</blockquote>
Finally after the initialization the only thing you need to do afterwards is to call the `init()` function. This function initializes the sensor hardware. So your magnetic sensor initialization code will look like:
```cpp
MagneticSensorAnalog sensor = MagneticSensorAnalog(A1, 14, 1020);

void setup(){
  ...
  sensor.init();
  ...
}
```

If you wish to use more than one magnetic sensor, make sure you connect their ADC pins to different arduino pins and follow the same idea as above, here is a simple example:
```cpp
MagneticSensorAnalog sensor1 = MagneticSensorAnalog(A1, 14, 1020);
MagneticSensorAnalog sensor2 = MagneticSensorAnalog(A2, 14, 1020);

void setup(){
  ...
  sensor1.init();
  sensor2.init();
  ...
}
```

Please check the `magnetic_sensor_analog_example.ino` example to see more about it.


## Step 2. Using magnetic sensor in real-time

There are two ways to use magnetic sensor implemented within this library:
- As a motor position sensor for FOC algorithm
- As a standalone position sensor

### Position sensor for FOC algorithm

To use the ensor with the FOC algorithm implemented in this library, once when you have initialized `sensor.init()` you just need to link it to the BLDC motor by executing:
```cpp
motor.linkSensor(&sensor);
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

#### Example code

Here is a quick example for analog output magnetic sensor with a BLDC motor and driver:

```cpp
#include <SimpleFOC.h>

// motor and driver
BLDCMotor motor = BLDCMotor(7);
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// MagneticSensorAnalog(uint8_t _pinAnalog, int _min, int _max)
//  pinAnalog     - the pin that is reading the analog output from magnetic sensor
//  min_raw_count - the smallest expected reading.  
//  max_raw_count - the largest value read.  
MagneticSensorAnalog sensor = MagneticSensorAnalog(A1, 14, 1020);

void setup() {
  // driver
  driver.init()
  motor.linkDriver(&driver);

  // init magnetic sensor hardware
  sensor.init();
  motor.linkSensor(&sensor);

  // init motor hardware
  motor.init();
  motor.initFOC();

  Serial.println("Motor ready");
  _delay(1000);
}
void loop(){
  motor.loopFOC();
  motor.move();
}
```

### Standalone sensor 

To get the magnetic sensor angle and velocity at any given time you can use the public methods:
```cpp
class MagneticSensorAnalog{
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

#### Example code

Here is a quick example for AS5600 magnetic sensor using it's analog output:
```cpp
#include <SimpleFOC.h>

// MagneticSensorAnalog(uint8_t _pinAnalog, int _min, int _max)
//  pinAnalog     - the pin that is reading the analog output from magnetic sensor
//  min_raw_count - the smallest expected reading.  
//  max_raw_count - the largest value read.  
MagneticSensorAnalog sensor = MagneticSensorAnalog(A1, 14, 1020);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // initialize magnetic sensor hardware
  sensor.init();

  Serial.println("Sensor ready");
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