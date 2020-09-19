---
layout: default
title: BLDC Motor
nav_order: 2
parent: Using the Code
permalink: /motor_initialization
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Motor setup

<div class="width60">
<img src="extras/Images/mot2.jpg" style="width:24%;display:inline"><img src="extras/Images/bigger.jpg" style="width:24%;display:inline"><img src="extras/Images/big.jpg" style="width:24%;display:inline"><img src="extras/Images/mot.jpg" style="width:24%;display:inline">
</div>

All BLDC motors are handled with the `BLDCMotor` class. This class is the heart ❤️ of the  Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>. <br>
It implements:
- BLDC motor & BLDC driver hardware support 
- FOC algorithm
- Motion control loops
- Monitoring
- User communication interface

## Step 1. Hardware setup
To initialize the hardware of the BLDC motor and BLDC driver you need to specify the `pwm` pin numbers, number of `pole pairs` of the motor and optionally `enable` pin.
```cpp
//  BLDCMotor( int phA, int phB, int phC, int pp, int en)
//  - phA, phB, phC - motor A,B,C phase pwm pins
//  - pp            - pole pair number
//  - enable pin    - (optional input)
BLDCMotor motor = BLDCMotor(9, 10, 11, 11, 8);
```
<blockquote class="info"><p class="heading">Pole pair number </p>
If you are not sure what your `pole_paris` number is I included an example code to estimate your <code class="highlighter-rouge">pole_paris</code> number in the examples <code class="highlighter-rouge">find_pole_pairs_number.ino</code>.
 </blockquote>

When you have your `motor` defined you can start the configuration. 

## Step 2. Linking the sensor 
Once when you have the `motor` defined and the sensor initialized you need to link the `motor` and the `sensor` by executing:    
```cpp
// link the motor to the sensor
motor.linkSensor(&sensor);
```

## Step 3. Configuration

Once when you have motor hardware setup and sensor linked you can start to configure:
- FOC algorithm parameters
- Motion control parameters

If you choose not to set some of the configuration parameters they will take values defined in the `defaults.h` file.
Check the [library source code](source_code) to dig deeper.

### Step 3.1 FOC algorithm parameters

Field oriented control algorithm implemented in this library has only two parameters:
- Modulation type
- Motor and sensor align voltage.
<blockquote class="warning"> Neither of these parameters is strictly necessary to configure for most applications. </blockquote>

There are two types of Field Oriented Control modulation types implemented in this library:
- Sinusoidal PWM modulation
- Space Vector PWM modulation

You can set them by changing the `motor.foc_modulation` variable:
```cpp
// choose FOC modulation
// FOCModulationType::SineWPM; (default)
// FOCModulationType::SpaceVectorPWM;
motor.foc_modulation = FOCModulationType::SpaceVectorPWM;
```

The voltage used for the motor and sensor alignment set the variable `motor.voltage_sensor_align`:
```cpp
// aligning voltage [V]
// default 6V
motor.voltage_sensor_align = 3;
```
For more information about the theory of these approaches please and source code implementation check the [FOC implementation docs](foc_implementation) or visit the [digging deeper section](digging_deeper).

### Step 3.2 Motion control parameters  

There are 3 different closed loop control strategies implemented in the Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>: 
- [torque control loop using voltage](voltage_loop)
- [position/angle motion control](angle_loop)
- [velocity motion control](velocity_loop)

Additionally <span class="simple">Simple<span class="foc">FOC</span>library</span> implements two open loop control strategies as well:
- [position open-loop control](angle_openloop)
- [velocity open-loop control](velocity_openloop)

You set it by changing the `motor.controller` variable. 
```cpp
// set FOC loop to be used
// ControlType::voltage     - torque control loop using voltage
// ControlType::velocity    - velocity motion control
// ControlType::angle       - position/angle motion control
// ControlType::velocity_openloop    - velocity open-loop control
// ControlType::angle_openloop       - position open-loop control
motor.controller = ControlType::angle;
```
<blockquote class="warning"><p class="heading"> Important!</p>This parameter doesn't have a default value and it has to be set before real-time execution starts.</blockquote>

Each motion control strategy has its own parameters and you can find more about them on [motion control docs](motion_control). 

Here is the list of all the motion control configuration parameters:
```cpp
// power supply voltage [V]
motor.voltage_power_supply = 12;

// index search velocity [rad/s]
motor.velocity_index_search = 3;

// set control loop type to be used
motor.controller = ControlType::voltage;

// controller configuration based on the control type 
motor.PID_velocity.P = 0.2;
motor.PID_velocity.I = 20;
motor.PID_velocity.D = 0.001;

// velocity low pass filtering time constant
motor.LPF_velocity.Tf = 0.01;

// angle loop controller
motor.P_angle.P = 20;

// angle loop velocity limit
motor.velocity_limit = 50;
// default voltage_power_supply
motor.voltage_limit = 12;
```

