---
layout: default
title: Using the Code
nav_order: 6
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /code
has_children: True
has_toc: False
parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
---

# Let's write the code

Once when you have your <span class="simple">Simple<span class="foc">FOC</span>library</span> [installed](installation) and you have all the necessary [hardware](supported_hardware),we can finally start the fun part, let's white the code and move the motor!

## Step 0. Include the library

Let's start by including the library header file:
```cpp
#include <SimpleFOC.h>
```
Or if you have downloaded the [minimal example](minimal_download) you will include it this way:
```cpp
#include "SimpleFOC.h"
```
The quotation marks `" "` are used when the header files are in the same directory as the Arduino `.ino` file. 

## Step 1. <a href="sensors" class="remove_dec">Position sensor setup</a>

First step when writing the code is initializing and configuring the position sensor.
The library supports these position sensors:
 - [Encoders](encoder): Optical, Capacitive, Magnetic encoders (ABI)
 - [Magnetic sensors](magnetic_sensor): SPI, I2C or Analog
 - [Hall sensors](hall_sensors): 3xHall sonde, Magnetic sensor (UVW interface) 

Choose position sensor to use with this example:

<a href ="javascript:showMagnetic();" id="mag" class="btn btn-primary">Magnetic sensor</a> <a href="javascript:showEncoder();" id="enc" class="btn">Encoder</a> 

```c
#include <SimpleFOC.h>

// Encoder(pin_A, pin_B, PPR)
Encoder sensor = Encoder(2, 3, 2048);
// channel A and B callbacks
void doA(){sensor.handleA();}
void doB(){sensor.handleB();}

 
void setup() {  
  // initialize encoder hardware
  sensor.init();
  // hardware interrupt enable
  sensor.enableInterrupts(doA, doB);

}

void loop() {
  
}
```

```c++
#include <SimpleFOC.h>

// SPI example
// MagneticSensorSPI(int cs, float bit_resolution, int angle_register)
MagneticSensorSPI sensor = MagneticSensorSPI(10, 14, 0x3FFF);

void setup() {
  // initialize magnetic sensor hardware
  sensor.init();
}

void loop() {

}
```

<div id="enc_p" class="hide_p">
Encoders as position sensors are implemented in the class <code class="highlighter-rouge">Encoder</code> and are defined by its:
  <ul>
    <li> <code class="highlighter-rouge">A</code> and <code class="highlighter-rouge">B</code> channel pin numbers: <code class="highlighter-rouge">2</code> and <code class="highlighter-rouge">3</code></li>
    <li> Encoder  <code class="highlighter-rouge">PPR</code> (impulses per revolution number): <code class="highlighter-rouge">2048</code></li>
    <li> <code class="highlighter-rouge">Index</code> pin number <i>(optional)</i> </li>
  </ul> 
</div>

<div id="mag_p" class="hide_p">
In this example we will be using teh setup of a 14 bit magnetic sensor such as <a href="https://www.mouser.fr/ProductDetail/ams/AS5X47U-TS_EK_AB?qs=sGAEpiMZZMve4%2FbfQkoj%252BBDLPCj82ZLyYIPEtADg0FE%3D">AS5047u <i class="fa fa-external-link"></i></a>, connected to the pin <code class="highlighter-rouge">10</code>.<br>
Magnetic sensors using the SPI communication are implemented in the class <code class="highlighter-rouge">MagneticSensorSPI</code>and are defined by its
  <ul>
    <li><code class="highlighter-rouge">chip_select</code> pin: <code class="highlighter-rouge">10</code> </li>
    <li> the overall <code class="highlighter-rouge">CPR</code> of the sensor:   <code class="highlighter-rouge">CPR = 2^14bit =16384</code></li>
    <li> <code class="highlighter-rouge">angle</code> SPI register: <code class="highlighter-rouge">0x3FFF</code></li> 
  </ul>
</div>

Sensor is initialized hardware pins by running `sensor.init()`.

For full documentation of the setup and all configuration parameters please visit the <a href="sensors"> position sensors docs</a>.


## Step 2. <a href="motor_initialization" class="remove_dec"> Motor setup</a>
After the position sensor we proceed to initializing and configuring the motor. The library supports BLDC motors handled by `BLDCMotor` class and stepper motors handled by `StepperMotor` class. `BLDCMotor` class instantiated by providing:
- phase `A`, `B` and `C` pin number
- `pole_pairs` number of the motor
- `enable` pin number *(optional)*

