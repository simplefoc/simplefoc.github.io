---
layout: default
title: Low-Pass Filter Implementation
parent: Library Source
nav_order: 4
permalink: /lpf_implementation
grand_parent: Digging deeper
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# Low-Pass Filter Implementation [v2.4+](https://github.com/simplefoc/Arduino-FOC/releases)

The `LowPassFilter` class provides first-order low-pass filtering for noisy sensor measurements, particularly useful for velocity calculations that involve differentiation of position.

## Class Definition

From `common/lowpass_filter.h`:

```cpp
class LowPassFilter {
public:
    LowPassFilter(float Tf, float Ts = NOT_SET);
    float operator() (float x);
    
    float Tf; // Low pass filter time constant
    float Ts; // Fixed sampling time (optional)
    
protected:
    unsigned long timestamp_prev; // Last execution timestamp
    float y_prev; // Filtered value in previous execution step 
};
```

## Constructor

```cpp
LowPassFilter::LowPassFilter(float time_constant, float sampling_time)
    : Tf(time_constant)
    , Ts(sampling_time)
    , y_prev(0.0f)
{
    timestamp_prev = _micros();
}
```

**Parameters:**
- `Tf`: Time constant (seconds) - determines filter response speed
- `Ts`: Fixed sampling time (optional) - if not set, adaptive timing is used

## Filter Algorithm

The `operator()` method implements the filtering:

```cpp
float LowPassFilter::operator() (float x) {
    // Initialize elapsed time with fixed sampling time Ts
    float dt = Ts; 
    
    // If Ts not set, use adaptive sampling time
    if(!_isset(dt)){
        unsigned long timestamp = _micros();
        dt = (timestamp - timestamp_prev)*1e-6f;

        // Handle timing issues
        if (dt < 0.0f) dt = 1e-3f;
        else if(dt > 0.3f) {
            y_prev = x;
            timestamp_prev = timestamp;
            return x;
        }
        timestamp_prev = timestamp;
    }

    // Calculate first-order filter
    float alpha = Tf/(Tf + dt);
    float y = alpha*y_prev + (1.0f - alpha)*x;

    // Save for next iteration
    y_prev = y;
    return y;
}
```

## Filter Equation

The discrete-time filter equation is:

$$
y[k] = \alpha \cdot y[k-1] + (1-\alpha) \cdot x[k]
$$

Where the smoothing factor is:

$$
\alpha = \frac{T_f}{T_f + \Delta t}
$$

**Variables:**
- $$y[k]$$: Current filtered output
- $$y[k-1]$$: Previous filtered output
- $$x[k]$$: Current input sample
- $$T_f$$: Filter time constant
- $$\Delta t$$: Time since last sample

## Characteristics

### Time Constant (Tf)

The time constant determines the filter's cutoff frequency:

$$
f_c = \frac{1}{2\pi T_f}
$$

**Effect on filtering:**
- **Smaller Tf** (e.g., 0.001s = 1ms):
  - Faster response
  - Less smoothing
  - Higher cutoff frequency (~159 Hz)
  - More noise passes through

- **Larger Tf** (e.g., 0.1s = 100ms):
  - Slower response
  - More smoothing
  - Lower cutoff frequency (~1.6 Hz)
  - Better noise rejection

### Adaptive vs Fixed Timing

**Adaptive timing** (`Ts = NOT_SET`):
- Measures actual time between calls
- Handles variable execution rates
- Adds timestamp overhead
- Includes safety checks for timing anomalies

**Fixed timing** (`Ts` set):
- Assumes constant sampling rate
- Faster execution (no time measurement)
- Suitable for fixed-rate control loops
- More predictable frequency response

## Usage in SimpleFOC

### Velocity Filtering

```cpp
// In motor initialization
motor.LPF_velocity.Tf = 0.01;  // 10ms time constant, fc ≈ 16 Hz

// Automatic usage in shaftVelocity() 
// in FOCMotor.cpp
float FOCMotor::shaftVelocity() {
    return LPF_velocity(sensor->getVelocity());
}
```

### Angle Filtering

```cpp
// Position control with angle filtering
motor.LPF_angle.Tf = 0.005;  // 5ms time constant

// in FOCMotor.cpp move() function
// Used in position control
current_sp = P_angle(shaft_angle_sp - LPF_angle(shaft_angle));
```

### Current Filtering (FOC mode)

```cpp
// Current measurement filtering
motor.LPF_current_q.Tf = 0.005;  // 5ms
motor.LPF_current_d.Tf = 0.005;  // 5ms

// in FOCMotor.cpp loopFOC() function
// Applied to measured currents
current.q = LPF_current_q(current_sense->getDCCurrent(electrical_angle));
current.d = LPF_current_d(0);  // d-current filtered similarly
```

## Tuning Guidelines

### Step 1: Identify Noise Frequency

Measure or estimate the dominant noise frequency $$f_{noise}$$:
- PWM noise: typically 10-50 kHz
- Sensor quantization: depends on encoder resolution and speed
- Electromagnetic interference: variable

### Step 2: Set Cutoff Frequency

Choose cutoff frequency below noise but above signal:

$$
f_c = \frac{f_{signal}}{2} \text{ to } f_{noise} \times 0.1
$$

### Step 3: Calculate Time Constant

$$
T_f = \frac{1}{2\pi f_c}
$$

### Step 4: Verify Performance

**Too much filtering** (Tf too large):
- Sluggish response
- Phase lag in control
- Reduced performance at higher speeds

**Too little filtering** (Tf too small):
- Noisy measurements
- Control instability
- Audible noise from motor

### Example: Velocity Filtering

For a motor with:
- Maximum velocity: 50 rad/s
- Control frequency: 1 kHz
- Noisy encoder with ~100 Hz noise

```cpp
// Signal bandwidth: ~50 rad/s ≈ 8 Hz
// Noise frequency: ~100 Hz
// Target cutoff: 15-20 Hz (between signal and noise)

float fc = 16.0;  // Hz
float Tf = 1.0 / (2.0 * PI * fc);  // ≈ 0.01 seconds

motor.LPF_velocity.Tf = 0.01;  // 10ms time constant
```

## Related Documentation

[PID Controller Implementation](pid_implementation){: .btn .btn-docs}
[Motion control implementation](motion_control_implementation){: .btn .btn-docs}
[Torque control implementation](torque_control_implementation){: .btn .btn-docs}
