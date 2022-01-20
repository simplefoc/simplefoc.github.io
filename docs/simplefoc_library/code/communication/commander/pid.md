---
layout: default
title: PID config
nav_order: 2
permalink: /commander_pid
parent: Commander Interface
grand_parent: Communication
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# PID configuration using the Commander
When using a standard callback for `PIDController` class:`commander.pid(&pid,cmd)` the user will have available set of possible commands:
- **P**: PID controller P gain
- **I**: PID controller I gain
- **D**: PID controller D gain
- **R**: PID controller output ramp
- **L**: PID controller output limit

For example if you have a PID controller added to the `commander`:
```cpp
PIDController pid = ....
Commander commander = ...

void onPid(char* cmd){ commander.pid(&pid,cmd); }
void setup(){
  ...
  commander.add('C',onPid,"my pid");
  ...
}
void loop(){
  ...
  commander.run();
}
```
You will be able to configure (set and get) its parameters from serial monitor:
```sh
$ CP           # get P gain
P: 1.0
$ CD0.05       # set D gain
D: 0.05
$ CO           # unknown command
err
$ CL3.25       # set output limit
limit: 3.25
``` 