And `StepperMotor` it is defined by setting:
- phase `1A`, `1B` pin number
- phase `2A`, `2B` pin number
- `pole_pairs` number of the motor
- `enable1` and `enable2` pin numbers *(optional)*

In this example we will use BLDC motor:
```cpp
#include <SimpleFOC.h>

//  BLDCMotor( pin_pwmA, pin_pwmB, pin_pwmC, pole_pairs, enable (optional))
BLDCMotor motor = BLDCMotor(9, 5, 6, 11, 8);

// instantiate sensor 

void setup() {  

  // init sensor

  // link the motor to the sensor
  motor.linkSensor(&sensor);

  // set control loop type to be used
  motor.controller = ControlType::velocity;
  // initialize motor
  motor.init();

}

void loop() {

}
```

After the  `BLDCMotor` class instance `motor` has been created we need to link the motor with the sensor `motor.linkSensor()` which will read the motor position and velocity. <br>
The next step is the configuration step, for the sake of this example we will configure only the motion control loop we will be using:
```cpp
// set control loop type to be used
motor.controller = ControlType::velocity;
```
And to finish the `motor` setup we run the `motor.init()` function.

For full documentation of the setup and all configuration parameters please visit the <a href="motor_initialization"> BLDC motor docs</a>.


## Step 3.<a href="motor_initialization#step-4-field-oriented-control-initialization" class="remove_dec"> FOC routine and real-time motion control</a>
Once when we have initialized the position sensor and the BLDC motor, and before we can run the FOC algorithm we need to align the motor and sensor. This is done by calling `motor.initFOC()`. 
After this step we have a functional position sensor, we have configured BLDC motor and our FOC algorithm knows how to set the appropriate voltages based on position sensor measurements.

For the real-time routine of the FOC algorithm we need to add the `motor.loopFOC()` and `motor.move(target)` functions in the Arduino `loop()`.
- `motor.loopFOC()`:  FOC algorithm execution - should be executed as fast as possible `> 1kHz`
- `motor.move(target)`: motion control routine - depends of the `motor.controller` parameter

Here is how it looks in code:

```cpp
#include <SimpleFOC.h>

// instantiate motor
// instantiate senor

void setup() {  
  
  // init senor

  // link motor and sensor
  // configure motor
  // init motor

  // align encoder and start FOC
  motor.initFOC();
}

void loop() {
  // FOC algorithm function
  motor.loopFOC();

  // velocity control loop function
  // setting the target velocity or 2rad/s
  motor.move(2);
}
```

For full documentation of the setup and all configuration parameters please visit the <a href="foc_routine"> FOC algorithm docs</a>.


## Step 4. <a href="monitoring" class="remove_dec"> Monitoring</a>

`BLDCMotor` and `StepperMotor` classes provide monitoring functionality. For enabling the monitoring feature make sure you call `motor.useMonitoring()` with the `Serial` port instance you want to output to. It uses `Serial` class to output motor initialization status during the `motor.init()` function, as well as in `motor.initFOC()` function.

If you are interested to output motors state variables in real-time (even though it will impact the performance - writing the Serial port is slow!) add the `motor.monitor()` function call to the Arduino `loop()` function. 

```cpp
#include <SimpleFOC.h>

// instantiate motor
// instantiate senor

void setup() {  
  
  // init senor

  // link motor and sensor
  // configure motor

  // use monitoring with the BLDCMotor
  Serial.begin(115200);
  // monitoring port
  motor.useMonitoring(Serial);
  
  // init motor
  
  // align encoder and start FOC
}

void loop() {
  
  // FOC execution
  // motion control loop

  // monitoring function outputting motor variables to the serial terminal 
  motor.monitor();
}
```
For full documentation of the setup and all configuration parameters please visit the <a href="monitoring"> Monitoring docs</a>.


## Step 4. <a href="communication" class="remove_dec"> Motor Commands</a>

Finally in order to configure the control algorithm, set the target values and get the state variables in the user-friendly way (not just dumping as using `motor.monitor()`)  Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>  provides you with the communication interface in a form of motor commands.Very similar to G code.

The communication interface is implemented in the `motor.command()` function. It receives `String` with the user command. 

The following code is one basic implementations of the full communication interface with the user:

