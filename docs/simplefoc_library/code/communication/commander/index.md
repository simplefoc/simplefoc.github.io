---
layout: default
title: Commander Interface
nav_order: 1
permalink: /commander_interface
parent: Communication
grand_parent: Writing the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: true
has_toc: false
---

# Commander interface

Commander is a simple and flexible interface monitoring, supervision, configuration and control using the G-code like communication protocol. The communication is based on `ASCII` character command ids which makes simple and efficient to parse on any MCU. After the command id has been received the function attached to this command is called and provided the remaining string of characters received which follows the command character. 

<img src="extras/Images/cmd_motor.gif" class="img100">
<img src="extras/Images/cmd_motor_get.gif" class="img100">

This g-code like interface provides callback to configure and tune any:
- [PID controllers](commander_pid)
- [Low pass filters](commander_lpf)
- [Scalar variables](commander_scalar)
- [Motion control](commander_target) <b><i>NEW</i>📢</b>
  - Setting target values and limits at once (ex. angle velocity torque)
  - Changing the motion and torque control mode
  - Enable/Disable the motor 
- [Fully integrated configuration](commander_motor) for BLDC or Stepper motors
  - PID controllers
  - Low pass filters
  - Motion control
  - monitoring
  - limits
  - enable/disable
  - sensor offsets
  - phase resistance 
  - ... 

Furthermore commander enables you to easily create your own commands and extend this interface in any way you might need for your particular application.
Here is the link to the docs about how to [make your custom commands.](commander_custom)

## What happens when user sends a command?
When the commander received the string:

<img src="extras/Images/cmd1.png" class="width20">

It first checks the command id, identifies its `M` and sends the remaining string to the motor handling callback. Then the motor callback checks what is the coommand id, finds `V` and sends the remaining string to the PID velocity callbacK. Then the PID velocity callback scans the command id and finds it is the `D`, so derivative gain and sets the value.

Commander | Motor callback (cmd id `M` )  | PID callback (cmd id `V` ) 
--- | ---| ---
<img src="extras/Images/cmd2.png" > | <img src="extras/Images/cmd3.png" > | <img src="extras/Images/cmd4.png" >

The other example is if the commander receives:

<img src="extras/Images/cmd5.png" class="width20">

First id that it finds is `O`, which is for example motor. It calls the callback that is assigned to this command (which is in this case motor callback) with the string remaining string. Then the motor callback finds the command `E` and knows its the status (enabled/disabled) either getting or getting. It checks the value and sees that the value is empty, which means the user has sent a get request. 

Commander | Motor callback (cmd id `O` ) 
--- | ---
<img src="extras/Images/cmd6.png" class="img100"> | <img src="extras/Images/cmd7.png" class="img100"> 


## Using the commander interface
Command interface is implemented in the `Commander` class.
```cpp
// Commander interface constructor
// - serial  - optionally receives HardwareSerial/Stream instance
// - eol     - optionally receives eol character - by default it is the newline: "\n" 
// - echo    - option echo last typed character (for command line feedback) - defualt false
Commander commander = Commander(Serial, "\n", false);
```
The end of line (eol) character is an optional input of the `Commander` class which represents end of command character. User can define its own end of command characters here, but by default the character used is newline character `\n`. For example i

<blockquote class="warning"><p class="heading">BEWARE: EOL characters</p> Different operating systems have different EOL characters by default. Newline character is probably the most common one but there is also the carriage return '\r' for linux users. Be sure to provide it to the constructor of the Commander class if you wish to use it with your setup!</blockquote>

The echo flag can be used as a debugging feature but it is not recommended to be used for real time motor control and configuration!

Next step would be to add the commander function that reads the `Serial` instance that you provided into the Arduino `loop()`:
```cpp
void loop(){
  ...
  commander.run(); // reads Serial instance form constructor
}
```

