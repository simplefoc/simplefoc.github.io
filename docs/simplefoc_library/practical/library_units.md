---
layout: default
title: Units in the library
nav_order: 5
permalink: /library_units
parent: Practical guides
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---


# Units in the library

All the motor/driver/sensor parameters and control variables are defined in the following units:

Physical value | Description | Unit | Comment 
--- | --- | --- | ---
Position/angle | `Radians` | `RAD` | Motor and sensor position in radians
Velocity | `Radians per second` | `RAD/s` | Motor and sensor velocity in radians per second
Torque/Current | `Amperes` | `A`  | Motor torque or current
Voltage | `Volts` | `V` | Motor/Driver voltage
Phase resistance | `Ohms` | `Ω` | Motor phase resistance
Phase inductance | `Henries` | `H` | Motor phase inductance
KV rating | `Radians per second per volt` | `RAD/s/V` | Motor velocity constant
PWM frequency | `Hertz` | `Hz` | Motor/driver PWM frequency
PMW duty cycle | `No unit` | - | All duty cycles have the range: `0 - 1.0`



## Position/angle units

The motor and sensor position is defined in radians. One full rotation of the motor is equal to $$2\pi$$ (6.14) radians is equal to 360 degrees.

### Some important variables in radians (`RAD`)

- `motor.shaft_angle` - the current motor position in radians
- `motor.electrical_angle` - the current motor electrical position in radians
- `motor.target` - the target motor position in radians ( in closed-loop and open-loop angle control) 
- `sensor.getAngle()` - the current sensor position in radians ( in closed-loop control)

### Converting the angle to degrees

In order to convert the angle from radians to degrees, you can use the following formula:

$$
a_{deg} = a_{rad} \cdot \frac{360}{2\pi}
$$

#### Setting the target angle in degrees

For example, if you wish to set the target angle in degrees:

```cpp
motor.target = my_target_in_degrees * _2PI/360;
```

Or for example if you're using [commander interface](commander) to set the target angle in degrees:

```cpp
void onTarget(char* cmd){ 
    // get the target angle in degrees
    float target_angle_deg = atof(cmd);
    // set the target angle in radians
    motor.target = target_angle_deg * _2PI/360;
}
...
void setup(){
    ...
    // add the command to the commander
    commander.add('A', onTarget, "angle in degrees");
    ...
}
```

#### Reading the sensor angle in degrees

If you wish to read motor position in degrees, you can easily convert it using

```cpp
float angle_deg = motor.shaft_angle * 360/_2PI;
```

The same can be done for the outputs of the `Sensor` class:
```cpp
float angle_deg = sensor.getAngle() * 360/_2PI;
```

### Converting the angle to rotations (turns)

To convert the angle from radians to turns (rotations) you can use the following formula:

$$
a_{rot} = a_{rad} \cdot \frac{1}{2\pi}
$$

#### Setting the target angle in turns

For example, if you wish to set the target angle in turns:

```cpp
motor.target = my_target_in_turns / _2PI;
```

Or for example if you're using [commander interface](commander) to set the target angle in turns:

```cpp
void onTarget(char* cmd){ 
    // get the target angle in turns
    float target_angle_turns = atof(cmd);
    // set the target angle in radians
    motor.target = target_angle_turns / _2PI;
}
... 
void setup(){
    ...
    // add the command to the commander
    commander.add('A', onTarget, "angle in turns");
    ...
}
```

#### Reading the sensor angle in turns

If you wish to read motor position in turns, you can easily convert it using

```cpp
float angle_turns = motor.shaft_angle / _2PI;
```

The same can be done for the outputs of the `Sensor` class:
```cpp
float angle_turns = sensor.getAngle() / _2PI;
```

## Velocity units

All the parameters/variables related to the velocity, both for the motor and sensor, are defined in radians per second. One full rotation of the motor is equal to $$2\pi$$ (6.14) radians is equal to 360 degrees.
Some other standard units for the velocity are RPM (revolutions per minute) and 1/s (turns per second).


### Some important variables in radians per second (`RAD/s`)

- `motor.shaft_velocity` - the current motor velocity in radians per second
- `motor.velocity_limit` - the velocity limit in radians per second
- `motor.feedback_velocity` - the current motor velocity in radians per second ( in closed-loop control)
- `motor.target` - the target motor velocity in radians per second ( in closed-loop and open-loop velocity control)
- `sensor.getVelocity()` - the current sensor velocity in radians per second ( in closed-loop control)

### Converting the velocity to RPM

In order to convert the velocity from radians per second to RPM, you can use the following formula:

$$
v_{RPM} = v_{RAD/s} \cdot \frac{60}{2\pi}
$$

