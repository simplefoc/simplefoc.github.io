---
layout: default
title: PWM Modulation Strategies
parent: Theory corner
grand_parent: Digging deeper
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "PWM modulation techniques for FOC motor control"
nav_order: 3
permalink: /pwm_modulation
toc: true
---

# PWM Modulation Strategies

After FOC calculates the desired phase voltages through coordinate transformations, these voltages must be converted into PWM signals. SimpleFOC supports multiple modulation techniques, each with different voltage efficiency and complexity.

## Overview

All modulation types receive the same inputs from FOC:
- $$U_q$$ = torque voltage (q-axis)
- $$U_d$$ = flux voltage (d-axis)  
- $$\theta_{el}$$ = electrical rotor angle

The modulation strategy determines how to apply these voltages to the motor phases.

---

## Sinusoidal PWM (`SinePWM`)

The standard approach applies pure sinusoidal phase voltages.

**Voltage utilization:**
$$U_{q,max} = 0.5 \cdot V_{supply}$$

**Use when:**
- You want clean, simple waveforms

**Supported motors:**
- BLDC motors (3-phase)
- Stepper motors (2-phase)
- Hybrid stepper motors (3-phase)

---

## Space Vector PWM (`SpaceVectorPWM`)

SVPWM achieves better efficiency by modulating the common-mode voltage, enabling ~15% higher voltage output.

**Voltage utilization:**
$$U_{q,max} = \frac{1}{\sqrt{3}} \cdot V_{supply} \approx 0.577 \cdot V_{supply}$$

**Benefits:**
- 15% more available voltage than SinePWM
- Better for battery-powered systems
- Industry standard for high-performance drives

**Centered vs non-centered:**
- **Centered** (`modulation_centered = true`): Default, symmetric waveforms
- **Non-centered** (`modulation_centered = false`): Better for lowside current sensing (not recommended for general use)

**Use when:**
- You need maximum torque at voltage limit
- Battery power efficiency matters
- Using lowside current sensing (not recommended for general use)

**Supported motors:**
- BLDC motors (3-phase)
- Hybrid stepper motors (3-phase)

---

## Trapezoidal modulation (BLDC only)

Classical BLDC commutation using discrete 6-step or 12-step sequences.

### Trapezoid_120
- 6-step commutation (classical BLDC)
- Sharpest transitions
- Simplest computation
- Highest torque ripple

### Trapezoid_150
- 12-step commutation
- Smoother than 120°
- Better efficiency
- Lower torque ripple

**Voltage utilization:**
$$U_{q,max} \approx 0.5 \cdot V_{supply}$$

**Use when:**
- Motor has trapezoidal back-EMF
- Minimal computation power available
- Hall sensor commutation is desired

**Note:** Only supported for BLDC motors.

---

## Comparison

| Modulation | Max Voltage | Complexity | Best For |
|------------|-------------|-----------|----------|
| **SinePWM** | 0.5× V<sub>supply</sub> | Low | Learning, debugging, clean waveforms |
| **SpaceVectorPWM** | 0.577× V<sub>supply</sub> | Medium | High efficiency, battery power, high speed |
| **Trapezoid_120** | 0.5× V<sub>supply</sub> | Very Low | Minimal computation, classical BLDC |
| **Trapezoid_150** | 0.5× V<sub>supply</sub> | Low | Trapezoidal back-EMF motors |

---

## Configuration

### Power supply voltage
```cpp
motor.voltage_power_supply = 12; // volts
```
Critical for PWM scaling and saturation detection.

### Select modulation type
```cpp
// For BLDC motors
motor.foc_modulation = FOCModulationType::SinePWM;
motor.foc_modulation = FOCModulationType::SpaceVectorPWM;  // Recommended for efficiency
motor.foc_modulation = FOCModulationType::Trapezoid_120;
motor.foc_modulation = FOCModulationType::Trapezoid_150;

// For stepper motors (SVPWM not supported)
motor.foc_modulation = FOCModulationType::SinePWM;

// For hybrid stepper motors
motor.foc_modulation = FOCModulationType::SinePWM;
motor.foc_modulation = FOCModulationType::SpaceVectorPWM;  // Recommended for efficiency
```

### SVPWM centering (if using SpaceVectorPWM)
```cpp
motor.modulation_centered = true;  // Default: symmetric waveforms
// or
motor.modulation_centered = false; // For lowside current sensing
```

---

## Motor support matrix

| Motor Type | SinePWM | SpaceVectorPWM | Trapezoid |
|-----------|---------|----------------|-----------|
| **BLDC** | ✓ | ✓ | ✓ |
| **Stepper** | ✓ | ✗ | ✗ |
| **Hybrid Stepper** | ✓ | ✓ | ✗ |

---

## Choosing your modulation

**For most users:** Start with **SinePWM** (default, simplest)

**Switch to SpaceVectorPWM if you need:**
- Maximum torque at voltage limit
- Battery-powered efficiency
- High-speed operation

**Use Trapezoidal if:**
- Working with older BLDC drivers
- Minimal CPU needed
- Motor has trapezoidal back-EMF

---

## What about saturation?

When voltage demand exceeds the modulation limit ($$U_q > U_{q,max}$$), the PWM duty cycles clip to the available range. The motor still works due to its inductance filtering the waveforms, but efficiency decreases and torque ripple increases.

For smooth operation, keep $$U_q$$ below the theoretical limit of your chosen modulation type.

