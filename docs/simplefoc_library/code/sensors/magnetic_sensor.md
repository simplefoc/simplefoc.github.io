---
layout: default
title: Magnetic sensor
parent: Position Sensor
grand_parent: Using the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 2
permalink: /magnetic_sensor
---


# Magnetic sensor setup
<div class="width60">
<img src="extras/Images/mag0.jpg" style="width:32%;display:inline"><img src="extras/Images/mag.jpg" style="width:32%;display:inline"><img src="extras/Images/mag2.jpg" style="width:32%;display:inline">
</div>


Magnetic sensors implementation in this library ([version 1.4.1 <i class="fa fa-tag"></i>](https://github.com/askuric/Arduino-FOC/releases)) support communications:
- **SPI** - [MagneticSensorSPI](#spi-communication-magneticsensorspi)
- **I2C** - [MagneticSensorI2C](#i2c-communication-magneticsensori2c)
- **Analog** - [MagneticSensorAnalog](#analog-output-magneticsensoranalog)
- **ABI** - *equivalent to encoder sensors* - [encoder docs <i class="fa fa-external-link"></i>](encoder)
- **UVW** - *equivalent to hall sensors* - [hall sensors docs <i class="fa fa-external-link"></i>](hall_sensors).


## SPI communication `MagneticSensorSPI`
In order to use your SPI magnetic position sensor with <span class="simple">Simple<span class="foc">FOC</span>library</span> first create an instance of the `MagneticSensorSPI` class:
```cpp
// MagneticSensorSPI(int cs, float _cpr, int _angle_register)
//  cs              - SPI chip select pin 
//  bit_resolution - magnetic sensor resolution
//  angle_register  - (optional) angle read register - default 0x3FFF
MagneticSensorSPI sensor = MagneticSensorSPI(10, 14, 0x3FFF);
```
The parameters of the class are
- `chip_select` - pin number you connected your sensor to be used with the SPI communication, 
- `bit_resolution` - resolution of your sensor (number of bits of the sensor internal counter register) and your
- `angle register` - register number containing angle value. <br>The default `angle_register` number is set to `0x3FFF` as it is the angle register for most of the low cost AS5048/AS5047 sensors. 

Finally after the initialization the only thing you need to do afterwards is to call the `init()` function. This function prepares the SPI interface and initializes the sensor hardware. So your magnetic sensor initialization code will look like:
```cpp
MagneticSensorSPI sensor = MagneticSensorSPI(10, 14, 0x3FFF);

void loop(){
  ...
  sensor.init();
  ...
}
```

If you wish to use more than one magnetic sensor, make sure you connect their `chip_select` pins to different arduino pins and follow the same idea as above, here is a simple example:
```cpp
MagneticSensorSPI sensor1 = MagneticSensorSPI(10, 14, 0x3FFF);
MagneticSensorSPI sensor1 = MagneticSensorSPI(9, 14, 0x3FFF);

void loop(){
  ...
  sensor1.init();
  sensor2.init();
  ...
}
```

Please check the `magnetic_sensor_spi_example.ino` example to see more about it.


## I2C communication `MagneticSensorI2C`
Now in order to use your I2C magnetic position sensor with <span class="simple">Simple<span class="foc">FOC</span>library</span> first create an instance of the `MagneticSensorI2C` class:
```cpp
// MagneticSensorI2C(uint8_t _chip_address, float _cpr, uint8_t _angle_register_msb)
//  chip_address         - I2C chip address
//  bit_resolution       - resolution of the sensor
//  angle_register_msb   - angle read register msb
//  bits_used_msb        - number of used bits in msb register
MagneticSensorI2C sensor = MagneticSensorI2C(0x36, 12, 0x0E, 4);
```

The parameters of the class are:
 - `chip_address` - I2C address of the magnetic sensor 
 - `bit_resolution` - resolution of your sensor (number of bits of the sensor internal counter register) and your
 - `angle_register_msb` - register number containing the MSB part of the angle value. ( ex. AS5600 - `0x0E`, AS5048 - `0xFE` ) 
 - `bits_used_msb` - number of used bits in MSB register

<blockquote class="info"> <p class="heading">How to find the MSB register values and used bits value?</p>

Since I2C registers are Byte(8-bit) registers they contain the 12-14 bit angle representation separated in two Byte addresses,  MSB(most significant byte) and LSB(least significant byte).
To instantiate the I2C magnetic sensor you need to provide the MSB register address and tell the library how is the angle value separated in the two registers. By specifying the number of bits used in the MSB register and specifying the <code class="highlighter-rouge">bit_resolution</code> value the library can calculate how many bits are used in the LSB register and can reconstruct the complete angle value.
<br>
Example MSB/LSB division:
<ul>
<li>AS5600 (12 bit) - MSB (4bit), LSB (8bit)</li>
<li>AS5048B (14bit): - MSB (8bit), LSB(6bit)</li>
</ul>
</blockquote>
<blockquote class="info">
<p class="heading">AS5600 example</p>
<ul>
  <li> First we open AS5600 sensor's <a href="https://ams.com/documents/20143/36005/AS5600_DS000365_5-00.pdf" target="_blank"> datasheet <i class="fa fa-external-link"></i></a>.</li>
  <li> In the datasheet we find the table of I2C registers (page 18)</li>
  <li> The <code class="highlighter-rouge">angle_register_msb</code> value is the first (upper in datasheet) register value - <code class="highlighter-rouge">0x0E</code></li>
  <li> The <code class="highlighter-rouge">bits_used_msb</code> value is number of bits in the MSB register - <code class="highlighter-rouge">4</code></li>
</ul>
</blockquote>

Finally after the initialization the only thing you need to do afterwards is to call the `init()` function. This function prepares the SPI interface and initializes the sensor hardware. So your magnetic sensor initialization code will look like:
```cpp
MagneticSensorI2C sensor = MagneticSensorI2C(0x36, 12, 0x0E, 4);

void loop(){
  ...
  sensor.init();
  ...
}
```

If you wish to use more than one magnetic sensor using SPI interface, make sure your sensors have different addresses, here is a simple example:
```cpp
MagneticSensorI2C sensor1 = MagneticSensorI2C(0x36, 12, 0x0E, 4);
MagneticSensorI2C sensor2 = MagneticSensorI2C(0x37, 12, 0x0E, 4);

void loop(){
  ...
  sensor1.init();
  sensor2.init();
  ...
}
```

Please check the `magnetic_sensor_i2_example.ino` example to see more about it.



## Analog output `MagneticSensorAnalog`
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

Finally after the initialization the only thing you need to do afterwards is to call the `init()` function. This function initializes the sensor hardware. So your magnetic sensor initialization code will look like:
```cpp
MagneticSensorAnalog sensor = MagneticSensorAnalog(A1, 14, 1020);

void loop(){
  ...
  sensor.init();
  ...
}
```

If you wish to use more than one magnetic sensor, make sure you connect their `chip_select` pins to different arduino pins and follow the same idea as above, here is a simple example:
```cpp
MagneticSensorAnalog sensor1 = MagneticSensorAnalog(A1, 14, 1020);
MagneticSensorAnalog sensor2 = MagneticSensorAnalog(A2, 14, 1020);

void loop(){
  ...
  sensor1.init();
  sensor2.init();
  ...
}
```

Please check the `magnetic_sensor_analog_example.ino` example to see more about it.


## Using magnetic sensor in real-time

There are two ways to use magnetic sensor implemented within this library:
- As motor position sensor for FOC algorithm
- As standalone position sensor

### Position sensor for FOC algorithm

To use the ensor with the FOC algorithm implemented in this library, once when you have initialized `sensor.init()` you just need to link it to the BLDC motor by executing:
```cpp
motor.linkSensor(&sensor);
```

### Standalone sensor 

To get the magnetic sensor angle and velocity at any given time you can use the public methods:
```cpp
class MagneticSensorSPI{
 public:
    // shaft velocity getter
    float getVelocity();
  	// shaft angle getter
    float getAngle();
}

class MagneticSensorI2C{
 public:
    // shaft velocity getter
    float getVelocity();
  	// shaft angle getter
    float getAngle();
}
```

Here is a quick example for the AS5047U magnetic sensor with SPI communication:
```cpp
#include <SimpleFOC.h>

// MagneticSensorSPI(int cs, float _cpr, int _angle_register)
// cs              - SPI chip select pin 
// bit_resolution  - sensor resolution
// angle_register  - (optional) angle read register - default 0x3FFF
MagneticSensorSPI as5047u = MagneticSensorSPI(10, 14, 0x3FFF);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // initialise magnetic sensor hardware
  as5047u.init();

  Serial.println("as5047u ready");
  _delay(1000);
}

void loop() {
  // display the angle and the angular velocity to the terminal
  Serial.print(as5047u.getAngle());
  Serial.print("\t");
  Serial.println(as5047u.getVelocity());
}
```

Here is a quick example for AS5600 magnetic sensor with I2C communication:
```cpp
#include <SimpleFOC.h>

// MagneticSensorI2C(uint8_t _chip_address, float _cpr, uint8_t _angle_register_msb)
//  chip_address         - I2C chip address
//  bit_resolution       - resolution of the sensor
//  angle_register_msb   - angle read register msb
//  bits_used_msb        - number of used bits in msb register
MagneticSensorI2C as5600 = MagneticSensorI2C(0x36, 12, 0x0E, 4);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // init magnetic sensor hardware
  as5600.init();

  Serial.println("AS5600 ready");
  _delay(1000);
}

void loop() {
  // display the angle and the angular velocity to the terminal
  Serial.print(as5600.getAngle());
  Serial.print("\t");
  Serial.println(as5600.getVelocity());
}
```

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

  // initialise magnetic sensor hardware
  sensor.init();

  Serial.println("Sensor ready");
  _delay(1000);
}

void loop() {
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```