---
layout: default
title: Practical guides
parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 5
permalink: /practical_guides
has_children: True
has_toc: False
---

# Practical guides

Hands-on procedures and troubleshooting tips for working with BLDC and stepper motors using the <span class="simple">Simple<span class="foc">FOC</span>library</span>. This section is community-driven, so contributions are welcome.

Need more help? Visit the community forum: <a href="https://community.simplefoc.com">community.simplefoc.com</a>


## Getting started

- [Choosing PWM pins for your application](choosing_pwm_pins) - selecting appropriate PWM pins.
- [Choosing analog pins for your application](choosing_adc_pins) - selecting ADC inputs.
- [Units in the library](library_units) - units for key motor/sensor parameters and how to transform them.

## PID tuning

- [Tuning the current control loop](tuning_current_loop) - step-by-step tuning process.
- [Tuning the velocity control loop](tuning_velocity_loop) - step-by-step tuning process.
- [Ziegler-Nichols PID tuning](zn_tuning_pid) - structured PID tuning method.

## Motor parameters and characterization

- [Measuring the phase resistance](phase_resistance) -  multimeter method.
- [Measuring the KV rating](kv_rating_measure) - using SimpleFOC to measure KV.
- [Testing and tuning motor parameters](motor_params_test) - how to manually test and tune motor parameters for optimal performance.
- [Automatic motor characterisation with characteriseMotor()](motor_characterisation) - using the built-in function to measure motor parameters.

## Advanced control

- [Real-time loop with timers](real_time_loop) - implementing the FOC loop in hard real time.
- [Implementing a custom sensor](generic_sensor) - how to implement a custom sensor class.
- [Implementing a custom current sense](generic_current_sense) - how to implement a custom current-sensing setup.
- [Regular ADC reads during FOC](regular_adc_read) - reading auxiliary analog signals safely while current sensing is active.

## Performance tips

- [Open-loop efficiency considerations](openloop_efficiency) — efficiency considerations for open-loop control.

## Coding guides

- [Library options and build flags cheat sheet](options_cheetsheet) - compile-time options and build flags.