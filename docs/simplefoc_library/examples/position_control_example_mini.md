---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span>Mini</span> & UNO
parent: Example projects
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 9
permalink: /mini_example
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
---


# Position control example<br>using <span class="simple">Simple<span class="foc">FOC</span>Mini</span>
For this BLDC motor position control example we are going to be using this hardware:

[Arduino UNO](https://store.arduino.cc/arduino-uno-rev3) | [Arduino <span class="simple">Simple<span class="foc">FOC</span>Mini</span>](simplefocmini) | [AMT 103 encoder](https://www.mouser.fr/ProductDetail/CUI-Devices/AMT103-V?qs=%2Fha2pyFaduiAsBlScvLoAWHUnKz39jAIpNPVt58AQ0PVb84dpbt53g%3D%3D) | [IPower GBM4108H-120T](https://www.ebay.com/itm/iPower-Gimbal-Brushless-Motor-GBM4108H-120T-for-5N-7N-GH2-ILDC-Aerial-photo-FPV/254541115855?hash=item3b43d531cf:g:q94AAOSwPcVVo571)
--- | --- | --- | --- 
<img src="extras/Images/arduino_uno.jpg" class="imgtable150"> |  <img src="https://simplefoc.com/assets/img/mini.jpg" class="imgtable150">  | <img src="extras/Images/enc1.png" class="imgtable150">  | <img src="extras/Images/mot.jpg" class="imgtable150"> 


# Connecting everything together
<p><img src="extras/Images/connection_mini.jpg" class="width60"></p>

For more information about the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> check the [docs](simplefocmini). Currently, there are two versions of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> board available v1.0 and v1.1. The main difference between the two versions is the order of the pins. This example can be used with both versions of the board, however there are slight differences in the pinout.<br>
Choose your <span class="simple">Simple<span class="foc">FOC</span>Mini</span> version: 


<script type="text/javascript">
    function show(id,cls){
        Array.from(document.getElementsByClassName(cls)).forEach(
        function(e){e.style.display = "none";});
        Array.from(document.getElementsByClassName(cls+"-"+id)).forEach(
        function(e){e.style.display = "block";});
        Array.from(document.getElementsByClassName("btn-"+cls)).forEach(
        function(e){e.classList.remove("btn-primary");});
        document.getElementById("btn-"+id).classList.add("btn-primary");
    }
</script>

<style>
    .mini-1{
        display: none;
    }
</style>

<a href="javascript:show(0,'mini');" id="btn-0" class="btn btn-mini btn-primary"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.0</a> 
<a href ="javascript:show(1,'mini');" id="btn-1" class="btn btn-mini"><span class="simple">Simple<span class="foc">FOC</span>Mini</span> V1.1</a> 

<p class="mini mini-0" ><img src="extras/Images/mini_connection_uno.png" class="width60"></p>
<p class="mini  mini-1" ><img src="extras/Images/miniv11_connection_uno.png" class="width60"></p>

## Encoder 
- Channels `A` and `B` are connected to the Arduino  UNO pins `2` and `3`. 

## Motor
- Motor phases `a`, `b` and `c` are connected directly the motor terminal connector connectors `M1`, `M2` and `M3` of the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> board


<div markdown="1" class="mini mini-0">

## <span class="simple">Simple<span class="foc">FOC</span>Mini</span> v1.0

- The most convenient way of plugging the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> v1.0 board to the Arduino UNO is to stack it on its pins `8-12`.
   - `GND` - `12`
   - `IN1` - `11`
   - `IN2` - `10`
   - `IN3` - `9`
   - `EN` - `8`


<blockquote class="warning" markdown="1">
Pin `12` is not a real ground pin. As no power is transferred through the mini's `GND` pin then we can use pin `12` to approximate the `GND` pin by declaring it as digital output and setting it to `LOW`. This technique might not always work though. If you start seeing that your motor vibrates, even in the open loop control mode, then you should ditch this approach and connect the mini's `GND` pin to the Arduino UNO's `GND` pin. The vibrations should then stop completely.
</blockquote>

</div>

<div markdown="1" class="mini mini-1">

## <span class="simple">Simple<span class="foc">FOC</span>Mini</span> v1.1

- The most convenient way of plugging the <span class="simple">Simple<span class="foc">FOC</span>Mini</span> v1.1 board to the Arduino UNO is to stack it on its pins `9-12`.
   - `GND` - `GND`
   - `EN` - `12`
   - `IN3` - `11`
   - `IN2` - `10`
   - `IN1` - `9`

</div>



### Small motivation :D
<p><img src="extras/Images/mini_gif1.gif" class="width60"></p>


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
First we need to define the `BLDCMotor` class with the  number od pole pairs (`11`)
```cpp
// define BLDC motor
BLDCMotor motor = BLDCMotor(11);
```
<blockquote class="warning">If you are not sure what your pole pairs number is please check the  <code class="highlighter-rouge">find_pole_pairs.ino</code> example.</blockquote>

<div markdown="1" class="mini mini-1">

Next we need to define the `BLDCDriver3PWM` class with the PWM pin numbers of the motor and the driver enable pin
```cpp
// define BLDC driver
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 10, 11, 12);
```

</div>


<div markdown="1" class="mini mini-0">

Next we need to define the `BLDCDriver3PWM` class with the PWM pin numbers of the motor and the driver enable pin
```cpp
// define BLDC driver
BLDCDriver3PWM driver = BLDCDriver3PWM(11, 10, 9, 8);
```

</div>

Then in the `setup()` we configure first the voltage of the power supply if it is not `12` Volts and init the driver.
```cpp
// power supply voltage
// default 12V
driver.voltage_power_supply = 12;
driver.init();
```

<div markdown="1" class="mini mini-0">

Additionally we add the code to declare the pin `12` ad digital output set to `LOW` to be used as a common ground signal. In the `setup` we add
```cpp
pinMode(12,OUTPUT); // declares pin 12 as output and sets it to LOW
```

</div>

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


<div markdown="1" class="mini mini-0">

```cpp
#include <SimpleFOC.h>

// init BLDC motor
BLDCMotor motor = BLDCMotor( 11 );
// init driver
BLDCDriver3PWM driver = BLDCDriver3PWM(11, 10, 9, 8);
//  init encoder
Encoder encoder = Encoder(2, 3, 2048);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// commander interface
Commander command = Commander(Serial);
void onTarget(char* cmd){ command.motion(&motor, cmd); }

void setup() {

  pinMode(12,OUTPUT); // declares pin 12 as output and sets it to LOW

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

<div markdown="1" class="mini mini-1">

```cpp
#include <SimpleFOC.h>

// init BLDC motor
BLDCMotor motor = BLDCMotor( 11 );
// init driver
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 10, 11, 12);
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