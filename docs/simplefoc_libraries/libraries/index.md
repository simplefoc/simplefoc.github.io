---
layout: default
title: libraries
parent: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 1
permalink: /additional_libraries
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
