---
layout: default
title: StepperMotor
nav_order: 2
permalink: /steppermotor
parent: Motor code
grand_parent: Writing the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Stepper Motor configuration

<div class="width60">
<img src="extras/Images/nema17_2.jpg" style="width:30%;display:inline"><img src="extras/Images/nema17_1.jpg" style="width:30%;display:inline"><img src="extras/Images/nema23.jpg" style="width:30%;display:inline">
</div>

All stepper motor are handled by `StepperMotor` class. The class implements:
- Stepper motor FOC algorithm
- Motion control loops
- Monitoring

## Step 1. Creating the instance of the stepper motor
To create a stepper motor instance you need to specify the number of `pole pairs` of the motor.
```cpp
// StepperMotor(  int pp, (optional R, KV))
//  - pp  - pole pair number
//  - R   - phase resistance value - optional
//  - KV  - motor KV rating [rpm/V] - optional
StepperMotor motor = StepperMotor(50, 1.5, 20.6);
```
<blockquote class="info"><p class="heading">Pole pair number </p>
Most of the stepper motors are 200 step per rotation motors making them 50 pole pair motors. In practice you can know the <code class="highlighter-rouge">pole_paris</code> number by dividing the number of steps per rotation by <code class="highlighter-rouge">4</code>.<br><br>
If you are not sure what your <code class="highlighter-rouge">pole_paris</code> number is. The library provides you an example code to estimate your <code class="highlighter-rouge">pole_paris</code> number in the examples <code class="highlighter-rouge">examples/utils/calibration/find_pole_pairs_number.ino</code>.
 </blockquote>

<blockquote class="warning" markdown="1">
<p class="heading">RULE OF THUMB: KV value </p>
We suggest to set the `KV` value provided to the library to 50-70% higher than the one given in the datasheet, or the one determined experimentally. Depending on the motor mechanics the appropriate value will be in between the 100% to 200% of the motor's KV rating.
</blockquote>

<blockquote class="info" markdown="1">
<p class="heading">Finding KV rating value </p>
If you are not sure what your motor's <code class="highlighter-rouge">KV</code> is. You can easily find it as the velocity of your motor when controlled in the voltage torque control with a setpoint of 1 volt -  <code class="highlighter-rouge">velocity_at_one_volt</code> . The KV rating units are rpm per Volt, and as the <span class="simple">Simple<span class="foc">FOC</span>library</span> works with rad/s rather than rpm. You once when you get the velocity reached with 1 volt setpoint, you can multiply it with $$30/\pi$$ 

```cpp
KV = velocity_at_one_volt * 30/pi
```
You can also use the provided libray examples `examples/utils/calibration/find_KV_rating.ino`.
<br>

</blockquote>

### Motor phase reistance and KV rating 
Providing the KV rating in combination with the phase resistance (not very used for current based torque modes `foc_current` and `dc_current`) will enable the user to control the motor's current without measuring it. The user will be able to control (and limit) the estimated current of the motor using the voltage control mode. Read more in the [torque control docs](voltage_torque_mode).

Working with currents instead of voltages is better in may ways, since the torque of the BLDC motor is proportional to the current and not voltages and especially since the same voltage value will produce very different currents for different motors (due to the different phase resistance). Once when the phase resistance is provided the user will be able to set current limit for its BLDC motor instead of voltage limit which is much easier to understand. 

It is important to say that once you specify the phase resistance value, you will most probably have to retune the [velocity motion control](velocity_loop) and [angle motion control](angle_loop) parameters, due to the reason that the voltages and currents values are in different orders of magnitude. The rule of thumb is to divide all the `P`, `I` and `D` gains with the `motor.phase_resistance` value. That will be a good staring point.

Finally, this parameter is suggested to be used if one whats to switch in real time in between voltage ([voltage mode](voltage_mode)) and current based ([DC current](dc_current_torque_mode) and [FOC current](foc_current_torque_mode)) torque control strategies. Since in this way all the torque control loops will have current as input (target value) the user will not have to change the motion control parameters (PID values). 

<blockquote class="info">
<p class="heading">Open-loop motion control will use KV and phase resitance values  </p>
KV rating and the pahse resitance values will be used in te open loop contol as well to let the user to limit the current drawn by the motor instead of limitting the volatge. Read more in the <a href="open_loop_motion_control">open-loop motion control docs</a>.
</blockquote>


