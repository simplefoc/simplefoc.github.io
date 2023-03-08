---
layout: default
title: Scalar variable
nav_order: 1
permalink: /commander_scalar
parent: Commander Interface
grand_parent: Communication
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Scalar variables using the Commander


Commander interface has a built-in feature for setting/getting a scalar variable.  To use it with a desired variable (ex. `variable`) use the callback `commander.scalar(&variable,cmd)`. 

For example if you have a variable added to the `commander`:
```cpp

Commander commander = ...

// define my variable
float my_variable = 1.234;

void onScalar(char* cmd){ commander.scalar(&my_variable,cmd); }
void setup(){
  ...
  commander.add('A',onScalar,"my variable");
  ...
}
void loop(){
  ...
  commander.run();
}
```

You will be able to configure (set and get) it from serial monitor:
```sh
$ ?           # list available commands 
A: my variable
$ A           # get time constant
1.234
$ A0.05       # set time constant
0.05
$ A           # get time constant
0.05
``` 
