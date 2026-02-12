---
layout: default
title: FOC Current Mode
parent: Torque Control
grand_parent: Writing the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /foc_current_torque_mode
nav_order: 3 
toc: true
---


# Torque control using FOC currents 
<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>


<div class="type type-b" markdown="1">

This torque control mode allows you to the true torque control of a BLDC motor and it requires current sensing in order to do it. The user sets the target current $$I_d$$ to the FOC algorithm calculates the necessary phase voltages $$u_a$$ ,$$u_b$$ and $$u_c$$ in order to maintain it by measuring the phase currents ($$i_a$$, $$i_b$$ and $$i_c$$) and the rotor angle $$a$$. This mode is enabled by:
</div>
<div class="type type-s hide" markdown="1">
This torque control mode allows you to the true torque control of a Stepper motor and it requires current sensing in order to do it. The user sets the target current $$I_d$$ to the FOC algorithm calculates the necessary phase voltages $$u_a$$ and $$u_b$$ in order to maintain it by measuring the phase currents ($$i_a$$ and $$i_b$$) and the rotor angle $$a$$. This mode is enabled by:
</div>

```cpp
// FOC current torque control mode
motor.torque_controller = TorqueControlType::foc_current;
```

## How does it work exactly
<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

<div class="type type-b">
 <a name="foc_image"></a><img class="width60" src="extras/Images/foc_current_mode.png">
</div>
<div class="type type-s hide">
 <a name="foc_image"></a><img class="width60" src="extras/Images/foc_current_stepper.png">
</div>



<div class="type type-b" markdown="1">
The FOC current torque control algorithm reads the phase currents of the Stepper motor ($$i_a$$ and $$i_b$$). 
The phase currents are transformed into the currents $$i_\alpha$$ and $$i_\beta$$ using the Inverse Clarke transform. For example if the phase currents $$i_a$$ and $$i_b$$ are measured:

$$
i_\alpha = i_a, \quad i_\beta = i_a\frac{1}{\sqrt{3}} + i_b\frac{2}{\sqrt{3}}
$$

</div>
<div class="type type-s hide" markdown="1">
The DC current torque control algorithm reads the phase currents of the Stepper motor ($$i_a$$ and $$i_b$$). 
The phase currents are transformed into the currents $$i_\alpha$$ and $$i_\beta$$ using the Inverse Clarke transform (which is trivial in case of stepper motors).

$$
i_\alpha = i_a, \quad i_\beta =i_b 
$$

</div>

Using the current rotor angle $$a$$ and the currents $$i_\alpha$$ and $$i_\beta$$ the FOC algorithm calculates the $$i_d$$ and $$i_q$$ components of the current using the inverse Park transform.

$$
i_q =  i_\beta \cos(a) - i_\alpha \sin(a), \quad i_d = i_\alpha \cos(a) + i_\beta \sin(a)
$$

Using the target current value $$I_d$$ (d as desired) and the measured currents $$i_q$$ and $$i_d$$, the PID controllers for each axis calculate the appropriate voltages $$U_q$$ and $$U_d$$ to be set to the motor, to maintain $$i_q$$=$$I_d$$ and $$i_d$$=0. 

$$
U_q = \text{PID}_q(I_d - i_q), \quad U_d = \text{PID}_d(0-i_d)
$$

<div class="type type-b" markdown="1">
Finally using the Park+Clarke (or SpaceVector) transformation the FOC algorithm sets the appropriate $$u_a$$, $$u_b$$ and $$u_c$$ voltages to the motor. By measuring the phase currents this torque control algorithm ensures that these voltages generate the appropriate currents and magnetic force in the motor rotor with exactly <i>90 degree</i> offset from its permanent magnetic field, which guarantees maximal torque, this is called commutation.

</div>
<div class="type type-s hide" markdown="1">
Finally using the Clarke transformation the FOC algorithm sets the appropriate $$u_a$$ and $$u_b$$ voltages to the motor. By measuring the phase currents this torque control algorithm ensures that these voltages generate the appropriate currents and magnetic force in the motor rotor with exactly <i>90 degree</i> offset from its permanent magnetic field, which guarantees maximal torque, this is called commutation.

</div>

The torque generated in the motor is proportional (with the torque constant $$K_t$$) to the q-axis current $$i_q$$. So for any target motor torque $$\tau$$ the target current $$I_d$$ can be calculated as:

$$
I_d = \frac{\tau}{K_t}
$$


