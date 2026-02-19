---
layout: default
title: PID Controller Implementation
parent: Library Source
nav_order: 5
permalink: /pid_implementation
grand_parent: Digging deeper
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# PID Controller Implementation [v2.4+](https://github.com/simplefoc/Arduino-FOC/releases)

The `PIDController` class provides a discrete-time PID controller with anti-windup, output limiting, and rate limiting capabilities. It is used throughout SimpleFOC for velocity and current control loops.

## Class Definition

From `common/pid.h`:

```cpp
class PIDController {
public:
    PIDController(float P, float I, float D, float ramp = NOT_SET, 
                  float limit = NOT_SET, float sampling_time = NOT_SET);
    float operator() (float error);
    void reset();
    
    float P;              // Proportional gain 
    float I;              // Integral gain 
    float D;              // Derivative gain 
    float output_ramp;    // Maximum speed of change of output value
    float limit;          // Maximum output value
    float Ts;             // Fixed sampling time (optional)
    
protected:
    float error_prev;     // Last tracking error value
    float output_prev;    // Last PID output value
    float integral_prev;  // Last integral component value
    unsigned long timestamp_prev; // Last execution timestamp
};
```

## Constructor

```cpp
PIDController::PIDController(float P, float I, float D, float ramp, 
                             float limit, float sampling_time)
    : P(P)
    , I(I)
    , D(D)
    , output_ramp(ramp)     // output derivative limit [ex. volts/second]
    , limit(limit)          // output supply limit [ex. volts]
    , Ts(sampling_time)     // sampling time [seconds]
    , error_prev(0.0f)
    , output_prev(0.0f)
    , integral_prev(0.0f)
{
    timestamp_prev = _micros();
}
```

**Parameters:**
- `P`: Proportional gain
- `I`: Integral gain
- `D`: Derivative gain
- `ramp`: Maximum rate of change of output (units/second)
- `limit`: Maximum absolute output value
- `sampling_time`: Fixed time step (optional)

## PID Algorithm

The `operator()` method calculates the control output:

```cpp
float PIDController::operator() (float error) {
    // Initialize elapsed time with fixed sampling time Ts
    float dt = Ts; 
    
    // If Ts not set, use adaptive sampling time
    if(!_isset(dt)){
        unsigned long timestamp_now = _micros();
        dt = (timestamp_now - timestamp_prev) * 1e-6f;
        // Quick fix for strange cases (micros overflow)
        if(dt <= 0 || dt > 0.5f) dt = 1e-3f;
        timestamp_prev = timestamp_now;
    }

    // Proportional part: u_p = P * e(k)
    float proportional = P * error;
    
    // Tustin transform of integral part
    // u_ik = u_ik_1 + I*Ts/2*(e_k + e_k_1)
    float integral = integral_prev + I*dt*0.5f*(error + error_prev);
    // Antiwindup - limit the integral
    if(_isset(limit)) integral = _constrain(integral, -limit, limit);
    
    // Discrete derivative: u_dk = D(e_k - e_k_1)/Ts
    float derivative = D*(error - error_prev)/dt;

    // Sum all components
    float output = proportional + integral + derivative;
    // Antiwindup - limit the output
    if(_isset(limit)) output = _constrain(output, -limit, limit);

    // Output ramping (rate limiting)
    if(_isset(output_ramp) && output_ramp > 0){
        float output_rate = (output - output_prev)/dt;
        if (output_rate > output_ramp)
            output = output_prev + output_ramp*dt;
        else if (output_rate < -output_ramp)
            output = output_prev - output_ramp*dt;
    }
    
    // Save for next iteration
    integral_prev = integral;
    output_prev = output;
    error_prev = error;
    return output;
}
```

## Control Equation

The continuous-time PID equation:

$$
u(t) = K_p e(t) + K_i \int_0^t e(\tau) d\tau + K_d \frac{de(t)}{dt}
$$

### Discrete Implementation

**Proportional:**
$$
u_p[k] = P \cdot e[k]
$$

**Integral (Tustin/Trapezoidal):**
$$
u_i[k] = u_i[k-1] + \frac{I \cdot \Delta t}{2}(e[k] + e[k-1])
$$

**Derivative (Backward difference):**
$$
u_d[k] = D \cdot \frac{e[k] - e[k-1]}{\Delta t}
$$

**Total output:**
$$
u[k] = u_p[k] + u_i[k] + u_d[k]
$$

## Key Features

### 1. Tustin Integration

The integral term uses the **Trapezoidal (Tustin) method** instead of simple Euler:

```cpp
integral = integral_prev + I*dt*0.5f*(error + error_prev);
```

**Benefits:**
- Better numerical stability
- More accurate for varying sample times
- Reduced integration drift
- Improved frequency response

