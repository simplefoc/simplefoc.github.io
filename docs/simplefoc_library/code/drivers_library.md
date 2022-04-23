---
layout: default
title: SimpleFOC Drivers Library
nav_order: 10
permalink: /drivers_library
parent: Writing the Code
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: False
has_toc: False
---


# SimpleFOC drivers library

![Library Compile](https://github.com/simplefoc/Arduino-FOC-Drivers/workflows/Library%20Compile/badge.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![arduino-library-badge](https://www.ardu-badge.com/badge/Simple%20FOC%20Drivers.svg?)


The <span class="simple">Simple<span class="foc">FOC</span>library</span>'s main objective is to provide an efficient low level motion motor control for BLDC and stepper motors.

There are a vast number of hardware and software options you might choose when implementing a FOC controlled motor system in conjunction - things like whole driver boards, different sensors, integrated driver ICs, different communications protocols and more.

The main library cannot possibly cover all these topics without becoming "overloaded", and hence hard to understand, maintain and use. So our approach is to keep the core library as lean and simple as we can - just the algorithms for motor driving, the PWM support for the different MCUs, and some universal, generic sensor and communications components.

The philosophy is that this will be enough to help you solve your motor control objectives, and there are other sources for the other code needed for your system.

One of these sources is the <span class="simple">Simple<span class="foc">FOC</span></span> drivers library.

## What is it?

A collection of drivers and supporting code you can use with <span class="simple">Simple<span class="foc">FOC</span>library</span>.

## What's in there?

It is steadily growing, but at the moment we have:

- a driver for the DRV8316 integrated 3-phase driver from TI
- various sensor drivers for different magnetic sensor ICs
- a communications driver for I2C

## What's the advantage?

- The drivers in the drivers repo enable functionality for components that don't work with the standard core library. For example, the TLE5012B sensor is not supported by the generic MagneticSensorSPI class, but there is a specific driver for it in the drivers repository.

- The drivers in the drivers repo may have extended functionality. So for example while the AS5048A sensor is supported by the generic MagneticSensorSPI class, the specific driver for it allows querying its other registers, allows detecting sensor erros, and is easier to use because it has the correct settings "embedded".

- Using the comms drivers provided as a starting point not only gets you off to a quick start, but also makes it more likely your solutions will be interoperable with those of others (or at least easy to adapt).

## How can I use it?

```c++
#include "SimpleFOCDrivers.h"
```

It is in the arduino library manager, called "Simple FOC Drivers". Install as normal for arduino libraries in Arduino IDE or PlatformIO.

To use some code from the library, include the specific driver module you want. For example:

```c++
#include "encoders/as5048a/MagneticSensorAS5048A.h"
```

Then check the README for the specific driver for further instructions, or post in the forum or discord if you get stuck!

### GitHub

You can find the source code for the library here: [https://github.com/simplefoc/Arduino-FOC-drivers](https://github.com/simplefoc/Arduino-FOC-drivers).


## Documentation

Each module / driver is documented in its README file that can be found in its respective sub-directory.

The [README](https://github.com/simplefoc/Arduino-FOC-drivers) for the main repository has an index to help you find things.


## How do we decide what goes there?

While working on <span class="simple">Simple<span class="foc">FOC</span> library</span> we sometimes develop code to support our projects that doesn't directly relate to the core library, or receive contributions of code that don't quite fit. We find a home for it in the drivers library.

So the drivers repository is for code that falls into one of these categories:

- hardware-specific, like for a specific sensor or driver chip
- solves problems related to motor control but that aren't needed by everyone using the library
- implements specific communications protocols

The code in the drivers repository is:

- not used by as many users, so less well tested
- not as completely documented as the core library
- probably more suited for experienced users

