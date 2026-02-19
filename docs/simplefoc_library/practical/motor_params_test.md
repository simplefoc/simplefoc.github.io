---
layout: default
title: Testing and Tuning Motor Parameters
nav_order: 5
description: "Practical guide to testing and tuning motor parameters in SimpleFOC"
permalink: /motor_params_test
parent: Practical guides
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---


# Testing and tuning motor parameters

This guide provides practical tips for testing and tuning key motor parameters (phase resistance, KV rating, and inductance) to ensure optimal performance with the SimpleFOC library.

It could even be potentially used without prior knowledge of the motor parameters, as it is a way to test if the parameters are set correctly, and how to adjust them if they are not.
But we certainly recommend to at least measure the phase resistance with a multimeter, as it is the most important parameter for the current control loop, and it is very easy to measure.

## Motor Parameters

Motor parameters in <span class="simple">Simple<span class="foc">FOC</span>library</span> are used for three main components:
- For `estimated_current` torque control mode, the phase resistance is used to convert the desired current to voltage, and the KV rating is used to compensate for the back-EMF voltage. **(Required)**
- For `foc_current` and `dc_current` torque control modes, the phase resistance is used compensate for the cross-coupling between the d and q axis. **(Optional)**
- For q and d-axis PI controller auto-tuning, the phase resistance and inductance are used to calculate the controller gains. **(Required for auto-tuning, but you can also set the gains manually without knowing the motor parameters)**

### Phase Resistance ($$R$$ in Ohms)

Electrical resistance between motor phases (in Ohms). Determines the voltage-to-current conversion at standstill. 

Typical values:
- Gimbal motors 3-10 Ω
- Drone motors 0.05-1 Ω
- Stepper motors 1-5 Ω depending on motor size.


This value is very commonly provided in the datasheet, and it is also very easy to measure with a multimeter. If you don't have the datasheet, or if you want to verify the value, you can measure it with a multimeter by measuring the resistance between two of the motor wires (phases). The value should be similar for all pairs of wires.

[See a guide to manual mesuring phase resistance](phase_resistance){: .btn .btn-docs}

### KV Rating (in rpm/V)

This parameter is the motor speed per volt under no load. The higher the KV rating, the faster the motor will spin for a given voltage. For example, a 100 KV motor spins at 100 rpm per 1 Volt applied (under no load).

Typical values: 
- Gimbal motors 50-200 KV
- Drone motors 200-3000 KV
- Stepper motors 10-50 KV depending on motor size and winding.

For drone motors, this parameter is often provided in the datasheet, but for gimbal motors and stepper motors it is often not provided, or it is very inaccurate. If the KV rating is unknown, you can measure it by applying a known voltage in voltage mode and converting the observed speed (in rad/s) to rpm.

[See a guide to measuring KV rating with SimpleFOC](kv_rating_measure){: .btn .btn-docs}

### Phase Inductance ($$L$$, in Henry)

Inductive reactance that stores energy in the magnetic field. Determines the d-axis voltage needed to prevent lag at high speeds. 

Typical values: 0.1–10 mH. Usually, larger motors often have higher inductance.

This parameter is often not provided in the datasheet, and it is also very difficult to measure. It is somewhat commonly specified for stepper motors, but it is often not provided for gimbal motors and drone motors.  

If the inductance value is unknown, that is not a problem, as the lag compensation is the least significant of the three compensations, and it is only needed for high speed applications. You can start with a low value (ex. 0.001 H) and increase it until you reach the desired performance at high speeds.

See the guides for testing the motor parameters below for more details on how to test and tune the inductance value.

## Troubleshooting motor parameters

Here is a quick guide to check if the motor parameters are set correctly, and how to adjust them if they are not. 

Always start by testing the phase resistance, then the KV rating, and finally the inductance. 


These tests will be done in torque control mode with estimated current, as this is the mode that is most affected by the motor parameters, and it is also the mode that is most commonly used for FOC applications.

```cpp

void setup() {
  // Set up your motor, sensor, and driver as usual
  // ...

  // Set control mode to torque and torque control mode to estimated current
  motor.controller = MotionControlType::torque;
  motor.torque_controller = TorqueControlType::estimated_current;
}
```

Also it is suggested to use `Commander` interface to easily change the target and motor parameters from the serial terminal, but it is not required. You can also change the parameters directly in the code and upload it again, but using `Commander` is more convenient for testing and tuning.

```cpp

  ...

// Set up commander interface
Commander commander = Commander(Serial);

void doMotor(char* cmd) {
  commander.motor(&motor, cmd);
}
void setup() {
  // Set up your motor, sensor, and driver as usual
  // ...

  // Set control mode to torque and torque control mode to estimated current
  motor.controller = MotionControlType::torque;
  motor.torque_controller = TorqueControlType::estimated_current;

  // Add motor to commander interface
  commander.add('M', doMotor, "motor");
  ...
}
void loop() {
  // Run the motor
  motor.loopFOC();
  motor.move();
  ...
  
  // Run commander interface
  commander.run();
}
```