```cpp
#include <SimpleFOC.h>

// instantiate motor
// instantiate senor

void setup() {  
  
  // init senor

  // link motor and sensor
  // configure motor

  // use monitoring
  Serial.begin(115200);
  // monitoring port
  motor.useMonitoring(Serial);
  
  // init motor
  
  // align encoder and start FOC
}

void loop() {
  
  // FOC execution
  // motion control loop
  
  
  // user communication
  motor.command(serialReceiveUserCommand());
}

// utility function enabling serial communication the user
String serialReceiveUserCommand() {
  
  // a string to hold incoming data
  static String received_chars;
  
  String command = "";

  while (Serial.available()) {
    // get the new byte:
    char inChar = (char)Serial.read();
    // add it to the string buffer:
    received_chars += inChar;

    // end of user input
    if (inChar == '\n') {
      
      // execute the user command
      command = received_chars;

      // reset the command buffer 
      received_chars = "";
    }
  }
  return command;
}

```
For full documentation of the setup and all configuration parameters please visit the <a href="communication"> Communication docs</a>. 


<script type="text/javascript">
    hideClass('language-c');
    document.getElementById("enc_p").style.display = "none";

    function showMagnetic(){
        document.getElementById("enc").classList.remove("btn-primary");
        document.getElementById("mag").classList.add("btn-primary");
        hideClass('language-c');
        showClass('language-c++');
        hideClass('hide_p');
        document.getElementById("mag_p").style.display = "block";


        return 0;
    }

    function showEncoder(){
        document.getElementById("mag").classList.remove("btn-primary");
        document.getElementById("enc").classList.add("btn-primary");
        showClass('language-c');
        hideClass('language-c++');
        hideClass('hide_p');
        document.getElementById("enc_p").style.display = "block";

        return 0;
    }

  function hideClass(class_name){
    var elems = document.getElementsByClassName(class_name);
    for (i = 0; i < elems.length; i++) {
        elems[i].style.display = "none";
    }
  }
  function showClass(class_name){
    var elems = document.getElementsByClassName(class_name);
    for (i = 0; i < elems.length; i++) {
        elems[i].style.display = "block";
    }
  }

</script>


## 🎨 Full interactive sketch example 

Now when you have learned what are all parts the parts of the Arduino program and what are they for check this interactive example to better understand how to integrate all these parts together.

This is the code of the library example `motor_full_control_serial_examples/magnetic_sensor/full_control_serial.ino`. Here is a quick overview of all the library examples [link](library_examples)

