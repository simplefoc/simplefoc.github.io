---
layout: default
title: <span class="simple">Simple<span class="foc">DC</span>Motor</span>
nav_order: 1
permalink: /dc_motors_library
parent: libraries
grand_parent: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
has_children: False
has_toc: False
---


# <span class="simple">Simple<span class="foc">DC</span> Motor</span> library

![Library Compile](https://github.com/simplefoc/Arduino-FOC-dcmotor/workflows/Library%20Compile/badge.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![arduino-library-badge](https://www.ardu-badge.com/badge/SimpleDCMotor.svg?)


The <span class="simple">Simple<span class="foc">DC</span>Motor library</span> extends  <span class="simple">Simple<span class="foc">FOC</span> library to handle DC motors via their commonly used driver types. 

## What is it?

A collection of `DCDriver` classes and a `DCMotor` class that build on <span class="simple">Simple<span class="foc">FOC</span>library</span>.

## What's in there?

- `DCMotor` class
- `DCDriver` classes for different DC motor driver types
- Some examples how to use them

## What's the advantage?

- The core library doesn't work with DC motors, that's not its purpose. This library adds DC motor functionality.

- This lets you use the many sensor drivers available in SimpleFOC, as well as helpful classes like the Commander, when working with DC motors also.

- And it allows you to use SimpleFOC's control architecture to use closed loop control, so in combination with a sensor, you can turn even a cheap DC motor into an accurate digital servo.

## How can I use it?

```cpp
#include "SimpleDCMotor.h"
```

It is in the arduino library manager, called "SimpleDCMotor". Install as normal for arduino libraries in Arduino IDE or PlatformIO.

Please see the readme file on github and the library examples for how to use it.

### GitHub

You can find the source code for the library here: [https://github.com/simplefoc/Arduino-FOC-dcmotor](https://github.com/simplefoc/Arduino-FOC-dcmotor).


## Documentation

Please see the documentation on github. Note that there is additional documentation in the `drivers` subdirectory describing the individual DC motor drivers.