## Configuration parameters
In order to make this loop run smoothly the user needs to configure the PID controller parameters of the `PID_current_q` and Low pass filter `LPF_current_q` time constant.
```cpp
// Q axis
// PID parameters - default 
motor.PID_current_q.P = 5;                       // 3    - Arduino UNO/MEGA
motor.PID_current_q.I = 1000;                    // 300  - Arduino UNO/MEGA
motor.PID_current_q.D = 0;
motor.PID_current_q.limit = motor.voltage_limit; 
motor.PID_current_q.ramp = 1e6;                  // 1000 - Arduino UNO/MEGA
// Low pass filtering - default 
LPF_current_q.Tf= 0.005;                         // 0.01 - Arduino UNO/MEGA

// D axis
// PID parameters - default 
motor.PID_current_d.P = 5;                       // 3    - Arduino UNO/MEGA
motor.PID_current_d.I = 1000;                    // 300  - Arduino UNO/MEGA
motor.PID_current_d.D = 0;
motor.PID_current_d.limit = motor.voltage_limit; 
motor.PID_current_d.ramp = 1e6;                  // 1000 - Arduino UNO/MEGA
// Low pass filtering - default 
LPF_current_d.Tf= 0.005;                         // 0.01 - Arduino UNO/MEGA
```


## Torque control example code

<a href ="javascript:show('b','type');"  class="btn btn-type btn-b btn-primary">BLDC motors</a>
<a href ="javascript:show('s','type');" class="btn btn-type btn-s"> Stepper motors</a>

A simple example of the FOC current based torque control using Inline current sensor and setting the target value by serial command interface. 

<div class="type type-b" markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDC motor & driver instance
BLDCMotor motor = BLDCMotor(11);
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// encoder instance
Encoder encoder = Encoder(2, 3, 500);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// current sensor
InlineCurrentSense current_sense = InlineCurrentSense(0.01, 50.0, A0, A2);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }

void setup() { 
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link driver
  motor.linkDriver(&driver);
  // link the driver to the current sense
  current_sense.linkDriver(&driver);

  // current sense init hardware
  current_sense.init();
  // link the current sense to the motor
  motor.linkCurrentSense(&current_sense);

  // set torque mode:
  motor.torque_controller = TorqueControlType::foc_current; 
  // set motion control loop to be used
  motor.controller = MotionControlType::torque;

  // foc current control parameters (Arduino UNO/Mega)
  motor.PID_current_q.P = 5;
  motor.PID_current_q.I= 300;
  motor.PID_current_d.P= 5;
  motor.PID_current_d.I = 300;
  motor.LPF_current_q.Tf = 0.01; 
  motor.LPF_current_d.Tf = 0.01; 

  // use monitoring with serial 
  Serial.begin(115200);
  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  motor.init();
  // align sensor and start FOC
  motor.initFOC();

  // add target command T
  command.add('T', doTarget, "target current");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target current using serial terminal:"));
  _delay(1000);
}

void loop() {

  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="type type-s hide" markdown="1">

```cpp
#include <SimpleFOC.h>

// Stepper motor & driver instance
StepperMotor motor = StepperMotor(50);
StepperDriver2PWM driver = StepperDriver2PWM(9, 5, 6, 8);

// encoder instance
Encoder encoder = Encoder(2, 3, 500);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// current sensor
InlineCurrentSense current_sense = InlineCurrentSense(0.01, 50.0, A0, A2);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }

void setup() { 
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  driver.init();
  // link driver
  motor.linkDriver(&driver);
  // link the driver to the current sense
  current_sense.linkDriver(&driver);

  // current sense init hardware
  current_sense.init();
  // link the current sense to the motor
  motor.linkCurrentSense(&current_sense);

  // set torque mode:
  motor.torque_controller = TorqueControlType::foc_current; 
  // set motion control loop to be used
  motor.controller = MotionControlType::torque;

  // foc current control parameters (Arduino UNO/Mega)
  motor.PID_current_q.P = 5;
  motor.PID_current_q.I= 300;
  motor.PID_current_d.P= 5;
  motor.PID_current_d.I = 300;
  motor.LPF_current_q.Tf = 0.01; 
  motor.LPF_current_d.Tf = 0.01; 

  // use monitoring with serial 
  Serial.begin(115200);
  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  motor.init();
  // align sensor and start FOC
  motor.initFOC();

  // add target command T
  command.add('T', doTarget, "target current");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target current using serial terminal:"));
  _delay(1000);
}

void loop() {

  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // user communication
  command.run();
}
```

</div>