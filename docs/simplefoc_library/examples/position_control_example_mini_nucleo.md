---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span>Mini</span> & Nucleo
parent: Example projects
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 10
permalink: /mini_example_nucleo
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---



# Position control example<br>using <span class="simple">Simple<span class="foc">FOC</span>Mini</span>
For this BLDC motor position control example we are going to be using this hardware:

[Stm32 Nucleo-64](https://www.mouser.fr/ProductDetail/STMicroelectronics/NUCLEO-F446RE?qs=%2Fha2pyFaduj0LE%252BzmDN2WNd7nDNNMR7%2Fr%2FThuKnpWrd0IvwHkOHrpg%3D%3D) | [Arduino <span class="simple">Simple<span class="foc">FOC</span>Mini</span>](simplefocmini) | [AMT 103 encoder](https://www.mouser.fr/ProductDetail/CUI-Devices/AMT103-V?qs=%2Fha2pyFaduiAsBlScvLoAWHUnKz39jAIpNPVt58AQ0PVb84dpbt53g%3D%3D) | [IPower GBM4108H-120T](https://www.ebay.com/itm/iPower-Gimbal-Brushless-Motor-GBM4108H-120T-for-5N-7N-GH2-ILDC-Aerial-photo-FPV/254541115855?hash=item3b43d531cf:g:q94AAOSwPcVVo571)
--- | --- | --- | --- 
<img src="extras/Images/nucleo.jpg" class="imgtable150"> |  <img src="https://simplefoc.com/assets/img/mini.jpg" class="imgtable150">  | <img src="extras/Images/enc1.png" class="imgtable150">  | <img src="extras/Images/mot.jpg" class="imgtable150"> 


# Connecting everything together
<p><img src="extras/Images/connection_mini_nucleo.jpg" class="width60"></p>

For more information about the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> check the [docs](simplefocmini). Currently, there are two versions of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> board available v1.0 and v1.1. The main difference between the two versions is the order of the pins. This example can be used with both versions of the board, however there are slight differences in the pinout.<br>
Choose your <span class="simple">Simple<span class="foc">FOC</span>Mini</span> version: 


<a href="javascript:show(0,'mini');" class="btn btn-mini btn-0 btn-primary"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.0</a> 
<a href ="javascript:show(1,'mini');" class="btn btn-mini  btn-1"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.1</a> 

<p class="mini mini-0" ><img src="extras/Images/mini_connection_mucleo.png" class="width60"></p>
<p class="mini mini-1 hide" ><img src="extras/Images/miniv11_connection_mucleo.png" class="width60"></p>

## Encoder 
- Channels `A` and `B` are connected to the Arduino  UNO pins `2` and `3`. 

## Motor
- Motor phases `a`, `b` and `c` are connected directly the motor terminal connector connectors `M1`, `M2` and `M3` of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> board

## <span class="simple">Simple<span class="foc">FOC</span>Mini</span>

<a href="javascript:show(0,'mini');" class="btn btn-mini btn-0 btn-primary"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.0</a> 
<a href ="javascript:show(1,'mini');" class="btn btn-mini  btn-1"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.1</a> 

<div markdown="1" class="mini mini-0">

- The most convenient way of plugging the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> board to the Nucleo board is to stack it on its pins `10-13`.
   - `GND` - `GND`
   - `IN1` - `13`
   - `IN2` - `12`
   - `IN3` - `11`
   - `EN` - `10`

</div>


<div markdown="1" class="mini mini-1 hide">

- The most convenient way of plugging the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> board to the Nucleo board is to stack it on its pins `10-13`.
   - `GND` - `GND`
   - `EN` - `13`
   - `IN3` - `12`
   - `IN2` - `11`
   - `IN1` - `10`

</div>

# Arduino code 
Let's go through the full code for this example and write it together.
First thing you need to do is include the `SimpleFOC` library:

```cpp
#include <SimpleFOC.h>
```
Make sure you have the library installed. If you still don't have it please check the [get started page](installation)


## Encoder code
First we define the `Encoder` class with the A and B channel pins and number of impulses per revolution.
```cpp
// define Encoder
Encoder encoder = Encoder(2, 3, 2048);
```
Then we define the buffering callback functions.
```cpp
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}
```
In the `setup()` function we initialize the encoder and enable interrupts:
```cpp
// initialize encoder hardware
encoder.init();
// hardware interrupt enable
encoder.enableInterrupts(doA, doB);
```
And that is it, let's setup the motor.

<blockquote class="info">For more configuration parameters of the encoders please check the <code class="highlighter-rouge">Encoder</code> class <a href="encoder">docs</a>.</blockquote>


## Motor code


<a href="javascript:show(0,'mini');" class="btn btn-mini btn-0 btn-primary"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.0</a> 
<a href ="javascript:show(1,'mini');" class="btn btn-mini  btn-1"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.1</a> 

First we need to define the `BLDCMotor` class with the  number od pole pairs (`11`)
```cpp
// define BLDC motor
BLDCMotor motor = BLDCMotor(11);
```
<blockquote class="warning">If you are not sure what your pole pairs number is please check the  <code class="highlighter-rouge">find_pole_pairs.ino</code> example.</blockquote>


Next we need to define the `BLDCDriver3PWM` class with the PWM pin numbers of the motor and the driver enable pin


<div markdown="1" class="mini mini-0">

```cpp
// define BLDC driver
BLDCDriver3PWM driver = BLDCDriver3PWM(13, 12, 11, 10);
```
</div>
<div markdown="1" class="mini mini-1 hide">

```cpp
// define BLDC driver
BLDCDriver3PWM driver = BLDCDriver3PWM(10, 11, 12, 13);
```
</div>


Then in the `setup()` we configure first the voltage of the power supply if it is not `12` Volts and init the driver.
```cpp
// power supply voltage
// default 12V
driver.voltage_power_supply = 12;
driver.init();
```

Then we tell the motor which control loop to run by specifying the `motor.controller` variable.
```cpp
// set control loop type to be used
// MotionControlType::torque
// MotionControlType::velocity
// MotionControlType::angle
motor.controller = MotionControlType::angle;
```
Now we configure the velocity PI controller parameters
```cpp
// velocity PI controller parameters
// default P=0.5 I = 10
motor.PID_velocity.P = 0.2;
motor.PID_velocity.I = 20;

//default voltage_power_supply
motor.voltage_limit = 6;
```
Additionally we can configure the Low pass filter time constant `Tf`
```cpp
// velocity low pass filtering
// default 5ms - try different values to see what is the best. 
// the lower the less filtered
motor.LPF_velocity.Tf = 0.02;
```
Finally we configure position P controller gain and the velocity limit variable.
```cpp
// angle P controller 
// default P=20
motor.P_angle.P = 20;
//  maximal velocity of the position control
// default 20
motor.velocity_limit = 4;
```
<blockquote class="info">For more information about the angle control loop parameters please check the  <a href="angle_loop">doc</a>.</blockquote>

Next we connect the encoder and the driver to the motor, do the hardware init and init of the Field Oriented Control.
```cpp  
// link the motor to the sensor
motor.linkSensor(&encoder);
// link the motor to the driver
motor.linkDriver(&driver);

// initialize motor
motor.init();
// align encoder and start FOC
motor.initFOC();
```
The last peace of code important for the motor is of course the FOC routine in the `loop` function.
```cpp
void loop() {
// iterative FOC function
motor.loopFOC();

// iterative function setting and calculating the angle/position loop
// this function can be run at much lower frequency than loopFOC function
motor.move();
}
```
That is it, let's see the full code now!
<blockquote class="info">For more configuration parameters and control loops please check the <code class="highlighter-rouge">BLDCMotor</code> class <a href="motors_config">doc</a>.</blockquote>

## Full Arduino code
To the full code I have added a small serial [commander interface](commander_interface),  to be able to change position/angle target value in real time.



<a href="javascript:show(0,'mini');" class="btn btn-mini btn-0 btn-primary"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.0</a> 
<a href ="javascript:show(1,'mini');" class="btn btn-mini  btn-1"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.1</a> 

<div markdown="1" class="mini mini-0">

```cpp
#include <SimpleFOC.h>

// init BLDC motor
BLDCMotor motor = BLDCMotor( 11 );
// init driver
BLDCDriver3PWM driver = BLDCDriver3PWM(13, 12, 11, 10);
//  init encoder
Encoder encoder = Encoder(2, 3, 2048);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// commander interface
Commander command = Commander(Serial);
void onTarget(char* cmd){ command.motion(&motor, cmd); }

void setup() {

  // initialize encoder hardware
  encoder.init();
  // hardware interrupt enable
  encoder.enableInterrupts(doA, doB);
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // power supply voltage
  // default 12V
  driver.voltage_power_supply = 12;
  driver.init();
  // link the motor to the driver
  motor.linkDriver(&driver);

  // set control loop to be used
  motor.controller = MotionControlType::angle;
  
  // controller configuration based on the control type 
  // velocity PI controller parameters
  // default P=0.5 I = 10
  motor.PID_velocity.P = 0.2;
  motor.PID_velocity.I = 20;
  
  //default voltage_power_supply
  motor.voltage_limit = 6;

  // velocity low pass filtering
  // default 5ms - try different values to see what is the best. 
  // the lower the less filtered
  motor.LPF_velocity.Tf = 0.02;

  // angle P controller 
  // default P=20
  motor.P_angle.P = 20;
  //  maximal velocity of the position control
  // default 20
  motor.velocity_limit = 4;
  
  // initialize motor
  motor.init();
  // align encoder and start FOC
  motor.initFOC();

  // add target command T
  command.add('T', doTarget, "motion control");

  // monitoring port
  Serial.begin(115200);
  Serial.println("Motor ready.");
  Serial.println("Set the target angle using serial terminal:");
  _delay(1000);
}

void loop() {
  // iterative FOC function
  motor.loopFOC();

  // function calculating the outer position loop and setting the target position 
  motor.move();

  // commander interface with the user
  commander.run();

}
```

</div>


<div markdown="1" class="mini mini-1 hide">

```cpp
#include <SimpleFOC.h>

// init BLDC motor
BLDCMotor motor = BLDCMotor( 11 );
// init driver
BLDCDriver3PWM driver = BLDCDriver3PWM(10, 11, 12, 13);
//  init encoder
Encoder encoder = Encoder(2, 3, 2048);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// commander interface
Commander command = Commander(Serial);
void onTarget(char* cmd){ command.motion(&motor, cmd); }

void setup() {

  // initialize encoder hardware
  encoder.init();
  // hardware interrupt enable
  encoder.enableInterrupts(doA, doB);
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // power supply voltage
  // default 12V
  driver.voltage_power_supply = 12;
  driver.init();
  // link the motor to the driver
  motor.linkDriver(&driver);

  // set control loop to be used
  motor.controller = MotionControlType::angle;
  
  // controller configuration based on the control type 
  // velocity PI controller parameters
  // default P=0.5 I = 10
  motor.PID_velocity.P = 0.2;
  motor.PID_velocity.I = 20;
  
  //default voltage_power_supply
  motor.voltage_limit = 6;

  // velocity low pass filtering
  // default 5ms - try different values to see what is the best. 
  // the lower the less filtered
  motor.LPF_velocity.Tf = 0.02;

  // angle P controller 
  // default P=20
  motor.P_angle.P = 20;
  //  maximal velocity of the position control
  // default 20
  motor.velocity_limit = 4;
  
  // initialize motor
  motor.init();
  // align encoder and start FOC
  motor.initFOC();

  // add target command T
  command.add('T', doTarget, "motion control");

  // monitoring port
  Serial.begin(115200);
  Serial.println("Motor ready.");
  Serial.println("Set the target angle using serial terminal:");
  _delay(1000);
}

void loop() {
  // iterative FOC function
  motor.loopFOC();

  // function calculating the outer position loop and setting the target position 
  motor.move();

  // commander interface with the user
  commander.run();

}
```
</div>