Finally the configuration is terminated by running `init()` function which prepares all the hardware and software motor components using the configured values.
```cpp
// initialize motor
motor.init();
```

## Step 4. Field Oriented Control initialization

After the motor and sensor are initialized and configured, and before we can start the motion control we need to initialize the FOC algorithm. 
```cpp
// align sensor and start FOC
motor.initFOC();
```
This function aligns sensor and motor zero positions and initializes FOC variables. It is intended to be run in the `setup` function of the Arduino. After the call of this function FOC is ready and our setup is done! 

If you are using absolute sensors such as magnetic sensors, in the `initFOC()` you can provide the sensor offset `zero_electric_offset` and sensor direction `sensor_direction` to avoid alignment procedure:
```cpp
// align sensor and start FOC
//motor.initFOC(zero_electric_offset, sensor_direction);
motor.initFOC(2.15, Direction::CW);
```
You can find these values by running the `find_sensor_offset_and_direction.ino` example.

For more info about what really happens in `initFOC()` check the [FOC source code implementation](foc_implementation).

## Step 5. Real-time motion control

The real-time motion control of theArduino <span class="simple">Simple<span class="foc">FOC</span>library</span> is realized with two functions: `motor.loopFOC()` and `motor.move(float target)`.
```cpp
// Function running FOC algorithm in real-time
// it calculates the gets motor angle and sets the appropriate voltages 
// to the phase pwm signals
// - the faster you can run it the better Arduino UNO ~1ms, Bluepill ~ 100us
motor.loopFOC();
```
The function `loopFOC()` gets the current motor angle from the sensor, turns it into the electrical angle and transforms the desired <i>U<sub>q</sub></i> voltage `motor.voltage_q` to the appropriate phase voltages <i>u<sub>a</sub></i>, <i>u<sub>b</sub></i> and <i>u<sub>c</sub></i> which are set then set to the motor. 

The <i>U<sub>q</sub></i> to <i>u<sub>a</sub></i>, <i>u<sub>b</sub></i> and <i>u<sub>c</sub></i>  transformation is defined with the configuration parameter `motor.foc_modulation` and it can be either: ***Sinusoidal PWM*** or ***Space Vector PWM***.

This function is execution time is critical, therefore it is very important that the `motor.loopFOC()` function is executed as fast as possible!

<blockquote class="warning"><p class="heading">Rule od thumb: execution time</p>

The faster you can run this function the better!
<ul style="margin-bottom:0em">
    <li> Arduino UNO <code class="highlighter-rouge">loop()</code> ~ 500us </li>
    <li> Bluepill <code class="highlighter-rouge">loop()</code> ~ 100us</li>
    <li> Nucleo <code class="highlighter-rouge">loop()</code> ~ 50us</li>
</ul>
</blockquote>

Finally, when we can set the phase voltages to the motor using the FOC algorithm we can proceed to the motion control. And this is done with `motor.move()` function.

```cpp
// Function executing the control loops configured by the motor.controller parameter of the BLDCMotor. 
// - This function doesn't need to be run upon each loop execution - depends of the use case
//
// target  Either voltage, angle or velocity based on the motor.controller
//         If it is not set the motor will use the target set in its variable motor.target
motor.move(target);
```

The `move()` method executes the control loops of the algorithm. If is governed by the `motor.controller` variable. It executes either pure voltage loop, velocity loop or angle loop.

It receives one parameter `float target` which is current user defined target value.
- If the user runs [velocity loop](velocity_loop) or [velocity open-loop](velocity_openloop), `move` function will interpret `target` as the target velocity <i>v<sub>d</sub></i>.
- If the user runs [angle loop](angle_loop) or [angle open-loop](angle_openloop), `move` will interpret `target` parameter as the target angle <i>a<sub>d</sub></i>. 
- If the user runs the [voltage loop](voltage_loop), `move` function will interpret the `target` parameter as voltage <i>u<sub>d</sub></i>.

 The `target` parameter is optional and if it is not set, the target value will be set by the public motor variable `motor.target`. The equivalent code would be:

```cpp
motor.target = 2;
motor.move();
```

And that is it, you have your complete Field Oriented Controlled BLDC motor with motion control. 

## User interaction

<span class="simple">Simple<span class="foc">FOC</span>library</span> implements two types of real-time user interaction:
- [Monitoring functionality](monitoring)
- [Motor commands](communication)


## Digging deeper
For more theoretical explanations and source code implementations of the FOC algorithm and the motion control approaches check out the [digging deeper section](digging_deeper).
