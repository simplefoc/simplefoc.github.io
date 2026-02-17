---
layout: default
title: Full FOC Current Control
nav_order: 2
description: "Enable current-based torque control with closed-loop current feedback"
permalink: /test_foc_current
parent: FOC Current Control
grand_parent: Writing the Code
great_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---

# Full FOC Current Control

⏱️ **~30 minutes**

This guide shows how to switch from voltage-based to **current-based torque control**. This enables precise current limiting and the smoothest motor control.

[Read more about FOC current torque mode](foc_current_mode){: .btn .btn-docs}

## Prerequisites

Before starting, make sure you already have:
- Working **current sensing** setup - [Current sense setup guide](test_current)
- D/Q current visualization working and values are correct
- Motor is stable and [responsive in voltage torque mode](test_closedloop)

## What Changes?

| Aspect | Voltage Mode | Current Mode |
|---|---|---|
| Control method | `TorqueControlType::voltage` | `TorqueControlType::foc_current` |
| Set target as | Volts (ex. 0-12V) | Amps (ex. 0-5A) |
| Behavior | Torque depends on speed | Torque is direct & predictable |
| Protection | Voltage limits | Current limiting |

## Step 1: Switch to current control mode

The change is minimal - just one line in your setup:

```cpp
// Before (voltage control)
motor.torque_controller = TorqueControlType::voltage;

// After (current control)
motor.torque_controller = TorqueControlType::foc_current;
```

Take your working code from the [current sense setup guide](test_current) and make this change:

```cpp
void setup() {
  // ... sensor, driver, current sense init ...

  // Change this line:
  motor.torque_controller = TorqueControlType::foc_current;  // ← Was: voltage
  motor.controller = MotionControlType::torque;

  // Everything else stays the same!
  motor.useMonitoring(Serial);
  if(!motor.init()) { /* ... */ }
  if(!motor.initFOC()) { /* ... */ }

  motor.target = 0; // Now in Amps instead of Volts!
  
  // ... rest of setup ...
}

void loop() {
  motor.loopFOC();
  motor.move();
  motor.monitor();
  command.run();
}
```

I using the code from the [previous step](test_current) you can change the torque mode using the Commander interface without re-uploading:
```sh
MT2 
```

## Step 2: Validate current control

Test that the motor responds correctly to current commands.

### Expected behavior

1. Motor responds immediately to current commands
2. Torque is proportional to the current command (e.g., 0.5A should feel like half the torque of 1A)
3. At zero current motor feel like disconnected (no resistance)
4. D-axis current stays at 0 the whole time

### Validation procedure

0. **Visualise currents** 
  - Use **Arduino Serial Plotter** or the serial monitor (should be stable and responsive to commands)
1. **Set target to zero current:** 
  - Type `M0`: Motor should feel free to spin (no resistance). 
  - More or less the same as disconnected.
2. **Set small current:**
  - Type `M0.2`: Motor should feel light resistance, 
  - Current $$Iq$$ should stabilize around 0.2 Amps in the serial output.
3. **Increase current:**
   - Type `M0.5`: Should feel stronger resistance.
4. **Try negative current:**
   - Type `M-0.2`: Motor should resist in the opposite direction
5. **Let motor spin**
   - Release motor shaft and set current target: `M0.5`
   - Motor should accelerate to a certain speed
     - Then current drops as motor can't keep up (this is normal)
   - Change current magnitude and direction to observe that
      - The higher the current, the higher the acceleration
      - The current magnitude does not affect the top speed, just how quickly it gets there
6. **Check D-axis current:**
   - D-axis current $$Id$$ should stay close to zero the whole time
7. **Back to zero current:**
   - While motor is spinning, set `M0` again
   - Motor should decelerate and come to the full stop
   - It should feel like it's disconnected again (no resistance)

### Quick Troubleshooting

**Current control feels jerky or unstable?**
- Check that `motor.initFOC()` completed successfully
- Ensure current sensing is working well [(from Step 5)](test_current)
- Reduce serial output frequency (e.g., `motor.monitor_downsampling = 500;` or in commander: `MMD500`)
- If none of the other troubleshooting steps work, the issue are probably PID parameters 
   - Quick fix: Try lowering `motor.PID_current_q.P` and ``motor.PID_current_q.P` values (e.g., 5.0, 3.0, etc.) - using commande `MQP5` and `MDP5` 
   - For detailed tuning, see the [Current loop PID tuning guide](tuning_current_loop)


For more details about FOC control parameters, see the [FOC current control mode documentation](foc_current_torque_mode).


## Step 3. Use Velocity or Angle Control with FOC 

Now that you have current control working, you can switch to velocity or angle control modes for motion control. The current control will still be active in the background, but now the library will automatically calculate the required current to achieve your velocity or angle targets.

```cpp
// Switch to velocity control mode
motor.controller = MotionControlType::velocity;

// Switch to angle control mode
motor.controller = MotionControlType::angle;
```

or you can switch modes on the fly using the Commander interface:
```sh
MC1  // Velocity mode
MC2  // Angle mode
MC0  // Torque mode (current control)
```

Once in these modes, you can set velocity or angle targets as usual (e.g., `M100` for 100 rad/s or `M3.14` for 180 degrees) and the current control will ensure the motor delivers the required torque to achieve those targets. You will likely need to tune your PID parameters for velocity and angle control to get the best performance, but the current control will provide a solid foundation for smooth and responsive motion.

[See the velocity loop tuning guide](tuning_velocity_loop){:.btn .btn-docs} 
[See the angle loop tuning guide](angle_cascade_control#angle-loop-outer-loop-guidance){:.btn .btn-docs}
[See more practical guides](practical_guides#pid-tuning){:.btn .btn-docs}

[For general motion control tips, see the motion control documentation](motion_control){:.btn .btn-docs}

<blockquote class="info" markdown="1"> <p class="heading">💡 Commander Interface</p>

You can use the Commander interface to easily switch between modes:
- `MT2` - Switch to current control (foc_current)
- `M0.5` - Set current target to 0.5 Amps
- `MC1` - Switch to velocity mode - target will be set to 0
- `M100` - Set velocity target to 100 rad/s
- `MC2` - Switch to angle mode - target will be set to current angle
- `M3.14` - Set angle target to 3.14 radians (180 degrees)

See the [Commander interface guide](commander_interface) for more commands.
</blockquote>


## What's Next?

You now have a fully functional FOC control system! Depending on your needs:

### For Robotics/Automation
- See [Motion Control docs](closed_loop_motion_control)
- Implement feedback loops in your application code

### For Precision Applications
- Tune PID parameters for your motor
- Use angle mode for position control
- See [PID tuning guide](practical_guides#pid-tuning)

### For Learning
- Explore the [SimpleFOC library examples](https://github.com/simplefoc/Arduino-FOC/tree/master/examples)
- Read about [FOC theory and source code](digging_deeper)
- For debugging, monitoring, and communication docs, see [Communication](communication) and [Debugging](debugging) sections

