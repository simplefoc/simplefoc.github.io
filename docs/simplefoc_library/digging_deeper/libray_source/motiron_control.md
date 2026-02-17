---
layout: default
title: Motion Control
parent: Library Source
nav_order: 2
permalink: /motion_control_implementation
grand_parent: Digging deeper
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# Motion control implementation [v2.4+](https://github.com/simplefoc/Arduino-FOC/releases)

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b  width60" src="extras/Images/clb.png"/>
<img class="type type-s hide width60" src="extras/Images/cls.png"/>

Motion control is the outer control loop in the <span class="simple">Simple<span class="foc">FOC</span>library</span>, executed within `move()`. It converts user commands (position, velocity, or torque targets) into a current setpoint (`current_sp`) that is passed to the torque control layer in `loopFOC()`.

The library implements 7 motion control strategies:

```cpp
enum MotionControlType : uint8_t {
  torque            = 0x00,     // Direct torque/current control
  velocity          = 0x01,     // Velocity motion control
  angle             = 0x02,     // Position control (cascade)
  velocity_openloop = 0x03,     // Open-loop velocity
  angle_openloop    = 0x04,     // Open-loop position
  angle_nocascade   = 0x05,     // Position control (direct)
  custom            = 0x06      // User-defined control
};
```

Selection:
```cpp
motor.controller = MotionControlType::velocity;  // Can be changed in real-time
```

## The move() function

Motion control is executed in the `move()` function, which should be called iteratively in the main loop. The function accepts an optional target parameter; if omitted, it uses `motor.target`.

```cpp
void loop() {
  motor.loopFOC();        // Fast: torque control
  motor.move();           // Slower: motion control
  // or
  motor.move(new_target); // Override motor.target
}
``` 

The real-time motion control is executed inside the `move()` function. This function executes one of the control loops based on the `controller` variable. The parameter `new_target` is the target value to be set for the control loop. The `new_target` value is optional; if not set, the motion control will use the `motor.target` variable.

Here is a simplified view of the implementation:
```cpp
// Iterative function running outer loop of the FOC algorithm
// Behavior of this function is determined by the motor.controller variable
// - needs to be called iteratively, it is an asynchronous function
// - if target is not set it uses motor.target value
void FOCMotor::move(float new_target) {
  // set internal target variable
  if(_isset(new_target)) target = new_target;
  
  // downsampling (optional)
  if(motion_cnt++ < motion_downsample) return;
  motion_cnt = 0;

  // read sensor values (except for open loop modes)
  if(controller != MotionControlType::angle_openloop && 
     controller != MotionControlType::velocity_openloop) {
    shaft_angle = shaftAngle(); 
    shaft_velocity = shaftVelocity(); 
  }

  // if disabled or not ready, do nothing
  if(!enabled || motor_status != FOCMotorStatus::motor_ready) return;
  
  // choose control loop
  switch (controller) {
    case MotionControlType::torque:
      current_sp = target;
      break;
      
    case MotionControlType::angle_nocascade:
      // angle set point
      shaft_angle_sp = target;
      // calculate the torque command directly from position error
      current_sp = P_angle(shaft_angle_sp - LPF_angle(shaft_angle));
      break;
      
    case MotionControlType::angle:
      // angle set point
      shaft_angle_sp = target;
      // calculate velocity set point
      shaft_velocity_sp = feed_forward_velocity + P_angle(shaft_angle_sp - LPF_angle(shaft_angle));
      shaft_velocity_sp = _constrain(shaft_velocity_sp, -velocity_limit, velocity_limit);
      // calculate the torque command
      current_sp = PID_velocity(shaft_velocity_sp - shaft_velocity); 
      break;
      
    case MotionControlType::velocity:
      // velocity set point
      shaft_velocity_sp = target;
      // calculate the torque command
      current_sp = PID_velocity(shaft_velocity_sp - shaft_velocity); 
      break;
      
    case MotionControlType::velocity_openloop:
      shaft_velocity_sp = target;
      current_sp = velocityOpenloop(shaft_velocity_sp); 
      break;
      
    case MotionControlType::angle_openloop:
      shaft_angle_sp = target;
      current_sp = angleOpenloop(shaft_angle_sp); 
      break;
      
    case MotionControlType::custom:
      // custom control - user provides the callback function
      if(customMotionControlCallback) 
        current_sp = customMotionControlCallback(this, target);
      break;
  }
}
```

