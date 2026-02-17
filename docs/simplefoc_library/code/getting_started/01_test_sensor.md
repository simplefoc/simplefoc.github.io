---
layout: default
title: Test Your Sensor
nav_order: 1
description: "Test and validate your position sensor setup"
permalink: /test_sensor
parent: Getting Started Guide
grand_parent: Writing the Code
great_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---

# Test Your Sensor

⏱️ **~10 minutes**

The first sign that everything is well connected is if the sensor readings are good. This quick test validates that your position sensor is working correctly.

## Select Your Sensor Type

<a href="javascript:show('enc','sensor');" class="btn btn-sensor btn-enc btn-primary">Encoder</a> 
<a href ="javascript:show('mspi','sensor');"  class="btn btn-sensor btn-mspi">Magnetic Sensor SPI</a>
<a href ="javascript:show('mi2c','sensor');"  class="btn btn-sensor btn-mi2c">Magnetic Sensor I2C</a>
<a href ="javascript:show('manalog','sensor');"  class="btn btn-sensor btn-manalog">Magnetic Sensor Analog</a>
<a href ="javascript:show('hall','sensor');"  class="btn btn-sensor btn-hall">Hall Sensors</a>


<div class="sensor sensor-manalog hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// MagneticSensorAnalog(uint8_t _pinAnalog, int _min, int _max)
// - pinAnalog      - the pin that is reading the pwm from magnetic sensor
// - min_raw_count  - the smallest expected reading. 
// - max_raw_count  - the largest value read. 
MagneticSensorAnalog sensor = MagneticSensorAnalog(A1, 14, 1020);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // initialise magnetic sensor hardware
  sensor.init();

  Serial.println("Sensor ready");
  _delay(1000);
}

void loop() {
  // iterative function updating the sensor internal variables
  // it is usually called in motor.loopFOC()
  // this function reads the sensor hardware and 
  // has to be called before getAngle nad getVelocity
  sensor.update();
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```

</div>


<div class="sensor sensor-mi2c hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// Example of AS5600 configuration 
MagneticSensorI2C sensor = MagneticSensorI2C(AS5600_I2C);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // configure i2C
  Wire.setClock(400000);
  // initialise magnetic sensor hardware
  sensor.init();

  Serial.println("Sensor ready");
  _delay(1000);
}

void loop() {
  // iterative function updating the sensor internal variables
  // it is usually called in motor.loopFOC()
  // this function reads the sensor hardware and 
  // has to be called before getAngle nad getVelocity
  sensor.update();
  
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```

</div>


<div class="sensor sensor-mspi  hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// MagneticSensorSPI(MagneticSensorSPIConfig_s config, int cs)
//  config  - SPI config
//  cs      - SPI chip select pin 
MagneticSensorSPI sensor = MagneticSensorSPI(AS5147_SPI, 10);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // initialise magnetic sensor hardware
  sensor.init();

  Serial.println("Sensor ready");
  _delay(1000);
}

void loop() {
  // iterative function updating the sensor internal variables
  // it is usually called in motor.loopFOC()
  // this function reads the sensor hardware and 
  // has to be called before getAngle nad getVelocity
  sensor.update();
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```

</div>


<div class="sensor sensor-hall  hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// Hall sensor instance
// HallSensor(int hallA, int hallB , int cpr, int index)
//  - hallA, hallB, hallC    - HallSensor A, B and C pins
//  - pp                     - pole pairs
HallSensor sensor = HallSensor(2, 3, 4, 14);

void setup() {
  // monitoring port
  Serial.begin(115200);
  
  // initialise sensor hardware
  sensor.init();

  Serial.println("Sensor ready");
  _delay(1000);
}

void loop() {
  // iterative function updating the sensor internal variables
  // it is usually called in motor.loopFOC()
  sensor.update();
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
  delay(100);
}
```

</div>


<div class="sensor sensor-enc"  markdown="1">

```cpp
#include <SimpleFOC.h>

Encoder encoder = Encoder(2, 3, 500);
// interrupt routine initialisation
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

void setup() {
  // monitoring port
  Serial.begin(115200);
  
  // initialise encoder hardware
  encoder.init();
  // hardware interrupt enable
  encoder.enableInterrupts(doA, doB);

  Serial.println("Encoder ready");
  _delay(1000);
}

void loop() {
  // IMPORTANT
  // read sensor and update the internal variables
  encoder.update();
  // display the angle and the angular velocity to the terminal
  Serial.print(encoder.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```

</div>

## Setup Steps

1. **Copy the code** for your sensor type above
2. **Update parameters** to match your setup:
   - Pin numbers (e.g., `A1`, `2`, `3`, `10`)
   - Sensor-specific parameters (impulses per revolution, I2C address, etc.)
   - See [Position Sensors](sensors) docs for details
3. **Upload** to your microcontroller
4. **Open Serial Monitor** (115200 baud)

## Verify It Works

<blockquote class="info" markdown="1"> <p class="heading">✓ What to expect</p> 
You should see continuous output of angle and velocity values in the serial terminal.
- **One rotation** of the motor should give angle output of **6.28** ($$2\pi$$ radians)
- **Velocity** should increase when you rotate faster
</blockquote>

### Troubleshooting

**Velocity output is noisy/jumpy?**
- This usually means velocity is calculated too often
- Try increasing `sensor.min_elapsed_time` to 500-1000 microseconds
- Example: `sensor.min_elapsed_time = 0.0005;`

**No output or wrong values?**
- Check your pin numbers match the hardware wiring
- Verify sensor power and ground connections
- Check I2C/SPI bus configuration (clock speeds, pull-ups)
- See [Position Sensors documentation](sensors) for detailed configuration

**Units confusion?**
- SimpleFOC uses **radians** where 6.28 rad = 1 full rotation
- Learn more: [Library Units](library_units)

## Next Step

Once your sensor is reading correctly, proceed to

[Test Your Driver](test_driver){: .btn .btn-step}