## Step 2. Linking the sensor 
Once when you have the `motor` defined and the sensor initialized you need to link the `motor` and the `sensor` by executing:    
```cpp
// link the sensor to the motor
motor.linkSensor(&sensor);
```
Method `linkSensor` is able to link the motor to any sensor implemented in this library. The `sensor` will be used to determine electrical position of the motor for the FOC algorithm as well as for the motion control loops of velocity and position. See the [position sensor docs](sensors) for more info!

<blockquote class="info">Linking is not necessary when using the openloop motion control.</blockquote>

## Step 3. Linking the driver 
Once when you have the `motor` defined and the driver initialized you need to link the `motor` and the `driver` by executing:    
```cpp
// link the driver to the motor
motor.linkDriver(&driver);
```

The `StepperMotor` class expect to receive a `StepperDriver` class instance, implemented by default with the `StepperDriver4PWM` class. The `driver` deals with all the hardware specific operations related to specific microcontroller architecture and driver hardware. See the [stepper driver docs](stepperdriver) for more info!

## Step 4. Configuration

If you choose not to set some of the configuration parameters they will take values defined in the `defaults.h` file.
Check the [library source code](source_code) to dig deeper.

### Step 4.1 PWM Modulation type

You can set them by changing the `motor.foc_modulation` variable:
```cpp
// choose FOC modulation
// FOCModulationType::SinePWM;
motor.foc_modulation = FOCModulationType::SinePWM;
```
`StepperMotor` class has only Sinusoidal PWM modulation implemented for the moment <a href="https://github.com/simplefoc/Arduino-FOC/releases"> <i class="fa fa-tag"> current version</i></a>.

For more information about the theory of these approaches please and source code implementation check the [FOC implementation docs](foc_implementation) or visit the [digging deeper section](digging_deeper).


### Step 4.2 Sensor and motor aligning parameters
The voltage used for the motor and sensor alignment set the variable `motor.voltage_sensor_align`:
```cpp
// aligning voltage [V]
motor.voltage_sensor_align = 3; // default 3V
```

If your sensor is an encoder and if it has an index pin, you can set the index search velocity value by set the variable `motor.velocity_index_search`:
```cpp
// incremental encoder index search velocity [rad/s]
motor.velocity_index_search = 3; // default 1 rad/s
```

### Step 4.3 Position sensor offset
For some applications it is convenient to specify the sensor absolute zero offset, you can define it by changing the parameter  `motor.sensor_offset`:
```cpp
// sensor offset [rad]
motor.sensor_offset = 0; // default 0 rad
```
This parameter can be changed in real-time.


### Step 4.4 Motor phase resistance and KV rating

Motor phase resistance and KV rating are optional parameters which are not used for current based torque modes. These variables are used to estimate the motor current in the voltage torque mode and for open-loop motion control. If user specifies the `motor.phase_resistance` and `motor.KV_rating` (either in constructor or in the `setup()` function) the library will allow user to work with current value and it will calculate the necessary voltages automatically. In the setup function you can change this parameter by setting:
```cpp
// motor phase resistance [Ohms]
motor.phase_resistance = 2.54; // Ohms - default not set
// motor KV rating [rpm/V]
motor.KV_rating = 100; // rpm/volt - default not set
```

Read more in the [torque control docs](voltage_torque_mode).


### Step 4.5 Motion control parameters  

There are 3 different closed loop control strategies implemented in the Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>: 
- [torque control loop using voltage](torque_control)
- [position/angle motion control](angle_loop)
- [velocity motion control](velocity_loop)

Additionally <span class="simple">Simple<span class="foc">FOC</span>library</span> implements two open loop control strategies as well:
- [position open-loop control](angle_openloop)
- [velocity open-loop control](velocity_openloop)

You set it by changing the `motor.controller` variable. 
```cpp
// set FOC loop to be used
// MotionControlType::torque      - torque control loop using voltage
// MotionControlType::velocity    - velocity motion control
// MotionControlType::angle       - position/angle motion control
// MotionControlType::velocity_openloop    - velocity open-loop control
// MotionControlType::angle_openloop       - position open-loop control
motor.controller = MotionControlType::angle;
```
<blockquote class="warning"><p class="heading"> Important!</p>This parameter doesn't have a default value and it has to be set before real-time execution starts.</blockquote>