If you did not provide the `Serial` instance to the `Commander` constructor you can provide it to the `run()` function. 
```cpp
void loop(){
  ...
  commander.run(Serial); // reads Serial instance form run
}
```
Or, if you wish to use the commander without `Serial` and using just string variables then you can provide and `char*` variables to the `run()` function:
```cpp
char* my_string = "user command";
commander.run(my_string); // reads the string
```

<blockquote class="warning"><p class="heading"> Serial output</p>
The <code class="highlighter-rouge">Commander</code> class will always try to print the output to the serial instance provided in the constructor. If it did not receive one in the constructor, then it will use the one provided in the <code class="highlighter-rouge">run()</code> function. If it does not any of the two, it will not output anywhere, but the user can still use it.</blockquote>

### Configuration 
Commander class has two configuration parameters:
- `verbose` - Serial output mode
- `decimal_places` - Number of decimal places for floating point numbers 

Number of decimal places for floating point numbers can be changed easily by setting the parameter `decimal_places`:
```cpp
commander.decimal_places = 4; // default 3
```

Serial output mode can be easily changed by setting the parameter `verbose`
```cpp
// VerboseMode::nothing        - display nothing - good for monitoring
// VerboseMode::on_request     - display only on user request
// VerboseMode::user_friendly  - display textual messages to the user (default)
commander.verbose = VerboseMode::user_friendly;
```

There are three types of output modes:
-  `VerboseMode::nothing` - this mode does not output anything to the serial terminal - it is very useful when `Commander` is used in combination with [monitoring](monitoring) to avoid unkonwn values in the Arduino's Serial Plotter for example
- `VerboseMode::on_request` - this mode outputs only there resutls of get and set commands and will not output any additional unnecessary (human readable) text.
- `VerboseMode::user_friendly` - this mode is the default mode and is intended for the cases when it is the user who sends the commands using the serial monitor. This mode will in addition to all the necessary get and set values output additional text for easier comprehension for human user.

### Adding commands
In order to add the callback for a given command character to the `Commander` you will need to call the function `add()` that receives the command character, the function pointer and the commands label:
```cpp
// creating the command A in the commander
// - command id - character
// - callback   - function pointer - void callback(char* cmd)
// - label      - label of the command (optional) 
commander.add('A',doSomething,"do something");
```
The only real requirement for the type of the function you can use as the callback is that hey need to return `void` and they have to receive `char*` string:
```cpp
void doSomething(char* cmd){ ... }
```
With this simple interface you can create your own commands very simply and subscribe them to the `Commander` using just one line of code.

In addition to this flexible interface for adding generic callbacks the `Commander` class additionally implements standardized callbacks for:
- BLDC (`BLDCMotor`) or Stepper (`StepperMotor`) motor  - `commander.motor(&motor, cmd)` - [see more](commander_motor)
- PID controller (`PIDController`) - `commander.pid(&pid, cmd)` - [see more](commander_pid)
- Low pass filter (`LowPassFilter`) - `commander.lpf(&lpf, cmd)` - [see more](commander_lpf)
- Any numeric variable (`float`) - `commander.scalar(&variable, cmd)` - [see more](commander_scalar)
- Target setting control (`BLDCMotor` or `StepperMotor`) - `commander.target(&motor, cmd)` - [see more](commander_target)
- Full motion control (`BLDCMotor` or `StepperMotor`) - `commander.motion(&motor, cmd)` - [see more](commander_target)