### Toggle different parts of the code 
<div style="width:100%;margin-bottom:0px">
<a href ="javascript:show('mag_s');" id="mag_s" class="btn btn-primary">Magnetic sensor</a>
<a href="javascript:show('mot_s');" id="mot_s" class="btn">BLDC Motor</a> 
<a href="javascript:show('foc_s');" id="foc_s" class="btn">FOC routine</a> 
<a href="javascript:show('mon_s');" id="mon_s" class="btn">Monitoring</a> 
<a href="javascript:show('com_s');" id="com_s" class="btn">Communication</a> 
<a href="javascript:show('conf_s');" id="conf_s" class="btn">Configuration</a> 
<div>
<div class="language-cpp highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="cp">#include &lt;SimpleFOC.h&gt;
</span><span class="mag_s">
<span class="c1">// magnetic sensor instance</span>
<span class="n">MagneticSensorSPI</span> <span class="n">AS5x4x</span> <span class="o">=</span> <span class="n">MagneticSensorSPI</span><span class="p">(</span><span class="mi">10</span><span class="p">,</span> <span class="mi">14</span><span class="p">,</span> <span class="mh">0x3FFF</span><span class="p">);</span></span>
<span class="mot_s">
<span class="c1">// motor instance</span>
<span class="n">BLDCMotor</span> <span class="n">motor</span> <span class="o">=</span> <span class="n">BLDCMotor</span><span class="p">(</span><span class="mi">9</span><span class="p">,</span> <span class="mi">5</span><span class="p">,</span> <span class="mi">6</span><span class="p">,</span> <span class="mi">11</span><span class="p">,</span> <span class="mi">8</span><span class="p">);</span>
</span>
<span class="kt">void</span> <span class="nf">setup</span><span class="p">()</span> <span class="p">{</span>
<span class="mag_s">
  <span class="c1">// initialize magnetic sensor hardware</span>
  <span class="n">AS5x4x</span><span class="p">.</span><span class="n">init</span><span class="p">();</span></span>
  <span class="mot_s"><span class="c1">// link the motor to the sensor</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">linkSensor</span><span class="p">(</span><span class="o">&amp;</span><span class="n mag_s">AS5x4x</span><span class="n enc_s">encoder</span><span class="p">);</span></span>
<span class="conf_s">
  <span class="c1">// choose FOC modulation</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">foc_modulation</span> <span class="o">=</span> <span class="n">FOCModulationType</span><span class="o">::</span><span class="n">SpaceVectorPWM</span><span class="p">;</span>

  <span class="c1">// power supply voltage [V]</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">voltage_power_supply</span> <span class="o">=</span> <span class="mi">12</span><span class="p">;</span>
</span><span class="conf_s">
  <span class="c1">// set control loop type to be used</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">controller</span> <span class="o">=</span> <span class="n">ControlType</span><span class="o">::</span><span class="n">voltage</span><span class="p">;</span>

  <span class="c1">// controller configuration based on the control type </span>
  <span class="n">motor</span><span class="p">.</span><span class="n">PID_velocity</span><span class="p">.</span><span class="n">P</span> <span class="o">=</span> <span class="mf">0.2</span><span class="p">;</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">PID_velocity</span><span class="p">.</span><span class="n">I</span> <span class="o">=</span> <span class="mi">20</span><span class="p">;</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">PID_velocity</span><span class="p">.</span><span class="n">D</span> <span class="o">=</span> <span class="mi">0.001</span><span class="p">;</span>
  <span class="c1">// default voltage_power_supply</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">voltage_limit</span> <span class="o">=</span> <span class="mi">12</span><span class="p">;</span>

  <span class="c1">// velocity low pass filtering time constant</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">LPF_velocity</span><span class="p">.</span><span class="n">Tf</span> <span class="o">=</span> <span class="mf">0.01</span><span class="p">;</span>

  <span class="c1">// angle loop controller</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">P_angle</span><span class="p">.</span><span class="n">P</span> <span class="o">=</span> <span class="mi">20</span><span class="p">;</span>
  <span class="c1">// angle loop velocity limit</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">velocity_limit</span> <span class="o">=</span> <span class="mi">50</span><span class="p"></span>
</span><span class="com_s mon_s">
  <span class="c1">// use monitoring with serial for motor init</span>
  <span class="c1">// monitoring port</span>
  <span class="n">Serial</span><span class="p">.</span><span class="n">begin</span><span class="p">(</span><span class="mi">115200</span><span class="p">);</span>
  <span class="c1">// comment out if not needed</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">useMonitoring</span><span class="p">(</span><span class="n">Serial</span><span class="p">);</span>
</span><span class="mot_s">
  <span class="c1">// initialize motor</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">init</span><span class="p">();</span></span>
  <span class="foc_s"><span class="c1">// align encoder and start FOC</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">initFOC</span><span class="p">();</span></span>
<span class="foc_s">
  <span class="c1 ">// set the initial target value</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">target</span> <span class="o">=</span> <span class="mi">2</span><span class="p">;</span></span>

<span class="com_s">
  <span class="n">Serial</span><span class="p">.</span><span class="n">println</span><span class="p">(</span><span class="s">"Full control example: "</span><span class="p">);</span>
  <span class="n">Serial</span><span class="p">.</span><span class="n">println</span><span class="p">(</span><span class="s">"Run user commands to configure and the motor (find the full command list in docs.simplefoc.com) </span><span class="se">\n</span><span class="s"> "</span><span class="p">);</span>
  <span class="n">Serial</span><span class="p">.</span><span class="n">println</span><span class="p">(</span><span class="s">"Initial motion control loop is voltage loop."</span><span class="p">);</span>
  <span class="n">Serial</span><span class="p">.</span><span class="n">println</span><span class="p">(</span><span class="s">"Initial target voltage 2V."</span><span class="p">);</span></span>
  
  <span class="n">_delay</span><span class="p">(</span><span class="mi">1000</span><span class="p">);</span>
<span class="p">}</span>