Each motion control strategy has its own parameters and you can find more about them on [motion control docs](motion_control). 

```cpp
// set control loop type to be used
motor.controller = MotionControlType::angle;

// controller configuration based on the control type 
motor.PID_velocity.P = 0.2;
motor.PID_velocity.I = 20;
motor.PID_velocity.D = 0.001;

// velocity low pass filtering time constant
motor.LPF_velocity.Tf = 0.01;

// angle loop controller
motor.P_angle.P = 20;

// motion control limits
// angle loop velocity limit
motor.velocity_limit = 50;
// either voltage limit
motor.voltage_limit = 12; // Volts -  default driver.voltage_limit
// or current limit - if phase_resistance set
motor.current_limit = 1; // Amps -  default 2 Amps
```  

### Step 4.7 Configuration done - `motor.init()`
Finally the configuration is terminated by running `init()` function which prepares all the hardware and software motor components using the configured values.
```cpp
// initialize motor
motor.init();
```

## Step 5. Align motor and all the sensors - Field Oriented Control init

After the position sensor, driver and the motor are configured, and before we can start the motion control we need to align all  hardware components in order to initialize the FOC algorithm. This is done in the scope of the funciton `motor.initFOC()`
```cpp
// align sensor and start FOC
motor.initFOC();
```
<blockquote class="info"><p class="heading"> Can be skipped for openloop control!</p>If no sensor is attached this function will not really do anything, but you can still call it if necessary or more convenient. </blockquote>

This function does several things:
- Checks if driver (and current sense if available) are well initialised
- Checks/modifies position sensor direction in respect to the motor's direction
- Searches for encoder index if necessary
- Finds the motor electrical offset in respect to the position sensor
- Checks/modifies current sense pinout and gains signs if one available to make sure it aligned with the driver 

If for some reason the `initFOC` fails this function will return `0` and it will disable your motor and display you a message what is wrong (when using the [monitoring](monitoring) ). If everything is well configured, the call of this function will return `1` and the our setup is done, FOC is ready to be used! So we suggest you to check if the init function was executed successfully before continuing:

```cpp
// init current sense
if (motor.initFOC())  Serial.println("FOC init success!");
else{
  Serial.println("FOC init failed!");
  return;
}
```
The alignment procedure will have to move your motor several times and might not be desirable behavior, therefore for most of the position sensors (except encodes) and current senses, this alignment procedure can be skipped by following the steps 5.1. 

### Step 5.1 Skip alignment - position sensor

If you are using absolute sensors such as magnetic sensors or hall sensors, once you have done the alignment procedure and once you have the motor's zero electrical offset sensor direction you no longer need the full calibration sequence. Therefore, to the `motor.initFOC()` you can provide the sensor offset `zero_electric_offset` and sensor direction `sensor_direction` to avoid alignment procedure:
```cpp
// align sensor and start FOC
//motor.initFOC(zero_electric_offset, sensor_direction);
motor.initFOC(2.15, Direction::CW);
```
The same can be done by using the motor parameters:
```cpp
// align sensor and start FOC
motor.zero_electric_offset  = 2.15; // rad
motor.sensor_direction = Direction::CW; // CW or CCW
motor.initFOC();
```
You can find these values by running the `find_sensor_offset_and_direction.ino` example.

More generally, if you know any of these two values make sure to provide and the `iniFOC` will skip that part of the calibration. For example, for encoder sensors the zero electrical offset changes all the time but the sensor direction will stay the same so you can provide it and skip a large part of the calibration sequence.

## Step 6. Real-time motion control

The real-time motion control of theArduino <span class="simple">Simple<span class="foc">FOC</span>library</span> is realized with two functions: 
- `motor.loopFOC()` - low level torque control 
- `motor.move(float target)` - high level motion control


The function `loopFOC()` implements the torque control loop. As the stepper motors only support [torque using voltage mode](voltage_torque_mode) this function will read the current motor angle from the sensor, turn it into the electrical angle and transforms the q-axis <i>U<sub>q</sub></i> voltage command  `motor.voltage_q` to the appropriate phase voltages <i>u<sub>a</sub></i>, <i>u<sub>b</sub></i> and <i>u<sub>c</sub></i> which are set then set to the motor. If the stepper motor's phase resistance and KV rating are provided this function will furthermore calculate the estimated current and the user will be able to control this estiamted current value <i>I<sub>q</sub></i> directly. 

