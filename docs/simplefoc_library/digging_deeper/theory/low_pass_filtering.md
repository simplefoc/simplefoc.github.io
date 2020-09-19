---
layout: default
title: Low Pass Filter
parent: Theory corner
grand_parent: Digging deeper
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 4
permalink: /low_pass_filter
---

# Low-Pass velocity filter theory
Transfer function of the Low pass filter is controller is:

<p><img src="./extras/Images/cont_LPF.png" /></p>
In it discrete form it becomes:

<p><img src="./extras/Images/dis_LPF.png" /></p>

where <i>v<sub>f</sub>(k)</i> is filtered velocity value in moment <i>k</i>, <i>v(k)</i> is the measured velocity in the moment <i>k</i>, <i>T<sub>f</sub></i> is the filter time constant and <i>T<sub>s</sub></i> is the sampling time (or time in between executions of the equation).
This low pass filter can be also written in the form:

<p><img src="./extras/Images/LPF_alpha.png" /></p>

where:

<p><img src="./extras/Images/alpha.png" /></p>

This makes it a bit more clear what the time constant `Tf` of the Low pass filter stands for. If your sample time is around 1millisecond (for arduino UNO this can be taken as an average) then setting the
`Tf` value to `Tf = 0.01` will result in:

```cpp
alpha = 0.01/(0.01 + 0.001) = 0.91
```

Which means that your actual velocity measurement <i>v</i> will influence the filtered value <i>v<sub>f</sub><i> with the coefficient `1-alpha = 0.09` which is going to smooth the velocity values considerably (maybe even too much, depends of the application).


## Implementation details

Low pass filtering function implemented in the <span class="simple">Simple<span class="foc">FOC</span>library</span>:
```cpp
// low pass filter function
// - input  - signal to be filtered
// - lpf    - LPF_s structure with filter parameters 
float BLDCMotor::lowPassFilter(float input, LPF_s& lpf){
  unsigned long now_us = _micros();
  float Ts = (now_us - lpf.timestamp) * 1e-6;
  // quick fix for strange cases (micros overflow)
  if(Ts <= 0 || Ts > 0.5) Ts = 1e-3; 

  // calculate the filtering 
  float alpha = lpf.Tf/(lpf.Tf + Ts);
  float out = alpha*lpf.prev + (1-alpha)*input;

  // save the variables
  lpf.prev = out;
  lpf.timestamp = now_us;
  return out;
}
```
The low pass filter is configured with `motor.LPF_velocity`structure:
```cpp
// Low pass filter structure
struct LPF_s{
  float Tf; // Low pass filter time constant
  long timestamp; // Last execution timestamp
  float prev; // filtered value in previous execution step 
};
```
