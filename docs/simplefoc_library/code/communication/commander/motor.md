---
layout: default
title: Full motor config
nav_order: 4
permalink: /commander_motor
parent: Commander Interface
grand_parent: Communication
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Full motor configuration using the Commander

<img src="extras/Images/motor_cmd.png" class="img150">

When using a standard callback for `BLDCMotor` and `StepperMotor` classes:`commander.motor(&motor,cmd)` the user will have available set of possible commands:

- **Q** - Q current PID controller & LPF (see [pid](commander_pid) and [lpf](commander_lpf) for commands)
  - **P** - proportional gain
  - **I** - integration gain
  - **D** - derivative gain
  - **L** - saturation limit
  - **R** - ramp parameter
  - **F** - low pass filtering time constant
- **D** - D current PID controller & LPF (see [pid](commander_pid) and [lpf](commander_lpf) for commands)
- **V** - Velocity PID controller & LPF  (see [pid](commander_pid) and [lpf](commander_lpf) for commands) 
- **A** - Angle PID controller & LPF-  (see [pid](commander_pid) and [lpf](commander_lpf) for commands)
- **L** - Limits     
  -  **C** - Current  
  -  **U** - Voltage   
  -  **V** - Velocity  
- **R** - Motor phase resistance               
- **S** - Sensor offsets     
  - **M** - sensor offset          
  - **E** - sensor electrical zero             
- **W** - PWM settings     
  - **T** - pwm modulation type         
  - **C** - pwm waveform centering boolean 
- **M** - Monitoring control    
  - **D** - downsample monitoring     
  - **C** - clear monitor        
  - **S** - set monitoring variables  
  - **G** - get variable value        
- **C** - Motion control type config  - [see motion control](commander_target)
  - **D** - downsample motion loop 
  - `0` - torque    
  - `1` - velocity 
  - `2` - angle    
  - `3` - velocity_openloop 
  - `4` - angle_openloop    
- **T** - Torque control type - [see motion control](commander_target)
  - `0` - voltage      
  - `1` - dc_current     
  - `2` - foc_current 
- **E** - Motor status (enable/disable) - [see motion control](commander_target)
  - `0` - enable    
  - `1` - disable  
- **else** - Target setting interface - [see motion control target](commander_target) <br> 
    Depends of the motion control mode:
    - torque mode - torque target (ex. `M2.5`) 
    - velocity (open and closed loop) mode - velocity target and torque limit (ex.`M10 2.5` or `M10` to only change the target without limits)
    - angle (open and closed loop) mode - angle target,  velocity limit,  torque limit (ex.`M3.5 10 2.5` or `M3.5` to only change the target without limits)                 

All built-in commands and subcommands are defined in the library source, in file `src/communication/commands.h`



For example if you have a BLDC motor added to the `commander`:
```cpp
BLDCMotor motor = ....
Commander commander = ...

void onTarget(char* cmd){ commander.target(&motor,cmd); }
void setup(){
  ...
  commander.add('M',onTarget,"my motor motion");
  ...
}
void loop(){
  ...
  commander.run();
}
```

You will be able to configure (set and get) its parameters from serial monitor:
```sh
$ MVP                 # get PID velocity P gain
PID vel| P: 0.20
$ MVP1.2              # set PID velocity P gain
PID vel| P: 1.20
$ MAI                 # get PID angle I gain
PID angle| I: 0.00 
$ MAF                 # get LPF angle time constant 
LPF angle| Tf: 0.00
$ MLV50.4             # set velocity limit
Limits| vel: 50.4
$ MLC                 # get current limit
Limits| curr: 0.5
$ MT                  # get torque control mode
Torque: volt
$ MT1                 # set torque control mode
Torque: dc curr
$ MT2                 # set torque control mode
Torque: foc curr
$ ME                  # get motor status enabled/disabled
Status: 1
$ MSM                 # get sensor offset
Sensor| offset: 0.0
$ MSM1.2              # set sensor offset
Sensor| offset: 1.2
$ MC                  # get motion control mode
Motion: torque
$ MC3                 # set motion control mode
Motion: vel open
$ MC2                 # set motion control mode
Motion: angle
$ MCD100              # get motion control downsampling
Motion: downsample: 100
$ MMG0                # get variable - target
Monitor | target: 0.0
$ MMG1                # get variable - voltage q
Monitor | Vq: 1.4
$ MMG6                # get variable - angle
Monitor | angle: 23.5 
$ MMG6                # get variable - angle
Monitor | angle: 24.6 
$ MMG6                # get variable - angle
Monitor | angle: 25.5 
$ M0                  # set target
Target: 0.0
$ M0.4                # set target
Target: 0.4
$ @1                  # set verbose mode: on_request
Verb | on! 
$ MMG6                # get variable - angle
26.5
$ MMG5                # get variable - velocity
2.57
$ #6                  # set 6 decimal places
Decimal: 6
$ MMG6                # get variable - angle
27.732821
$ @0                  # set verbose mode: nothing
Verb: off!
$ MMG6                # get variable - angle
$ MMG6                # get variable - angle
$ @2                  # set verbose mode: user_friendly
Verb: on!
$ MMG6                # get variable - angle
Monitor | angle: 25.532131 
```

