---
layout: default
title: Options Reference
nav_order: 1
permalink: /reference/options_reference
parent: Reference
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: False
has_toc: False
---


# Options

<span class="simple">Simple<span class="foc">FOC</span>library</span> has many options, and the following tables summarize the important ones, collected in one convenient place for your immediate reference.

## Driver Options

Driver options are set before calling `driver.init()`, and normally never changed after intialization.

Option | Default value | Supported on | Description
--- | --- | --- | ---
driver.pwm_frequency | depends on MCU | BLDC, Stepper | PWM frequency, in Hz
driver.voltage_power_supply | 12V | BLDC, Stepper | Power supply voltage in volts
driver.voltage_limit | NOT SET | BLDC, Stepper | Hard limit on output voltage, in volts. Effectively limits PWM duty cycle proportionally to power supply voltage.
driver.intialized | | BLDC, Stepper | Read-only. true if initialized successfully, false otherwise
driver.enable_active_high | true | BLDC | If true, driver is enabled by writing '1' to enable pin. If false, driver is enabled by writing '0' to enable pin.
driver.dead_zone | 0.02 | BLDCDriver6PWM | Amount of dead-time for each pwm cycle, as a proportion of 100% duty cycle. A float in the range [0,1]. Values under 10% make sense.

## Motor Options

Option | Default value | Description
--- | --- | ---
motor.controller | MotionControlType::torque | Motion control mode
motor.torque_controller | TorqueControlType::voltage | Torque control mode
motor.motion_downsample | 0 | Set to values > 1 to reduce how often move() is executed compared to loopFOC(). On fast MCUs it makes sense to reduce how often move() gets called.
motor.phase_resistance | NOT SET | Motor phase resistance. If set, used to calculate current limits based on voltage limits. Value in Ohms.
motor.K_bemf | NOT SET | motor back emf constant, as 1/KV. Units 1/rad/s/V. Set via motor constructor, where you can specify KV in RPM/V.
motor.voltage_limit | 12V | Global voltage limit. Limits Q-axis voltage.
motor.current_limit | 2A | Global current limit. Limits Q-axis current.
motor.velocity_limit | 20rad/s | Global velocity limit. Value in rad/s.
motor.foc_modulation | FOCModulationType::SinePWM | FOC modulation mode.
motor.modulation_centered | 1 (true) | 1/True: centered modulation around driver.voltage_limit÷2 or 0/False: pulled to 0
motor.sensor_offset | 0 | Offset of motor zero to sensor zero. Can be used to make position 0 take on a specific motor orientation. For user convenience. Value in rad.
motor.voltage_sensor_align | motor.voltage_limit | Limits voltage (and therefore current) during motor alignment. Value in Volts.
motor.velocity_index_search | NOT SET | Limits motor velocity during motor initialization. Give value in rad/s.
motor.zero_electric_angle | NOT SET | Needed for FOC control. Normally set during motor FOC initialization. Can be stored and supplied as a parameter to motor.initFOC().
motor.sensor_direction | NOT SET | Normally set during motor FOC initialization. Determines sensor direction vs positive motor direction (can be opposite, depending how you connect your motor cables). Can be stored and supplied as a parameter to motor.initFOC().
motor.motor_status | FOCMotorStatus::motor_uninitialized | Read-only. Tracks the motor initialization status. You can check it to see if initialization succeeded, calibration is complete, etc.

## PID Tuning Options

Option | Modes used | Description
--- | --- | ---
motor.PID_velocity.P | All closed loop modes | Velocity PID controller P value. Varies by situation. Typical values are 0.2 to 0.6, but could be quite different.
motor.PID_velocity.I | All closed loop modes | Velocity PID controller I value. Varies by situation. Typical values are 2.0 to 20.0, but could be quite different.
motor.PID_velocity.D | All closed loop modes | Velocity PID controller D value. Normally set to 0. Typical values are 0, or a very low value like 0.001.
motor.PID_velocity.ramp | All closed loop modes | Velocity PID controller maximum change. Typical value 1000.0, set lower to limit accelleration.
motor.PID_velocity.limit | All closed loop modes | Velocity PID controller output limit. Set to limit velocity to this maximum.
motor.P_angle.P | Closed loop position control | Angle P controller P value. Varies by situation. Typical values are 10.0 to 20.0, but could be quite different.
motor.LPF_velocity.Tf | All closed loop modes | Velocity low pass filter time constant. Values larger than 0, max 1.0. The lower the value, the slower the effect of velocity changes from the sensor.
motor.LPF_angle.Tf | All closed loop modes | Angle low pass filter time constant. Values larger than 0, max 1.0. The lower the value, the slower the effect of angle changes from the sensor.
motor.PID_current_q.P | Torque current control | Q-Axis current controller P value.
motor.PID_current_q.I | Torque current control | Q-Axis current controller I value.
motor.PID_current_q.D | Torque current control | Q-Axis current controller D value.
motor.PID_current_q.ramp | Torque current control | Q-Axis current controller maximum change.
motor.PID_current_q.limit | Torque current control | Q-Axis current controller output limit.
motor.PID_current_d.P | Torque current control | D-Axis current controller P value.
motor.PID_current_d.I | Torque current control | D-Axis current controller I value.
motor.PID_current_d.D | Torque current control | D-Axis current controller D value.
motor.PID_current_d.ramp | Torque current control | D-Axis current controller maximum change.
motor.PID_current_d.limit | Torque current control | D-Axis current controller output limit.
motor.LPF_current_q.Tf | Torque current control | Q-Axis current low pass filter time constant.
motor.LPF_current_d.Tf | Torque current control | D-Axis current low pass filter time constant.

