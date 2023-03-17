---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 5
permalink: /arduino_simplefoc_utils
has_children: True
has_toc: False
---


# <span class="simple">Simple<span class="foc">FOC</span>utils</span>
In the context of the <span class="simple">Simple<span class="foc">FOC</span>project</span> many different open-source community projects have been developed. 


## Arduino libraries

- ### <span class="simple">Simple<span class="foc">FOC</span>Drivers</span> 

   This library contains an assortment of drivers and supporting code for <span class="simple">Simple<span class="foc">FOC</span>library</span>.


   The intent is to keep the core of <span class="simple">Simple<span class="foc">FOC</span>library</span> clean, and thus easy to maintain, understand and port to different platforms. In addition to this core, there are various drivers and supporting code which has grown around <span class="simple">Simple<span class="foc">FOC</span>library</span>, and which we would like to make available to the community. [Read more ...](drivers_library)

   <a href ="https://github.com/simplefoc/Arduino-FOC-drivers" class="btn"><i class="fa fa-github"></i> Github repo</a>   

- ### <span class="simple">Simple<span class="foc">DC</span>Motor</span>

   <span class="simple">Simple<span class="foc">FOC</span>library</span> is really intended for field oriented control of PMSM/BLDC motors, it's in the name ;-). But for different reasons, sometimes DC motors are preferred, and while we're not focused on this use-case, we do have a fair amount of code that can be leveraged to help with DC-Motor control applications.


   So this represents a less-supported effort to provide some useful building blocks for DC-Motors. [Read more ...](dc_motors_library)

   <a href ="https://github.com/simplefoc/Arduino-FOC-dcmotor" class="btn"><i class="fa fa-github"></i> Github repo</a>   


## User interface applications

- ### <span class="simple">Simple<span class="foc">FOC</span>Studio</span> by [@jorgemaker](https://github.com/JorgeMaker)

   Graphical user interface for the <span class="simple">Simple<span class="foc">FOC</span>library</span> based on python3 and PyQt5. This application allows to tune and configure any BLDC/Stepper  <span class="simple">Simple<span class="foc">FOC</span>library</span> controlled device, using serial port communications and the [Commander](commander_interface) interface. [Read more ... ](studio)

   <a href ="https://github.com/simplefoc/Arduino-FOC-dcmotor" class="btn"><i class="fa fa-github"></i> Github repo</a>   

- ###  <span class="simple">Simple<span class="foc">FOC</span>webcontroller</span> by [@geekuillaume](https://github.com/geekuillaume)

   This is a controller interface for the <span class="simple">Simple<span class="foc">FOC</span>library</span>. It uses WebSerial to communicate with a suitable micro-controller using serial port communications and the [Commander](commander_interface) interface. [Read more ... ](webcontroller)

   <a href ="https://github.com/geekuillaume/simplefoc-webcontroller" class="btn btn"><i class="fa fa-github"></i> Github repo</a> <a href ="https://docs.simplefoc.com/simplefoc-webcontroller/" class="btn btn-primary"><i class="fa fa-github"></i> Open <span class="simple">Simple<span class="foc">FOC</span>webcontroller</span></a>   
- ###  <span class="simple">Simple<span class="foc">FOC</span>Generator</span> by [@stijnsprojects](https://github.com/stijnsprojects)

   A web application which helps you generate <span class="simple">Simple<span class="foc">FOC</span>library</span> arduino sketches based on the hardware that you are using.

   <a href ="https://github.com/stijnsprojects/simplefocgenerator" class="btn btn"><i class="fa fa-github"></i> Github repo</a> <a href ="https://stijnsprojects.github.io/simplefocgenerator/" class="btn btn-primary"><i class="fa fa-github"></i> Open <span class="simple">Simple<span class="foc">FOC</span>Generator</span></a>   

   