## Motor monitoring control commands
Commander interface enables the user to control the output of the [monitoring](monitoring) functionality. The combination of the two enables user a full control of the motor configuration and tuning as well as full control of variables that are outputted to the user. In order to use his functionality the user needs to enable monitoring for the motor which is really straight-forward:
```cpp
BLDCMotor motor = ....
Commander commander = ...

void onMotor(char* cmd){ commander.motor(&motor,cmd); }
void setup(){
  ...
  motor.useMonitoring(Serial);
  commander.add('M',onMotor,"my motor");
  ...
}
void loop(){
  ...
  motor.monitor();
  commander.run();
}
```
Finally once the motor is added to the commander interface the use will be able to configure the monitoring with commands:
- **M** - Monitoring control    
  - **D** - downsample monitoring     
  - **C** - clear monitor        
  - **S** - set monitoring variables        

Using these commands you can change the downsampling rate (`motor.monitor_downsampling`) of the `monitor()` function that will determine your output sampling frequency. For example if your `loop` time is around 1ms, then with downsampling of monitor function with the rate of 100, it will output the motor variables each 100ms.  
If monitor dowsampling is set to 0  the `monitor()` function is disabled. The same is true if the `motor.monitor_variables` bitmap is empty (equal to `0`). Therefore the command **C** effectively does:
```cpp
// when command MC is called
motor.monitor_variables = 0;
```
Finally the command **MS** is used to get/set the `motor.monitor_variables` bitmap. 

Therefore te communication could look something like this:
```sh
$ MMD                 # get monitor downsampling rate
Monitor | downsample: 10 
$ MMD1000             # set monitor downsampling rate
Monitor | downsample: 1000 
$ MMS                 # get monitor variables
Monitor | 0000000
$ MMS1000001          # set monitor variables (target and angle)
Monitor | 1000001
1.000 0.999
1.000 0.985
1.000 1.064
.....
1.000 1.040
$ MMS0100000          # set monitor variables (voltage q)
Monitor | 0100000
1.234
-0.345
...
0.772
$ MMC                 # clear monitoring variables
Monitor | clear
$ MMS                 # get monitoring variables
Monitor | 0000000
```

<blockquote class="info"><p class="heading">📈 Good practice for visualization</p>
When using monitoring to tune the motion control parameters or just to visualize the different variables it makes sense to disable the commander outputs so that in the serial monitor you only have monitor output. To do that use the mode <code class="highlighter-rouge">VerboseMode::nothing</code> of the commander by sending the command <code class="highlighter-rouge">@0</code>. See all <a href="commander_interface#commander-commands">commander commands</a>.
</blockquote>


## Example code using the motor commands
This is one simple example of using motor commands with monitoring in the code. For more examples browse through the library examples, especially through the `examples/utils/communication_tes/commander` folder.
```cpp
#include <SimpleFOC.h>

// BLDC motor & driver instance
BLDCMotor motor = BLDCMotor(11);
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// encoder instance
Encoder encoder = Encoder(2, 3, 500);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}


// commander interface
Commander command = Commander(Serial);
void onMotor(char* cmd){ command.motor(&motor, cmd); }

void setup() {

  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link driver
  motor.linkDriver(&driver);

  // set control loop type to be used
  motor.controller = MotionControlType::torque;

  // use monitoring with serial for motor init
  // monitoring port
  Serial.begin(115200);
  // comment out if not needed
  motor.useMonitoring(Serial);
  motor.monitor_downsample = 0; // initially disable real-time monitoring

  // initialise motor
  motor.init();
  // align encoder and start FOC
  motor.initFOC();

  // set the inital target value
  motor.target = 2;

  // define the motor id
  command.add('A', onMotor, "motor");

  // Run user commands to configure and the motor (find the full command list in docs.simplefoc.com)
  Serial.println(F("Motor commands sketch | Initial motion control > torque/voltage : target 2V."));
  
  _delay(1000);
}


void loop() {
  // iterative setting FOC phase voltage
  motor.loopFOC();

  // iterative function setting the outter loop target
  motor.move();

  // monitoring
  motor.monitor();
  // user communication
  command.run();
}
```

## *Simple**FOC**Studio* by [@JorgeMaker](https://github.com/JorgeMaker)

SimpleFOCStudio is an awesome application built by [@JorgeMaker](https://github.com/JorgeMaker) which we will try to keep up to date with out library. It is a python application that uses commander interface for tunning and configuring the motor. 

<img src="https://raw.githubusercontent.com/JorgeMaker/SimpleFOCStudio/main/DOC/new_gif.gif" class="width80">

For more info how to install and use this application visit the studio [docs <i class="fa fa-external-link"></i>](studio). 

