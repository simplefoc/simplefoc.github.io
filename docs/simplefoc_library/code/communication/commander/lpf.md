---
layout: default
title: LPF config
nav_order: 3
permalink: /commander_lpf
parent: Commander Interface
grand_parent: Communication
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Low pass filter configuration using the Commander
When using a standard callback for `LowPassFilter` class:`commander.lpf(&lpf,cmd)` the user will have available a command:
- **F**: Low pass filter time constant

For example if you have a low pass filter added to the `commander`:
```cpp
LowPassFilter filter = ....
Commander commander = ...

void onLpf(char* cmd){ commander.lpf(&filter,cmd); }
void setup(){
  ...
  commander.add('A',onLpf,"my lpf");
  ...
}
void loop(){
  ...
  commander.run();
}
```
You will be able to configure (set and get) its parameters from serial monitor:
```sh
$ AF           # get time constant
Tf: 1.0
$ AF0.05       # set time constant
Tf: 0.05
$ AW           # unknown command
err
``` 
