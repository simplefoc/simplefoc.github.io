---
layout: default
title: FOC Current Control
nav_order: 8
description: "Sensor-based current control with hardware current sensing"
permalink: /foc_current
parent: Getting Started Guide
grand_parent: Writing the Code
great_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
has_children: true
---

# FOC Current Control (Guide)

FOC current control provides **precise torque control using current sensing**. This is the most accurate current control method and enables advanced features like current limiting, automatic compensation of Back-EMF, and cross-coupling compensation.

[Read more about FOC current control](foc_current_mode){: .btn .btn-docs}

## What you need

Before enabling FOC current control, you need:

1. **Current sensing hardware** - Inline or lowside current sensors - **REQUIRED**
2. **Working motor setup** - already tested and working
    - Sensor - [Test Your Sensor](test_sensor)
    - Driver - [Test Your Driver](test_driver)
    - Closed-loop control - [Test Closed-Loop Control](test_closedloop)

<blockquote class="warning" markdown="1"> If you don't have current sensors, see the - [Estimated Current Control Guide](estimated_current_guide){: .btn .btn-docs-middle} 

</blockquote>

## Guide steps

This guide follows a 2-step process:

1. **Setup and verify current sensing** - Configure hardware and validate current measurements<br>
[Start with step 1: Setup and verify current sensing](test_current){:.btn .btn-step}
2. **Enable full current control** - Switch from voltage to current-based torque control<br>
[Continue to step 2: Enable full current control](test_foc_current){:.btn .btn-step}


## Related references

- [FOC current torque mode](foc_current_mode)
- [Current sensing hardware](current_sense)
- [Choosing ADC pins](choosing_adc_pins)
