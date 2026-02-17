---
layout: default
title: Getting Started Guide
nav_order: 1
description: "Step-by-step guides to get your SimpleFOC motor control working"
permalink: /example_from_scratch
parent: Writing the Code
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
has_children: true
---

# Getting Started Guides

Here is an attempt to guide you through testing and configuring your motor control system step-by-step.  It is designed to be practical and fast - you should be able to get a working motor control system in around an hour by following these steps. Each step is self-contained and testable, guiding you through the configuration, expected behavior, and troubleshooting. And if you want to go further, there are clear next steps based on your hardware setup. 

## Before You Start

- You have [installed SimpleFOC](installation)  
- You are [familiar with the library structure](code)  
- You have [the necessary hardware](supported_hardware)  

## Foundation Steps (All Users)

Follow these 4 guides in order. Each takes **10-15 minutes** and builds on the previous one:

### [1. Test Your Sensor](test_sensor) 
**~10 min** - Validate that your position sensor is working  
➜ Output angle and velocity values in serial monitor

### [2. Test Your Driver](test_driver)
**~10 min** - Verify motor driver can output PWM signals  
➜ See "Driver ready!" message, no motors spinning yet

### [3. Motor + Driver Test (Open-Loop)](test_motor_driver)
**~15 min** - Test motor and driver together  
➜ Motor spins, you discover correct pole pair count

### [4. Closed-Loop Control (Voltage-Based Torque)](test_closedloop)
**~15 min** - First closed-loop FOC using sensor feedback  
➜ Closed loop torque control working, motor follows torque/velocity/angle targets

For many users the foundation steps are all you need to get a working motor control system. Voltage mode is not the most efficient or precise control method, but it is simple and works with most motors.

[Read more about voltage-based torque control](torque_control){: .btn .btn-docs} [Read more about motion control](motion_control){: .btn .btn-docs}

## Advanced Steps (Optional)

If you want to go further and use more advanced current-based torque control, follow the next steps based on your hardware.

### [A. Estimated Current Control (No Current Sensing)](estimated_current_guide)

**Benefits:**
- True torque control without current sensors
- Higher speeds and higher torques than voltage mode
- Good for budget builds or simple applications

**Requirements:**
- No current sensing hardware required
- Motor parameters (resistance, inductance, flux) must be measured or estimated

[Read more about estimated current control](estimated_current_mode){: .btn .btn-docs}

### [B. FOC Current Control (With Sensors)](foc_current)

**Benefits:**
- Most precise torque control available in <span class="simple">Simple<span class="foc">FOC</span>library</span>
- Hardware current feedback
- No model parameter needed

**Requirements:**
- Current sensing hardware (inline or lowside)

[Read more about FOC torque control](foc_current_torque_mode){: .btn .btn-docs}

## Full Reference Documentation

Once you're done with this getting started guide, see:
- [Library Code Structure & API](code) - Complete reference
- [Position Sensors](sensors) - Detailed sensor configuration
- [Motor Drivers](drivers_config) - All supported drivers
- [Motion Control](closed_loop_motion_control) - Control modes explained
- [Torque Control](torque_control) - Voltage and current control details
- [Current Sensing](current_sense) - Current feedback options

---