The `move()` function is typically called at the same frequency as `loopFOC()`, but it can be downsampled using the `motion_downsample` variable to reduce computational load. This can allow for more complex motion control algorithms that do not need to run at the full speed of the torque control loop.

$$ f_{MC} = \frac{f_{TC}}{\texttt{motion_downsample} } $$

## Torque Mode

**Type:** `MotionControlType::torque`


<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b  width40" src="extras/Images/torque_control_i.png"/>
<img class="type type-s hide width40" src="extras/Images/velocity_loop_stepper_curr.png"/>

Direct torque/current control - the target value is passed directly as the current setpoint to the torque controller.

```cpp
case MotionControlType::torque:
  current_sp = target;
  break;
```

The actual torque control strategy (voltage, DC current, FOC current, estimated current) is determined by `motor.torque_controller`.

<blockquote class="info">
<p class="heading">Detailed implementation</p>
For detailed information about torque control modes, see the <a href="torque_control_implementation">torque control implementation page</a>.
</blockquote>

[Torque control API documentation](voltage_loop){: .btn .btn-docs}

## Velocity Control

**Type:** `MotionControlType::velocity`

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b  width60" src="extras/Images/velocity_loop_i.png"/>
<img class="type type-s hide width60" src="extras/Images/velocity_loop_stepper_curr.png"/>

Closed-loop velocity control using a PID controller to calculate the current setpoint from velocity error.

### Control Flow

```cpp
case MotionControlType::velocity:
  shaft_velocity_sp = target;
  current_sp = PID_velocity(shaft_velocity_sp - shaft_velocity); 
  break;
```

### Velocity Controller

The `PID_velocity` controller is a `PIDController` object. Also note the low-pass filtering of the velocity measurement before calculating the error.

```cpp
motor.PID_velocity.P = 0.2;
motor.PID_velocity.I = 20.0;
motor.PID_velocity.D = 0.0;

motor.LPF_velocity.Tf = 0.01;  // 10ms low-pass filter time constant
```

[Velocity control API documentation](velocity_loop){: .btn .btn-docs}
[PID controller implementation](pid_implementation){: .btn .btn-docs}

### Additional Features

**Velocity low-pass filtering:**
```cpp
motor.LPF_velocity.Tf = 0.01;  // 10ms low-pass filter time constant
```
[Low-pass filter implementation](lpf_implementation){: .btn .btn-docs}

## Position Control (Cascade Mode)

**Type:** `MotionControlType::angle`

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b  width60" src="extras/Images/a_b_i.drawio.png"/>
<img class="type type-s hide width60" src="extras/Images/a_s_i.drawio.png"/>


Cascaded position control: P controller generates velocity setpoint, then velocity PID calculates current setpoint.

### Control Flow

```cpp
case MotionControlType::angle:
  shaft_angle_sp = target;
  // P controller calculates velocity setpoint
  shaft_velocity_sp = feed_forward_velocity + 
                      P_angle(shaft_angle_sp - LPF_angle(shaft_angle));
  // Constrain velocity
  shaft_velocity_sp = _constrain(shaft_velocity_sp, -velocity_limit, velocity_limit);
  // Velocity PID calculates current setpoint
  current_sp = PID_velocity(shaft_velocity_sp - shaft_velocity); 
  break;
```

### Position Controller

The `P_angle` controller is a `PIDController` object (though typically only P gain is used):

```cpp
motor.P_angle.P = 20.0;      // Proportional gain
motor.P_angle.I = 0.0;       // Usually zero
motor.P_angle.D = 0.0;       // Usually zero
```

[Angle control API documentation](angle_loop){: .btn .btn-docs}
[PID controller implementation](pid_implementation){: .btn .btn-docs}

### Additional Features

**Angle low-pass filtering:**
```cpp
motor.LPF_angle.Tf = 0.0;  // Usually disabled (0)
```
[Low-pass filter implementation](lpf_implementation){: .btn .btn-docs}

**Feed-forward velocity:**
```cpp
motor.feed_forward_velocity = 0.5;  // [rad/s]
```

**Velocity limiting:**
```cpp
motor.velocity_limit = 10.0;  // [rad/s]
```


## Position Control (Non-Cascade Mode)

**Type:** `MotionControlType::angle_nocascade`

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b  width60" src="extras/Images/an_b_i.drawio.png"/>
<img class="type type-s hide width60" src="extras/Images/an_s_i.drawio.png"/>