### Phase resistance testing

To check the phase resistance value do this test:
- Set KV and inductance to `NOT_SET` or 0, so that the back-EMF compensation and lag compensation are disabled and do not affect the test.
```cpp
motor.KV = NOT_SET; // or 0
motor.axis_inductance.q = NOT_SET; // or 0
motor.phase_resistance = 1.0; // set to the datasheet value or start low if unknown
```
or in commander interface:
```sh
$ MK0
$ MIQ0 # or MI0 for both d and q axis
$ MR1.0
```
- Set the target current to a reasonable value (ex. 1.0 A) 
```cpp
motor.target = 1.0; // set to a reasonable value
```
or in commander interface:
```sh
$ M1.0
```
- The motor should be able to start moving at 1A, especially without load. 

**Potential issues**

1. If **motor does not move**, increase `phase_resistance` value. If the motor does not move the current is probably much lower than the desired current $i_q$ because the phase resistance is set too low. 
2. If the **motor and driver get hot for small $$i_q$$ values**, resistance is probably too high. Most of the drivers and motors can handle 1-2A of current without heating too much. If they are heating a lot at 1A,  this probably means that the resistance value is too high and the actual current is much higher than the desired current $$i_q$$. In this case, decrease `phase_resistance` value.

### KV rating testing

Once you tested the phase resitance, you can test the KV rating. Testing the KV rating is a bit more tricky, but here are some tips to check if the KV rating is set correctly:
- Set the KV value
```cpp
motor.phase_resistance = 1.234; // verified value from the previous test
motor.KV_rating = 100; // set to the datasheet value or start low if unknown
```
or in commander interface:
```sh
$ MK100
$ MIQ0 # or MI0 for both d and q axis   
$ MR1.234
```
- Set the target current to 0 ($$i_q = 0$$) and try to turn the motor by hand. 
```cpp
motor.target = 0; // set to 0 to test the back-EMF compensation
```
or 
```sh
$ M0
```
- The ideal behavior is as if the motor is disconnected. Try spinning the unconnected motor by hand, and set the KV value so that the motor feels similar to the unconnected motor when you set $i_q = 0$.

**Potential issues**
1. If you feel almost no resistance and the motor **continues totating freely after you spin it** initally. This proabably means that the KV rating is set too high and the back-EMF compensation is overcompensating. In this case, decrease the `KV` value. The ideal behavior is as if the motor is disconnected, but if you feel that the motor is actually helping you to turn it, then the KV value is probably too high. Try spinning the unconnected motor by hand, and set the KV value so that the motor feels similar to the unconnected motor when you set $$i_q = 0$$.
2. If you **feel a lot of resistance**, similar resistance as for voltage mode, then the KV rating is probably set too low and the back-EMF compensation is undercompensating. In this case, increase the `KV` value. 

### Inductance value testing

Ok so you've tested the phase resistance and KV rating, and they seem to be correct, now you can check the inductance value. The inductance value is the most difficult to test, as it affects the performance of the motor at higher speeds, and it is not easy to feel the difference by hand, but here are some tips to check if the inductance value is set correctly:

- Set the inductance value
```cpp
motor.phase_resistance = 1.234; // verified value from the previous test
motor.KV_rating = 100; // verified value from the previous test
motor.axis_inductance.q = 0.001; // set to the datasheet value or start low if unknown
```
or in commander interface:
```sh
$ MK100
$ MIQ0.001 # or MI0.001 for both d and q axis
$ MR1.234
```
- Set the target current to a reasonable value (ex. 1.0 A) and try to reach the maximum speed of the motor.
```cpp
motor.target = 1.0; // set to a reasonable value
```
or in commander interface:
```sh
$ M1.0
```
- The motor should be able to reach higher speeds than without the lag compensation, especially with load.
  - Try setting the inducrance to 0 or `NOT_SET` and check the maximum speed you can reach.
    ```sh
    $ MIQ0 
    ```
    wait a bit and get the velocity value at the serial monitor, or by using the commander interface:
    ```sh
    $ MG5
    ```
  - Then set the inductance to a good value and check if the maximum speed you can reach is higher than without the lag compensation.
    ```sh
    $ MIQ0.001 
    ```
    wait a bit and get the velocity value at the serial monitor, or by using the commander interface:
    ```sh
    $ MG5
    ```
  - The difference will typically be between 10-30%
  
**Potential issues**
- **If the motor does not spin faster than without inductance compensation**, the inductance value is probably set too low and the lag compensation is not enough. In this case, increase the `axis_inductance.q` value.
- **If the motor is unstable at high speeds (much much faster)**, the inductance value is probably set too high and the lag compensation is overcompensating. In this case, decrease the `axis_inductance.q` value. You can also test this by trying to load the motor by hand at high speeds. If the motor feels like it has no torque at high speeds and it is very easy to brake it by hand, then the inductance value is probably too high and the lag compensation is overcompensating (even applying some field weakening). In this case, decrease the `axis_inductance.q` value.