**Comparison with Euler:**
- Euler: $$u_i[k] = u_i[k-1] + I \cdot \Delta t \cdot e[k]$$
- Tustin: $$u_i[k] = u_i[k-1] + \frac{I \cdot \Delta t}{2}(e[k] + e[k-1])$$

### 2. Anti-Windup

Integral windup occurs when the integral term accumulates beyond useful limits. SimpleFOC implements **clamping anti-windup**:

```cpp
// Limit integral component
if(_isset(limit)) integral = _constrain(integral, -limit, limit);

// Limit total output
if(_isset(limit)) output = _constrain(output, -limit, limit);
```

**How it works:**
1. Integral clamped to output limit
2. Total output also clamped
3. Prevents excessive overshoot
4. Faster recovery from saturation

### 3. Output Rate Limiting

Constrains how fast the output can change:

```cpp
if(_isset(output_ramp) && output_ramp > 0){
    float output_rate = (output - output_prev)/dt;
    if (output_rate > output_ramp)
        output = output_prev + output_ramp*dt;
    else if (output_rate < -output_ramp)
        output = output_prev - output_ramp*dt;
}
```

**Purpose:**
- Limits acceleration
- Prevents sudden jumps
- Smoother motor operation
- Reduces mechanical stress

### 4. Adaptive Time Step

When `Ts` is not set:

```cpp
dt = (timestamp_now - timestamp_prev) * 1e-6f;
if(dt <= 0 || dt > 0.5f) dt = 1e-3f;  // Safety check
```

**Handles:**
- Variable execution rates
- Micros() overflow
- Missed cycles

## Reset Function

```cpp
void PIDController::reset() {
    integral_prev = 0.0f;
    output_prev = 0.0f;
    error_prev = 0.0f;
}
```

**When to use:**
- Switching control modes
- After long pauses
- To clear accumulated error
- Mode transitions

## Usage in SimpleFOC

### Velocity Control

```cpp
// Configuration
motor.PID_velocity.P = 0.2;
motor.PID_velocity.I = 20.0;
motor.PID_velocity.D = 0.0;
motor.PID_velocity.output_ramp = 1000;  // [A/s] or [V/s]
motor.PID_velocity.limit = 12;          // [A] or [V]

// in FOCMotor.cpp move() function
current_sp = PID_velocity(target_velocity - shaft_velocity);
```

### Position Control (P Controller)

```cpp
// Typically only P gain used for position
motor.P_angle.P = 20.0;
motor.P_angle.I = 0.0;   // Usually zero
motor.P_angle.D = 0.0;   // Usually zero
motor.P_angle.limit = 0; // velocity_limit used instead

// in FOCMotor.cpp move() function
// Calculates velocity setpoint
shaft_velocity_sp = P_angle(target_angle - shaft_angle);
```

### FOC Current Control

```cpp
// Q-axis (torque) current
motor.PID_current_q.P = 5.0;
motor.PID_current_q.I = 300.0;
motor.PID_current_q.D = 0.0;
motor.PID_current_q.output_ramp = 0;
motor.PID_current_q.limit = motor.voltage_limit;

// D-axis (flux) current
motor.PID_current_d.P = 5.0;
motor.PID_current_d.I = 300.0;
motor.PID_current_d.D = 0.0;
motor.PID_current_d.output_ramp = 0;
motor.PID_current_d.limit = motor.voltage_limit;

// Usage in  FOCMotor.cpp - loopFOC() function
voltage.q = PID_current_q(current_sp - current.q);
voltage.d = PID_current_d(0 - current.d);  // d-current target = 0
```

### DC Current Control

```cpp
motor.PID_current_q.P = 5.0;
motor.PID_current_q.I = 300.0;
motor.PID_current_q.limit = motor.voltage_limit;

// in FOCMotor.cpp loopFOC() function
// Controls magnitude of current vector
voltage_magnitude = PID_current_q(current_sp - current_magnitude);
```

## Quick Reference: Parameter Effects

| Parameter | Too Low | Too High |
|-----------|---------|----------|
| **P** | Slow response, steady-state error | Overshoot, oscillation, instability |
| **I** | Steady-state error | Overshoot, oscillation, slow settling |
| **D** | Allows overshoot | Noise amplification, instability |
| **output_ramp** | Jerky motion | Slow response, sluggish |
| **limit** | Output saturation issues | No protection, potential damage |


## PID Tuning

For detailed tuning procedures and practical guidance, see the dedicated tuning guide:

[PID Controller Tuning Guide](tuning_pid){: .btn .btn-docs .btn-primary} 
[Velocity Loop Tuning Guide](tuning_velocity_loop){: .btn .btn-docs}

## Related Documentation

[Motion control implementation](motion_control_implementation){: .btn .btn-docs}
[Torque control implementation](torque_control_implementation){: .btn .btn-docs}
[Low-pass filter implementation](lpf_implementation){: .btn .btn-docs}