Direct position-to-torque control without velocity loop intermediary - P controller directly calculates current setpoint.

### Control Flow

```cpp
case MotionControlType::angle_nocascade:
  shaft_angle_sp = target;
  current_sp = P_angle(shaft_angle_sp - LPF_angle(shaft_angle));
  break;
```

### Position Controller

The `P_angle` controller is a `PIDController` object (though typically only P gain is used):

```cpp
motor.P_angle.P = 20.0;      // Proportional gain
motor.P_angle.I = 0.0;       // Usually very low or zero
motor.P_angle.D = 0.0;       // Usually zero
```

[Non-cascade angle control API documentation](angle_nocascade_loop){: .btn .btn-docs}
[PID controller implementation](pid_implementation){: .btn .btn-docs}

### Additional Features

**Angle low-pass filtering:**
```cpp
motor.LPF_angle.Tf = 0.0;  // Usually disabled (0)
```

[Low-pass filter implementation](lpf_implementation){: .btn .btn-docs}

## Open-Loop Velocity Control

**Type:** `MotionControlType::velocity_openloop`

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b  width50" src="extras/Images/olb_v_i.png"/>
<img class="type type-s hide width50" src="extras/Images/ols_v_i.png"/>

Open-loop velocity control without sensor feedback - generates rotating field at target velocity.

### Control Flow

```cpp
case MotionControlType::velocity_openloop:
  shaft_velocity_sp = target;
  current_sp = velocityOpenloop(shaft_velocity_sp); 
  break;
```

The `velocityOpenloop()` function integrates velocity to calculate angle:

```cpp
float FOCMotor::velocityOpenloop(float target_velocity) {
  unsigned long now_us = _micros();
  float Ts = (now_us - open_loop_timestamp) * 1e-6f;
  if(Ts <= 0 || Ts > 0.5f) Ts = 1e-3f;
  
  shaft_angle = _normalizeAngle(shaft_angle + target_velocity * Ts);
  shaft_velocity = target_velocity;
  
  open_loop_timestamp = now_us;
  return current_sp;
}
```

[Open-loop velocity control API documentation](velocity_openloop){: .btn .btn-docs}

## Open-Loop Position Control

**Type:** `MotionControlType::angle_openloop`

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b  width50" src="extras/Images/olb_p_i.png"/>
<img class="type type-s hide width50" src="extras/Images/ols_p_i.png"/>

Open-loop position control - sets electrical angle directly without sensor feedback.

### Control Flow

```cpp
case MotionControlType::angle_openloop:
  shaft_angle_sp = target;
  current_sp = angleOpenloop(shaft_angle_sp); 
  break;
```

The `angleOpenloop()` function sets the angle and applies velocity ramping:

```cpp
float FOCMotor::angleOpenloop(float target_angle) {
  unsigned long now_us = _micros();
  float Ts = (now_us - open_loop_timestamp) * 1e-6f;
  if(Ts <= 0 || Ts > 0.5f) Ts = 1e-3f;
  
  // Calculate angle difference
  float angle_diff = _normalizeAngle(target_angle - shaft_angle);
  
  // Apply velocity limit
  if(abs(angle_diff) > velocity_limit * Ts) {
    angle_diff = _sign(angle_diff) * velocity_limit * Ts;
  }
  
  shaft_angle = _normalizeAngle(shaft_angle + angle_diff);
  
  open_loop_timestamp = now_us;
  return current_sp;
}
```

[Open-loop position control API documentation](angle_openloop){: .btn .btn-docs}

## Custom Control

**Type:** `MotionControlType::custom`

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<img class="type type-b  width60" src="extras/Images/cus_b.png"/>
<img class="type type-s hide width60" src="extras/Images/cus_s.png"/>

User-defined control algorithm via callback function.

### Control Flow

```cpp
case MotionControlType::custom:
  if(customMotionControlCallback) 
    current_sp = customMotionControlCallback(this, target);
  break;
```

### Usage

Set the callback function pointer:

```cpp
// Define custom control function
float myCustomControl(FOCMotor* motor, float target) {
  // Your control algorithm here
  // Access motor state: motor->shaft_velocity, motor->shaft_angle, etc.
  // Return current setpoint
  return calculated_current_sp;
}

// Register callback
motor.customMotionControlCallback = myCustomControl;
motor.controller = MotionControlType::custom;
```

[Custom control API documentation](custom_control){: .btn .btn-docs}