```cpp
// Function running the low level torque control loop
// it calculates the gets motor angle and sets the appropriate voltages 
// to the phase pwm signals
// - the faster you can run it the better Arduino UNO ~1ms, Bluepill ~ 100us
motor.loopFOC();
```

<blockquote class="info"><p class="heading"> Can be skipped for openloop control!</p>This function will have no effect if the motor is run in open loop! </blockquote>

This function is execution time is critical so it is very important that the `motor.loopFOC()` function is executed as fast as possible.

<blockquote class="warning"><p class="heading">Rule od thumb: execution time</p>
The faster you can run this function the better 😃
</blockquote>

Finally, once we have a way to set the torque command (current <i>I<sub>q</sub></i> or voltage <i>I<sub>q</sub></i>) to the motor using the FOC algorithm we can proceed to the motion control. And this is done with `motor.move()` function.
```cpp
// Function executing the motion control loops configured by the motor.controller parameter of the motor. 
// - This function doesn't need to be run upon each loop execution - depends of the use case
//
// target  Either torque, angle or velocity based on the motor.controller
//         If it is not set the motor will use the target set in its variable motor.target
motor.move(target);
```

The `move()` method executes the motion control loops of the algorithm. If is governed by the `motor.controller` variable. It executes either pure torque loop, velocity loop or angle loop.

It receives one parameter `float target` which is current user defined target value.
- If the user runs [velocity loop](velocity_loop) or [velocity open-loop](velocity_openloop), `move` function will interpret `target` as the target velocity.
- If the user runs [angle loop](angle_loop) or [angle open-loop](angle_openloop), `move` will interpret `target` parameter as the target angle. 
- If the user runs the [torque loop](torque_control), `move` function will interpret the `target` parameter as either voltage <i>u<sub>q</sub></i> or current <i>i<sub>q</sub></i> (if phase resistance provided). 

The `target` parameter is optional and if it is not set, the target value will be set by the public motor variable `motor.target`. The equivalent code would be:

```cpp
motor.target = 2;
motor.move();
```

## Step 6.1 Motion control downsampling
For many motion control applications it will make sense run multiple torque control loops for each motion control loop. This can have a great impact on the smoothness and can provide better high-speed performance. Therefore this library enables a very simple downsampling strategy for the `move()` function which is set using the parameter `motor.motion_downsample`:
```cpp
// downsampling value
motor.motion_downsample = 5; // - times (default 0 - disabled)
```
The downsampling strategy works in a very simple way, even though the `motor.move()` is called in each arduino `loop` it will only be executed each `motor.motion_downsample` calls. This parameter si optional and can be configured in real time. 

<blockquote class="warning"><p class="heading">BEWARE: Motion control impact</p>
Different values of the downsampling might require a bit of tuning of motion parameters.</blockquote>  


And that is it, you have your complete Field Oriented Controlled BLDC motor with motion control. 

## User interaction

<span class="simple">Simple<span class="foc">FOC</span>library</span> implements two types of real-time user interaction:
- [Monitoring functionality](monitoring)
- [Motor commands](communication)


## Digging deeper
For more theoretical explanations and source code implementations of the FOC algorithm and the motion control approaches check out the [digging deeper section](digging_deeper).

## Example code
A simple stepper motor torque control using voltage based on the FOC algorithm.
```cpp
/**
 * Torque control example using voltage control loop.
 */
#include <SimpleFOC.h>

// Stepper motor instance
StepperMotor motor = StepperMotor( 50 );
// Stepper driver instance
StepperDriver4PWM driver = StepperDriver4PWM(9, 10, 5, 6, 7, 8);
// sensor instance
MagneticSensorI2C sensor = MagneticSensorI2C(AS5600_I2C);

void setup() { 
  
  // initialize encoder sensor hardware
  sensor.init();
  // link the motor to the sensor
  motor.linkSensor(&sensor);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link driver
  motor.linkDriver(&driver);

  // set motion control loop to be used
  motor.controller = MotionControlType::torque;

  // initialize motor
  motor.init();
  // align sensor and start FOC
  motor.initFOC();

  // setting target voltage
  motor.target = 2;

  _delay(1000);
}

void loop() {

  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();
}
```
