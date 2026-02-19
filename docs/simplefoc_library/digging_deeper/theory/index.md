---
layout: default
title: Theory corner
parent: Digging deeper
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 1
permalink: /theory_corner
has_children: True
has_toc: False
toc: true
---

# Theory corner

Core concepts and math behind the Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> implementation. If you want the “why” behind control modes, filters, and alignment, start here.

## FOC fundamentals

- [Torque Control Methods  in FOC](voltage_torque_control) — why voltage mode works 
- [Coordinate Transformations in FOC](foc_theory) — modulation methods and FOC fundamentals.
- [Hybrid stepper motor theory](hybrid_stepper_theory) — driving 2-phase steppers with 3-phase BLDC drivers.

## Control building blocks

- [Low-pass filter theory](lpf_implementation#filter-equation) — filter equation and implementation.
- [PID controller theory](pid_implementation#control-equation) — control equation and implementation.
- [Tunning the current control loop](tuning_current_loop#suggested-tuning-strategy) — pole placement method for tuning current loop.

## Alignment procedures

- [Sensor-Motor alignment procedure](alignment_procedure) — rotor alignment and sensor offset.
- [Current sense alignment procedure](current_sense_align) — automatic phase alignment.
