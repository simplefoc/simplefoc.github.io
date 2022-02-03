---
layout: default
title: Motion control
nav_order: 5
permalink: /commander_target
parent: Commander Interface
grand_parent: Communication
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Advanced motion control using the Commander

Motion control interface in the `Commander` can be integrated in user's application in three ways:
- [Target setting interface](#target-setting-in-one-line) - `commander.target(&motor,cmd)`
    - Set the target value along will all the limits in one command
- [Motion control interface](#motion-control-interface) - `commander.motion(&motor,cmd)`
    - Set the target value along will all the limits in one command
    - Change the [motion control](motion_control) mode
    - Change the [torque control](torque_mode) mode
    - Enable/Disable the motor mode
- [Full configuration interface](commander_motor) - `commander.motor(&motor,cmd)` 
    - All above + full parameter configuration of the motor...



## Target setting in one line
Commander interface enables the user to set the target value for the motion control as well as the limits at once. However different motion control strategies have different numbers of limits (angle, velocity, torque) so depending of the motion control mode used the behavior of the command will change:

This feature is available to the user if any one of the three interfaces is added to the `Commander`.

- `commander.target(&motor,cmd)`
```cpp
...
void onTarget(char* cmd){ commander.target(&motor,cmd); }
void setup(){
  ...
  commander.add('M',onTarget,"target setting");
  ....
}
```

- `commander.motion(&motor,cmd)`
```cpp
...
void onMotion(char* cmd){ commander.motion(&motor,cmd); }
void setup(){
  ...
  commander.add('M',onMotion,"motion control");
  ....
}
```

- `commander.motor(&motor,cmd)`
```cpp
...
void onMotor(char* cmd){ commander.motor(&motor,cmd); }
void setup(){
  ...
  commander.add('M',onMotor,"full motor config");
  ....
}
```

### Torque control mode
If the motor is run in the `MotionControlType::torque` there are no motion control limits associated with this mode and the user can set only the target torque.


**Example serial terminal communication**
```sh
$ ?
M: motion control
$ M1   # target torque1V for voltage mode or 1A for foc_current or dc_current
Target: 1.000
```


### Velocity control mode
If the motor is run in the `MotionControlType::velocity` or `MotionControlType::velocity_openloop` the user can simultaneously set the velocity target and torque (current or voltage) limit.
However, limit setting is optional and the user can also only set the target velocity, the commander will respect the limits that have been set in previous commands or in the code.

**Example serial terminal communication**

```sh
$ ?
M: motion control
$ M1            # target velocity 1rad/s  - using torque limit set earlier or in arduino code
Target: 1.000
$ M5 2          # target velocity 5rad/s and torque limit of 2V in voltage mode or 2A for foc_current or dc_current
Target: 2.000
$ M-5           # target velocity -5rad/s  - using torque limit set in the last command
Target: -5.000
```


### Angle control mode
If the motor is run in the `MotionControlType::angle` or `MotionControlType::angle_openloop` the user can simultaneously set the angle target, velocity limit and torque (current or voltage) limit.
However, limit setting is optional and the user can also only set the target velocity, the commander will respect the limits that have been set in previous commands or in the code.

**Example serial terminal communication**
```sh
$ ?
M: motion control
$ M1            # target angle 1rad/s  - using torque and velocity limits set earlier or in arduino code
Target: 1.000
$ M5 2          # target angle 5rad and velocity limit 2rad/s - using torque limit set earlier or in arduino code
Target: 5.000
$ M50 10 2      # target angle 50rad, velocity limit 10rad/s, torque limit of 2V in voltage mode or 2A for foc_current or dc_current
Target: 50.000
```
## Motion control interface
The motion control interface enables user to control every aspect of the motion of the motor through the `Commander`. The commands the user has at his disposition are:

- **C** - Motion control type config
  - **D** - downsample motion loop 
  - `0` - torque    
  - `1` - velocity 
  - `2` - angle    
  - `3` - velocity_openloop 
  - `4` - angle_openloop    
- **T** - Torque control type
  - `0` - voltage      
  - `1` - dc_current     
  - `2` - foc_current 
- **E** - Motor status (enable/disable)
  - `0` - enable    
  - `1` - disable  
- **else** - Target setting interface - [see motion control target](#target-setting-in-one-line) <br> 
    Depends of the motion control mode:
    - torque mode - torque target (ex. `M2.5`) 
    - velocity (open and closed loop) mode - velocity target and torque limit (ex.`M10 2.5` or `M10` to only change the target without limits)
    - angle (open and closed loop) mode - angle target,  velocity limit,  torque limit (ex.`M3.5 10 2.5` or `M3.5` to only change the target without limits)              

All built-in commands and subcommands are defined in the library source, in file `src/communication/commands.h`



### How to use it?
For example if you have a BLDC motor added to the `commander`:
```cpp
BLDCMotor motor = ....
Commander commander = ...

void onMotion(char* cmd){ commander.motion(&motor,cmd); }
void setup(){
  ...
  commander.add('M',onMotion,"motion control");
  ...
}
void loop(){
  ...
  commander.run();
}
```

You will be able to control the motion of your motor:
```sh
$ ?                   # list commands
M: motion control
$ MC                  # get motion control mode
Motion:angle
$ MT                  # get torque control mode
Torque: volt
$ M10                 # set target angle of 10 rad
Target: 10.000 
$ M-10 40 5           # set target angle of -10 rad, velocity limit at 40rad/s and torque(voltage) limit at 5V
Target: -10.000 
$ MC1                 # set velocity control mode
Motion:vel
$ M10                 # set target velocity of 10 rad/s
Target: 10.000 
$ M-10 3              # set target velocity of 10 rad/s and torque(voltage) limit of 3V
Target: -10.000 
$ M0                  # set target velocity of 0 rad/s
Target: 0.000
$ ME0                 # disable motor
Status: 0
$ ME1                 # enable motor
Status: 1
$ MT2                 # set foc current torque mode
Torque: foc curr
$ MC2                 # set angle control mode
Motion:angle
$ M10 60 0.5          # set target angle of 10 rad, velocity limit at 60rad/s and torque(current) limit at 0.5Amps
Limits| vel: 50.4
$ M0                  # set target angle of 0 rad
Target: 0.000
$ MC0                 # set torque control mode
Motion:torque
$ M0.4                # set target torque 0.4 Amps
```
