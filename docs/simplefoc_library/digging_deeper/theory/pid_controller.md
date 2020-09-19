---
layout: default
title: PID controller theory
parent: Theory corner
grand_parent: Digging deeper
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
nav_order: 3
permalink: /pi_controller
---

 
# PID controller theory
Transfer function of the PID controller this library implements is:

<p><img src="./extras/Images/contPID.png" /></p>

Continuos PID is transformed to the discrete domain and can be described as a sum of three components:

<p><img src="./extras/Images/PID_eq.png" /></p>

proportional: 
<p><img src="./extras/Images/PID_pro.png" /></p>
integral: 
<p><img src="./extras/Images/PID_int.png" /></p>
derivative: 
<p><img src="./extras/Images/PID_der.png" /></p>

Where the <i>u(k)</i> is the control signal (voltage <i>U<sub>q</sub></i> in our case) in moment <i>k</i>, <i>e(k),e(k-1)</i> is the tracking error in current moment <i>k</i> and previous step <i>k-1</i>. Tracking error presents the difference in between the target velocity value <i>v<sub>d</sub></i> and measured velocity <i>v</i>. 


<p><img src="./extras/Images/track.png" /></p>


## Implementation details

The `BLDMotor` class has implemented generic PI controller function called `controllerPID()`.
```cpp
// PI controller function
float BLDCMotor::controllerPID(float tracking_error, PI_s& cont){
  // calculate the time from the last call
  unsigned long now_us = _micros();
  float Ts = (now_us - cont.timestamp) * 1e-6;
  // quick fix for strange cases (micros overflow)
  if(Ts <= 0 || Ts > 0.5) Ts = 1e-3; 

  // u(s) = (P + I/s + Ds)e(s)
  // Discrete implementations
  // proportional part 
  // u_p  = P *e(k)
  float proportional = cont.P * tracking_error;
  // Tustin transform of the integral part
  // u_ik = u_ik_1  + I*Ts/2*(ek + ek_1)
  float integral = cont.integral_prev + cont.I*Ts*0.5*(tracking_error + cont.tracking_error_prev);
  // antiwindup - limit the output voltage_q
  integral = constrain(integral, -voltage_limit, voltage_limit);
  // Discrete derivation
  // u_dk = D(ek - ek_1)/Ts
  float derivative = cont.D*(tracking_error - cont.tracking_error_prev)/Ts;
  // sum all the components
  float voltage = proportional + integral + derivative;

  // antiwindup - limit the output voltage_q
  voltage = constrain(voltage, -voltage_limit, voltage_limit);

  // limit the acceleration by ramping the the voltage
  float d_voltage = voltage - cont.output_prev;
  if (abs(d_voltage)/Ts > cont.output_ramp) voltage = d_voltage > 0 ? cont.output_prev + cont.output_ramp*Ts : cont.output_prev - cont.output_ramp*Ts;

  // saving for the next pass
  cont.integral_prev = integral;
  cont.output_prev = voltage;
  cont.tracking_error_prev = tracking_error;
  cont.timestamp = now_us;
  return voltage;
}
```
The PID controller is configured with `motor.PID_velocity` structure:
```cpp
// PID controller configuration structure
struct PID_s{
  float P; //!< Proportional gain 
  float I; //!< Integral gain 
  float D; //!< Derivative gain 
  long timestamp;  //!< Last execution timestamp
  float integral_prev;  //!< last integral component value
  float output_prev;  //!< last pid output value
  float output_ramp;  //!< Maximum speed of change of the output value
  float tracking_error_prev;  //!< last tracking error value
};
```
