---
layout: default
title:  Simple<b>FOC</b>WebController
nav_order: 3
permalink: /webcontroller
parent: tools
grand_parent: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
---

#  *Simple**FOC**WebController*  <small>by [@geekuillaume](https://github.com/geekuillaume) </small>

This is a controller interface for the <span class="simple">Simple<span class="foc">FOC</span>library</span>. It uses WebSerial to communicate with a suitable micro-controller using serial port communications and the [Commander](commander_interface) interface.

Most of the code for this application was provided by [@geekuillaume](https://github.com/geekuillaume), his github repository can be found [here](https://github.com/geekuillaume/simplefoc-webcontroller) and his application can be found [here](https://simplefoc.besson.co/). 
Our app is a fork of his code, that has been further extended to support more devices and configuration parameters, as well as to ease the monitoring visualisation. 

<a href ="https://github.com/geekuillaume/simplefoc-webcontroller" class="btn btn"><i class="fa fa-github"></i> Github repo</a> <a href ="https://docs.simplefoc.com/simplefoc-webcontroller/" class="btn btn-primary"><i class="fa fa-github"></i> Open <span class="simple">Simple<span class="foc">FOC</span>webcontroller</span></a>   



<img  src="extras/Images/webcontroller.gif" >

<blockquote class="info">
<p class="heading">📢 Early stage project</p>
This project is still in its early stage, we are counting on your patience and looking forward to hear your feedback. As there are many people in the community with far more knowledge about these kinds of apps than we do, don't hesitate to leve the issues and do the pull requests. 
</blockquote>

### Features:
- Plug and play with the *Simple**FOC**library* version 2.3+
- Real-time tuning and configuration of the motors
- Real-time plotting and monitoring of motor variables
- Support for multiple motors


## Using the *Simple**FOC**WebController*

<a href ="https://docs.simplefoc.com/simplefoc-webcontroller/" class="btn btn-primary"><i class="fa fa-github"></i> Open <span class="simple">Simple<span class="foc">FOC</span>webcontroller</span></a>  

*Simple**FOC**WebController* does not require any installation, its only requirement is that your browser has `WebSerial` support. Check the supportedd browsers [here](https://caniuse.com/web-serial).

### Motion control tunning  blocks
Once you have your application running in your browser, connected to your microcontroller running the <span class="simple">Simple<span class="foc">FOC</span>library</span>. You can easily change most of the control parameters of different motion control loops and visualise different monitored variables.

<img src="extras/Images/webcontroller_motor.png"  class="width80">

### Integrated serial terminal

*Simple**FOC**WebController* also has integrated serial terminal for easier debugging and monitoring.

<img  src="extras/Images/webcontroller_init.png" class="width80">


## Arduino code
Basically there are three things you need to do:
1. Use the commander interface and add the motor to the commander
2. Use the monitoring and add the `motor.monitor()` in the loop
3. Make set the `motor.monitor_start_char` and `motor.monitor_end_char` to the same character as the motor id added to the commander

Here is a mockup of the code:

```cpp
#include <SimpleFOC.h>

....

// include commander interface
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup(){
  ....
  // add the motor to the commander interface
  // The letter id (here 'M') of the motor
  char motor_id = 'M';
  command.add(motor_id,doMotor,'motor');
  // tell the motor to use the monitoring
  motor.useMonitoring(Serial);
  // configuring the monitoring to be well parsed by the webcontroller
  motor.monitor_start_char = motor_id; // the same latter as the motor id in the commander 
  motor.monitor_end_char = motor_id; // the same latter as the motor id in the commander 

  commander.verbose = VerboseMode::machine_readable; // can be set using the webcontroller - optional
  ...

}
void loop(){
  ....

  ....
  // real-time monitoring calls
  motor.monitor();
  // real-time commander calls
  command.run();
}
```