<span class="kt">void</span> <span class="nf">loop</span><span class="p">()</span> <span class="p">{</span>
  <span class="foc_s"><span class="c1">// iterative setting FOC phase voltage</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">loopFOC</span><span class="p">();</span></span>

  <span class="foc_s"><span class="c1">// iterative function setting the outer loop target</span>
  <span class="c1">// velocity, position or voltage</span>
  <span class="c1">// if target not set in parameter uses motor.target variable</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">move</span><span class="p">();</span></span>

  <span class="c1 mon_s com_s_hide">// output motor state variables to the monitoring port (Serial)</span>
  <span class="mon_s com_s_hide"><span class="n">motor</span><span class="p">.</span><span class="n">monitor</span><span class="p">();</span></span> 
  
  <span class="com_s"><span class="c1">// user communication</span>
  <span class="n">motor</span><span class="p">.</span><span class="n">command</span><span class="p">(</span><span class="n">serialReceiveUserCommand</span><span class="p">());</span></span>
<span class="p">}</span>
<span class="com_s">
<span class="c1">// utility function enabling serial communication the user</span>
<span class="n">String</span> <span class="nf">serialReceiveUserCommand</span><span class="p">()</span> <span class="p">{</span>
  
  <span class="c1">// a string to hold incoming data</span>
  <span class="k">static</span> <span class="n">String</span> <span class="n">received_chars</span><span class="p">;</span>
  
  <span class="n">String</span> <span class="n">command</span> <span class="o">=</span> <span class="s">""</span><span class="p">;</span>

  <span class="k">while</span> <span class="p">(</span><span class="n">Serial</span><span class="p">.</span><span class="n">available</span><span class="p">())</span> <span class="p">{</span>
    <span class="c1">// get the new byte:</span>
    <span class="kt">char</span> <span class="n">inChar</span> <span class="o">=</span> <span class="p">(</span><span class="kt">char</span><span class="p">)</span><span class="n">Serial</span><span class="p">.</span><span class="n">read</span><span class="p">();</span>
    <span class="c1">// add it to the string buffer:</span>
    <span class="n">received_chars</span> <span class="o">+=</span> <span class="n">inChar</span><span class="p">;</span>

    <span class="c1">// end of user input</span>
    <span class="k">if</span> <span class="p">(</span><span class="n">inChar</span> <span class="o">==</span> <span class="sc">'\n'</span><span class="p">)</span> <span class="p">{</span>
      
      <span class="c1">// execute the user command</span>
      <span class="n">command</span> <span class="o">=</span> <span class="n">received_chars</span><span class="p">;</span>

      <span class="c1">// reset the command buffer </span>
      <span class="n">received_chars</span> <span class="o">=</span> <span class="s">""</span><span class="p">;</span>
    <span class="p">}</span>
  <span class="p">}</span>
  <span class="k">return</span> <span class="n">command</span><span class="p">;</span>
<span class="p">}</span>

</span>

</code></pre></div></div>


<script type="text/javascript">
    var elems = document.getElementsByClassName('com_s');
    for (i = 0; i < elems.length; i++) {
        elems[i].style.display = "none";
    }
    var elems = document.getElementsByClassName('mon_s');
    for (i = 0; i < elems.length; i++) {
        elems[i].style.display = "none";
    }
    var elems = document.getElementsByClassName('conf_s');
    for (i = 0; i < elems.length; i++) {
        elems[i].style.display = "none";
    }
    var elems = document.getElementsByClassName('foc_s');
    for (i = 0; i < elems.length; i++) {
        elems[i].style.display = "none";
    }
    var elems = document.getElementsByClassName('mot_s');
    for (i = 0; i < elems.length; i++) {
        elems[i].style.display = "none";
    }
    var elems = document.getElementsByClassName('enc_s');
    for (i = 0; i < elems.length; i++) {
        elems[i].style.display = "none";
    }

    function show(str){
        var hide = document.getElementById(str).classList.contains("btn-primary");

        if(hide){
            document.getElementById(str).classList.remove("btn-primary");
            var elems = document.getElementsByClassName(str);
            for (i = 0; i < elems.length; i++) {
                elems[i].style.display = "none";
            }  
        }else{
            document.getElementById(str).classList.add("btn-primary");
            var elems = document.getElementsByClassName(str);
            for (i = 0; i < elems.length; i++) {
                elems[i].style.display = "inline";
            } 
            var elems = document.getElementsByClassName(str+"_hide");
            for (i = 0; i < elems.length; i++) {
                elems[i].style.display = "none";
            } 
        }
        return 0;
    }
</script>



<h2> Library source code</h2>
If you are interested in extending and adapting the <span class="simple">Simple<span class="foc">FOC</span>library</span> source code you can find full documentation on <a href="source_code">library source docs</a>