---
layout: default
title: Sensor support
parent: Library Source
grand_parent: Digging deeper
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
nav_order: 3
permalink: /sensor_support
toc: true
---



# Generic position sensor definition
This class is purposefully kept simple, as a base for all kinds of sensors. Currently we have Encoders, Magnetic Encoders and Hall Sensor implementations. This base class extracts the
most basic common features so that a FOC driver can obtain the data it needs for operation.

To implement your own sensors, create a sub-class of this class, and implement the `getSensorAngle()` method. `getSensorAngle()` returns a float value, in **radians**, representing the current shaft angle in the
range `0` to `2*PI` (one full turn). 

To function correctly, the sensor class `update()` method has to be called sufficiently quickly. Normally, the `BLDCMotor`'s `loopFOC()` function calls it once per iteration, so you must ensure to call `loopFOC()` quickly enough, both for correct motor and sensor operation.

The `Sensor` base class provides an implementation of `getVelocity()`, and takes care of counting full revolutions in a precise way, but if you wish you can additionally override these methods to provide more
optimal implementations for your hardware.

```cpp
class Sensor{
    public:
        /**
         * Get mechanical shaft angle in the range 0 to 2PI. This value will be as precise as possible with
         * the hardware. Base implementation uses the values returned by update() so that 
         * the same values are returned until update() is called again.
         */
        virtual float getMechanicalAngle();

        /**
         * Get current position (in rad) including full rotations and shaft angle.
         * Base implementation uses the values returned by update() so that the same
         * values are returned until update() is called again.
         * Note that this value has limited precision as the number of rotations increases,
         * because the limited precision of float can't capture the large angle of the full 
         * rotations and the small angle of the shaft angle at the same time.
         */
        virtual float getAngle();
        
        /** 
         * On architectures supporting it, this will return a double precision position value,
         * which should have improved precision for large position values.
         * Base implementation uses the values returned by update() so that the same
         * values are returned until update() is called again.
         */
        virtual double getPreciseAngle();

        /** 
         * Get current angular velocity (rad/s)
         * Can be overridden in subclasses. Base implementation uses the values 
         * returned by update() so that it only makes sense to call this if update()
         * has been called in the meantime.
         */
        virtual float getVelocity();

        /**
         * Get the number of full rotations
         * Base implementation uses the values returned by update() so that the same
         * values are returned until update() is called again. 
         */
        virtual int32_t getFullRotations();

        /**
         * Updates the sensor values by reading the hardware sensor.
         * Some implementations may work with interrupts, and not need this.
         * The base implementation calls getSensorAngle(), and updates internal
         * fields for angle, timestamp and full rotations.
         * This method must be called frequently enough to guarantee that full
         * rotations are not "missed" due to infrequent polling.
         * Override in subclasses if alternative behaviours are required for your
         * sensor hardware.
         */
        virtual void update();

        /** 
         * returns 0 if it does need search for absolute zero
         * 0 - magnetic sensor (& encoder with index which is found)
         * 1 - encoder with index (with index not found yet)
         */
        virtual int needsSearch();
    protected:
        /** 
         * Get current shaft angle from the sensor hardware, and 
         * return it as a float in radians, in the range 0 to 2PI.
         * 
         * This method is pure virtual and must be implemented in subclasses.
         * Calling this method directly does not update the base-class internal fields.
         * Use update() when calling from outside code.
         */
        virtual float getSensorAngle()=0;
        /**
         * Call Sensor::init() from your sensor subclass's init method if you want smoother startup
         * The base class init() method calls getSensorAngle() several times to initialize the internal fields
         * to current values, ensuring there is no discontinuity ("jump from zero") during the first calls
         * to sensor.getAngle() and sensor.getVelocity()
         */
        virtual void init();
};

```
# Supporting additional sensors [v2.2](https://github.com/simplefoc/Arduino-FOC/releases)
In order to be able to use a new type of sensor with the FOC algorithm implemented with Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>, it needs to be extend the `Sensor` class. In order to do so, in the simplest case, one needs to implement just one function.
## Step 1. Header file `MySensor.h`
Lets make a mockup example of the new sensor implementation. We start with the `MySensor.h ` file: 

```cpp
#include <SimpleFOC.h>

class MySensor: public Sensor{
 public:
    MySensor(...);

    // initialize the sensor hardware
    void init();

    // Get current shaft angle from the sensor hardware, and 
    // return it as a float in radians, in the range 0 to 2PI.
    //  - This method is pure virtual and must be implemented in subclasses.
    //    Calling this method directly does not update the base-class internal fields.
    //    Use update() when calling from outside code.
    float getSensorAngle();
};
```

## Step 2. Class implementation file `MySensor.cpp`
Now let's implement the `MySensor.cpp` file:
```cpp
#include "MySensor.h"

MySensor::MySensor(...){
    // define all the necessary sensor variables
    // or leave empty if not necessary
}
MySensor::init(){
    // setup all the needed sensor hardware 
    // for example
    sensor.hardwareInit();
}

MySensor::getSensorAngle(){
    // Get current shaft angle from the sensor hardware, and 
    // return it as a float in radians, in the range 0 to 2PI.
    return sensor.read() ;
}
```

## Step 3. Arduino program
Finally we will be able to use it in Arduino code:
```cpp
#include <SimpleFOC.h>
#include "MySensor.h"

// instantiate the MySensor
MySensor my_sensor = MySensor(...);

// instantiate the motor
BLDCMotor motor = BLDCMotor(...)

// driver
BLDCDriver3PWM driver = BLDCDriver3PWM(...)

void setup(){
    // init MySensor position tracking
    my_sensor.init();

    // link MySensor with the motor
    motor.linkSensor(&my_sensor);

    // driver config
    driver.init();
    motor.linkDriver(&driver);

    // get ready for FOC
    motor.init();
    motor.initFOC();
}
void loop(){
    // do FOC
    motor.loopFOC();
    motor.move(target);
}

```

## (Optional) Arduino program - stand-alone sensor
Finally we will be able to use it in Arduino code:
```cpp
#include <SimpleFOC.h>
#include "MySensor.h"

// instantiate the MySensor
MySensor my_sensor = MySensor(...);

void setup(){
    // init MySensor position tracking
    my_sensor.init();
}
void loop(){
    // update the sensor states - IMPORTANT
    my_sensor.update();
    // display the angle and velocity
    Serial.print(my_sensor.getAngle());
    Serial.print("\t")
    Serial.print(my_sensor.getVelocity());
}

```