<blockquote class="info" markdown="block">
<p class="heading">RULE OF THUMB</p>

The motor velocity in `RPM` is approximately 10 times the motor velocity in radians per second `RAD/s`, as  $$\frac{60}{2\pi}\approx 10$$

So the conversion can be approximated as:

$$v_{RPM} = 10 \cdot v_{RAD/s} $$

</blockquote>

#### Setting the target velocity in RPM

For example, if you wish to set the target velocity in RPM:

```cpp
motor.target = my_target_in_RPM * _2PI/60;
```

Or for example if you're using [commander interface](commander) to set the target velocity in RPM:

```cpp
void onTarget(char* cmd){ 
    // get the target velocity in RPM
    float target_velocity_RPM = atof(cmd);
    // set the target velocity in radians per second
    motor.target = target_velocity_RPM * _2PI/60;
}
...
void setup(){
    ...
    // add the command to the commander
    commander.add('V', onTarget, "velocity in RPM");
    ...
}
```

#### Reading the sensor velocity in RPM

If you wish to read motor velocity in RPM, you can easily convert it using

```cpp
float velocity_RPM = motor.shaft_velocity * 60/_2PI;
```

The same can be done for the outputs of the `Sensor` class:
```cpp
float velocity_RPM = sensor.getVelocity() * 60/_2PI;
```

### Converting the velocity to turns per second

To convert the velocity from radians per second to turns per second, you can use the following formula:

$$
v_{tps} = v_{rad/s} \cdot \frac{1}{2\pi}
$$

#### Setting the target velocity in turns per second

For example, if you wish to set the target velocity in turns per second:

```cpp
motor.target = my_target_in_tps / _2PI;
```

Or for example if you're using [commander interface](commander) to set the target velocity in turns per second:

```cpp
void onTarget(char* cmd){ 
    // get the target velocity in turns per second
    float target_velocity_tps = atof(cmd);
    // set the target velocity in radians per second
    motor.target = target_velocity_tps / _2PI;
}
...
void setup(){
    ...
    // add the command to the commander
    commander.add('V', onTarget, "velocity in turns per second");
    ...
}
```

#### Reading the sensor velocity in turns per second

If you wish to read motor velocity in turns per second, you can easily convert it using

```cpp
float velocity_tps = motor.shaft_velocity / _2PI;
```

The same can be done for the outputs of the `Sensor` class:
```cpp
float velocity_tps = sensor.getVelocity() / _2PI;
```

## Torque/Current units

The motor torque or current is defined in Amperes. The torque is directly proportional to the current flowing through the motor windings. The torque constant $$K_t$$ is a parameter that describes the relationship between the motor torque and the current.

### Some important variables in Amperes [`A`]

- `motor.current_limit` - the current motor limit in Amperes
- `motor.target` - the target motor current in Amperes ( in closed-loop torque control or in voltage control with current estimation)
- `motor.currents.q` - the current motor currents in Amperes 
- `motor.currents.q` - the current motor currents in Amperes
- `current_sense.getFOCCurrent()` - the current motor currents in Amperes
- `current_sense.getDCCurrents()` - the current measured current magnitude in Amperes
- `current_sense.getPhaseCurrents()` - the current motor phase currents in Amperes
- `PhaseCurrent_s` - the structure that holds the motor phase currents in Amperes
- `DQCurrent_s` - the structure that holds the D and Q motor currents in Amperes
- `ABCurrent_s` - the structure that holds the Alpha and Beta motor currents in Amperes


### Converting the current to torque

The motor torque is directly proportional to the current through the torque constant $$K_t$$. The torque constant is defined in the motor datasheet and often can be approximated to be the same as the electrical (BEMF) constant $$K_t \approx K_e$$.

$$
\tau = K_t \cdot I
$$

#### Setting the target torque in `Nm`

For example, if you wish to set the target torque in Nm:

```cpp
float Kt = ....; // datasheet value
motor.target = my_target_in_Nm / Kt; // Nm / (Nm/A) = Amps
```

Or for example if you're using [commander interface](commander) to set the target torque in Nm:

```cpp
void onTarget(char* cmd){ 
    // get the target torque in Nm
    float target_torque_Nm = atof(cmd);
    // set the target current in Amperes
    motor.target = target_torque_Nm / Kt;
}
...
void setup(){
    ...
    // add the command to the commander
    commander.add('T', onTarget, "torque in Nm");
    ...
}
```

#### Reading the sensor current in Nm

If you wish to read motor current in Nm, you can easily convert it using

```cpp
float current_Nm = motor.currents.q * Kt;
```

Or directly from the current sensor:

```cpp
DQCurrent_s c = current_sense.getFOCCurrent();
float current_Nm = c.q * Kt; // torque in Nm
```
