---
layout: default
title: Ziegler-Nichols Tuning
nav_order: 8
description: "Practical guide to tuning PID controllers in SimpleFOC"
permalink: /zn_tuning_pid
parent: Practical guides
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# Ziegler-Nichols PID Tuning Guide

A short quick guide to PID tuning using the **Ziegler-Nichols method**.

## Ziegler-Nichols Method

One of the most straightforward methods to tune PID controllers is the Z[iegler-Nichols method](https://en.wikipedia.org/wiki/Ziegler%E2%80%93Nichols_method), which provides a systematic way to find initial PID gains based on the system's response.


Here is an example of the velocity PID tuning process using this method.
But the similar process can be applied to current PID tuning as well as for the position PID (with some adjustments to the target and monitoring variables).

### Step 1: Find Critical Gain

Start with I and D disabled:

```cpp
motor.PID_velocity.P = 0.1;  // Start low
motor.PID_velocity.I = 0.0;
motor.PID_velocity.D = 0.0;
```

Use the `motor.monitor()` to observe the velocity response while gradually increasing P:

```cpp
// Example monitoring setup (velocity + target)
void setup() {
    // ... motor, sensor, driver init ...
    Serial.begin(115200);

    motor.useMonitoring(Serial);
    motor.monitor_variables = _MON_TARGET | _MON_VEL;
    motor.monitor_downsample = 10; // print every 10th loop

    // ... motor.init(); motor.initFOC(); ...
}

void loop() {
    // main FOC algorithm
    motor.loopFOC();

    // hold a constant velocity target for tuning
    motor.move(5.0); // rad/s

    // print monitoring data
    motor.monitor();
}
```

**Increase P gradually until the system oscillates continuously.**

Note two values:
- **Kc** = the P value where oscillation starts
- **Tc** = period of oscillation (seconds) - the time between peaks in the velocity response

**Example:** Oscillation at P = 0.8 with period 0.1s → Kc = 0.8, Tc = 0.1

### Step 2: Apply Formula

Choose your controller type:

#### P Only
```cpp
P = 0.5 * Kc;
```

#### PI (Most Common)
```cpp
P = 0.45 * Kc;
I = 0.54 * Kc / Tc;
D = 0.0;
```

#### PID
```cpp
P = 0.6 * Kc;
I = 1.2 * Kc / Tc;
D = 0.075 * Kc * Tc;
```

### Example: Calculate PI Gains

Given Kc = 0.8, Tc = 0.1:

```cpp
motor.PID_velocity.P = 0.45 * 0.8 = 0.36;
motor.PID_velocity.I = 0.54 * 0.8 / 0.1 = 4.32;
motor.PID_velocity.D = 0.0;
```

## Fine-Tuning After Ziegler-Nichols

Minor adjustments after applying the formula:

**Oscillating?** → Reduce I by 20-50%  
**Too slow?** → Increase P or I by 20%  
**Overshooting?** → Reduce P by 10-20% or add D = 0.001×P

## Parameter Effects

| Parameter | Too High | Too Low |
|-----------|----------|---------|
| **P** | Oscillation | Slow, steady-state error |
| **I** | Overshoot, oscillation | Drifts from target |
| **D** | Noise amplification | Overshoot |
| **output_ramp** | Sluggish | Jerky motion |
| **limit** | No protection | Saturation |

## Common Fixes

| Problem | To try... |
|---------|----------|
| Oscillating | Reduce P or I by 50% |
| Slow | Increase P or I |
| Overshoot | Reduce P/I or add small D |
| Steady error | Increase I |
| Jittery | Increase LPF_velocity.Tf or set D = 0 |

## Related Documentation

[PID controller implementation](pid_implementation){: .btn .btn-docs}
[Tuning velocity loop](tuning_velocity_loop){: .btn .btn-docs}
[Low-pass filter implementation](lpf_implementation){: .btn .btn-docs}