For example if you are interested in full configuration of one `motor` your code could look something like this:
```cpp
BLDCMotor motor = .....
Commander commander = ....

// defined wrapper for generic callback
void onMotor(char* cmd){commander.motor(&motor, cmd);}

void setup(){
  ...
  commander.add('m',onMotor,"my motor");
  ...
}
void loop(){
  ...
  commander.run();
}
```
Or maybe you wish to tune the velocity PID and you and change the target value of the motor and you wish to remove unnecessary memory overhead due to the other functionalities you do nto necessarily need, then your code could look something like:
```cpp
BLDCMotor motor = .....
Commander commander = ....

// defined wrappers for generic callbacks
void onPid(char* cmd){commander.pid(&motor.PID_velocity, cmd);}
void onLpf(char* cmd){commander.lpf(&motor.LPF_velocity, cmd);}
void onTarget(char* cmd){commander.target(&motor, cmd);}

void setup(){
  ...
  commander.add('C',onPid,"PID vel");
  commander.add('L',onLpf,"LPF vel");
  commander.add('T',onTarget,"target vel (+ torque limit)");
  ...
}
void loop(){
  ...
  commander.run();
}
```

This simple interface provides the user a simple way to make communicate and configure  multiple motors, PID controllers, low pass filters, scalar variables and custom commands in the same time if necessary. 
It also makes the tuning of the custom control loops much easier since you can close the loop with a pid controller `PIDController` very easily and just add it to the commander to tune it in real time. 

You can find more examples in library examples `examples/utils/communication_test/commander` folder.
## Commander commands
When using the `Commander` in your program the user will have three built-in default commands he can use:
- `?` - list all the commands available
- `#` - get/set decimal point number
  - Examples:
    - get decimal places `#`
    - set 5 decimal places: `#5`
- `@` - get/set verbose output mode
  - Examples:
    - get mode: `@`
    - set user frinedly mode : `@3`
    - set noting mode : `@0`
    - set on request mode : `@1`

The list command `?` will display all the commands that were added to the `Commander` and their labels. For example if we have the added commands like these ones:
```cpp
void setup(){
  ...
  commander.add('M',doSomeMotor,"some motor");
  commander.add('P',doSomePID,"some pid");
  commander.add('R',doSomeOtherMotor,"some other motor");
  ...
}
```
Here is the example of the output of the list `?` command in *user-friendly* mode:
```sh
$ ?
M: some motor
P: some pid
R: some other motor
``` 


## List of available commands

All built-in commands and subcommands are defined in the library source, in file `src/communication/commands.h`.
If you wish to change the character id of a certain command that is the place to do it. 😄

In general we can separate the commands into:
- [Commander commands](#commander-commands) - commands specific for the `Commander` class
- [PID commands](commands_pid)  - commands specific for the `PIDController` class 
- [Low pass filter commands](command_lpf) - commands specific for the `LowPassFilter` class
- [Motor commands](command_motor) - commands specific for the `FOCMotor` classes

When adding the `scalar` variable to the commander or the motion control `target` the only command letter used is the one provided to the `commander.add`. 
- [Scaler variable](commander_scalar) - adding the scalar `float` variable
- [Motion control and target setting](commander_target) - setting the target for the `FOCMotor` classes

Commander provides a very simple way to extend the command list and implement new ones
- [Custom commands](commander_custom) - create your own callbacks


## Commander with Serial Monitor in Arudino IDE


Once the commander interface is added to the code you will be able to communicate to it using the Serial Monitor of the Arduino IDE

<img src="extras/Images/commander.png">

Commander paramters in the serial monitor are the same as for every other Arduino code working with the `Serial`. 
Make sure to:
- Set the baudrate number the same as in the `ino` file : for examle if in the `ino` file you have `Serial.begin(115200)`, the baud rate should be `115200`
- Make sure to set the termination character to `newline`

## *Simple**FOC**Studio* by [@JorgeMaker](https://github.com/JorgeMaker)

SimpleFOCStudio is an awesome application built by [@JorgeMaker](https://github.com/JorgeMaker) which we will try to keep up to date with out library. It is a python application that uses commander interface for tunning and configuring the motor. 

<img src="https://raw.githubusercontent.com/JorgeMaker/SimpleFOCStudio/main/DOC/new_gif.gif" class="width80">

For more info how to install and use this application visit the studio [docs <i class="fa fa-external-link"></i>](studio). 

