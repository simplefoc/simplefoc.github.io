---
layout: default
title: Getting started
nav_order: 1
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /example_from_scratch
parent: Writing the Code
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---


# Let's get started

Once you have your <span class="simple">Simple<span class="foc">FOC</span>library</span> [installed](installation) and you have all the necessary [hardware](supported_hardware), we can finally start the fun part, let's write the code and move the motor!

## Step 1. Testing the sensor
The first sign that tells us that everything is well connected is if the sensor readings are good. To test the sensor please browse the library examples `examples/utils/sensor_test` and find the example for your sensor. 
The example will have a structure something like this:



<a href="javascript:show('enc','sensor');" class="btn btn-sensor btn-enc btn-primary">Encoder</a> 
<a href ="javascript:show('mspi','sensor');"  class="btn btn-sensor btn-mspi">Magnetic sensor SPI</a>
<a href ="javascript:show('mi2c','sensor');"  class="btn btn-sensor btn-mi2c">Magnetic sensor I2C</a>
<a href ="javascript:show('manalog','sensor');"  class="btn btn-sensor btn-manalog">Magnetic sensor Analog</a>
<a href ="javascript:show('hall','sensor');"  class="btn btn-sensor btn-hall">Hall sensors</a>


<div class="sensor sensor-manalog hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// MagneticSensorAnalog(uint8_t _pinAnalog, int _min, int _max)
// - pinAnalog      - the pin that is reading the pwm from magnetic sensor
// - min_raw_count  - the smallest expected reading. 
// - max_raw_count  - the largest value read. 
MagneticSensorAnalog sensor = MagneticSensorAnalog(A1, 14, 1020);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // initialise magnetic sensor hardware
  sensor.init();

  Serial.println("Sensor ready");
  _delay(1000);
}

void loop() {
  // iterative function updating the sensor internal variables
  // it is usually called in motor.loopFOC()
  // this function reads the sensor hardware and 
  // has to be called before getAngle nad getVelocity
  sensor.update();
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```

</div>


<div class="sensor sensor-mi2c hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// Example of AS5600 configuration 
MagneticSensorI2C sensor = MagneticSensorI2C(AS5600_I2C);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // configure i2C
  Wire.setClock(400000);
  // initialise magnetic sensor hardware
  sensor.init();

  Serial.println("Sensor ready");
  _delay(1000);
}

void loop() {
  // iterative function updating the sensor internal variables
  // it is usually called in motor.loopFOC()
  // this function reads the sensor hardware and 
  // has to be called before getAngle nad getVelocity
  sensor.update();
  
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```

</div>


<div class="sensor sensor-mspi  hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// MagneticSensorSPI(MagneticSensorSPIConfig_s config, int cs)
//  config  - SPI config
//  cs      - SPI chip select pin 
MagneticSensorSPI sensor = MagneticSensorSPI(AS5147_SPI, 10);

void setup() {
  // monitoring port
  Serial.begin(115200);

  // initialise magnetic sensor hardware
  sensor.init();

  Serial.println("Sensor ready");
  _delay(1000);
}

void loop() {
  // iterative function updating the sensor internal variables
  // it is usually called in motor.loopFOC()
  // this function reads the sensor hardware and 
  // has to be called before getAngle nad getVelocity
  sensor.update();
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```

</div>


<div class="sensor sensor-hall  hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// Hall sensor instance
// HallSensor(int hallA, int hallB , int cpr, int index)
//  - hallA, hallB, hallC    - HallSensor A, B and C pins
//  - pp                     - pole pairs
HallSensor sensor = HallSensor(2, 3, 4, 14);

void setup() {
  // monitoring port
  Serial.begin(115200);
  
  // initialise encoder hardware
  sensor.init();

  Serial.println("Sensor ready");
  _delay(1000);
}

void loop() {
  // iterative function updating the sensor internal variables
  // it is usually called in motor.loopFOC()
  sensor.update();
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
  delay(100);
}
```

</div>


<div class="sensor sensor-enc"  markdown="1">

```cpp
#include <SimpleFOC.h>

Encoder encoder = Encoder(2, 3, 500);
// interrupt routine initialisation
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

void setup() {
  // monitoring port
  Serial.begin(115200);
  
  // initialise encoder hardware
  sensor.init();
  // hardware interrupt enable
  encoder.enableInterrupts(doA, doB);

  Serial.println("Encoder ready");
  _delay(1000);
}

void loop() {
  // IMPORTANT
  // read sensor and update the internal variables
  sensor.update();
  // display the angle and the angular velocity to the terminal
  Serial.print(sensor.getAngle());
  Serial.print("\t");
  Serial.println(sensor.getVelocity());
}
```

</div>
Make sure to change the sensor parameters to suit your application, such as pin numbers, impulses per revolution, bus addresses and similar. Make sure to go through the [sensor docs](sensors) if you are not sure about some of the parameters.  


If your sensor is well connected and if everything works well, you should have in your serial terminal an output of your sensor angle and velocity. 

<blockquote class="info" markdown="1"> <p class="heading">☑️ Simple test</p> Make sure to test that one rotation of the motor gives you the output of <b>6.28</b>($$2\pi$$) `RAD`. <br>
If you're new to radians `RAD`, you can find more info about the library units in the [here](library_units).
 </blockquote>

## Step 2. Testing just the driver
Once your sensor is working you can proceed to the driver test. The simplest way to test the driver is to use the library examples. If you have the luxury of time you can test the driver using the examples in the `examples/utils/driver_standalone_test` folder. These examples test the driver as a standalone module and with them you can set any voltage value to any of the driver phases. 




<a href="javascript:show('3pwm','driver');" class="btn btn-driver btn-3pwm btn-primary">3PMW BLDC Driver</a> 
<a href ="javascript:show('6pwm','driver');"  class="btn btn-driver btn-6pwm">6PMW BLDC Driver</a> 
<a href ="javascript:show('2pwm','driver');"  class="btn btn-driver btn-2pwm">2PWM Stepper Driver</a>
<a href ="javascript:show('4pwm','driver');"  class="btn btn-driver btn-4pwm">4PWM Stepper Driver</a>


<div class="driver driver-3pwm"  markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDCDriver3PWM(pwmA, pwmB, pwmC, (en optional))
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

void setup() {
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = 12;

  // driver init
  if (!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }

  // enable driver
  driver.enable();
  Serial.println("Driver ready!");
  _delay(1000);
}

void loop() {
    // setting pwm
    // phase A: 3V
    // phase B: 6V
    // phase C: 5V
    driver.setPwm(3,6,5);
}
```
</div>

<div class="driver driver-6pwm hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// BLDCDriver6PWM(pwmAh, pwmAl, pwmBh, pwmBl, pwmCh, pwmCl, (en optional))
BLDCDriver6PWM driver = BLDCDriver6PWM(5, 6, 9,10, 3, 11, 8);

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = 12;
  // daad_zone [0,1] - default 0.02f - 2%
  driver.dead_zone = 0.05f;

  // driver init
  if (!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }

  // enable driver
  driver.enable();
  Serial.println("Driver ready!");
  _delay(1000);
}

void loop() {
    // setting pwm
    // phase A: 3V
    // phase B: 6V
    // phase C: 5V
    driver.setPwm(3,6,5);
}
```

</div>

<div class="driver driver-2pwm hide"  markdown="1">

```cpp
#include <SimpleFOC.h>

// StepperDriver2PWM(pwm1, dir1, pwm2, dir2,(en1, en2 optional))
StepperDriver2PWM driver = StepperDriver2PWM(3, 4, 5, 6, 11, 12);

void setup() {
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = 12;
  
  // driver init
  if (!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }

  // enable driver
  driver.enable();
  Serial.println("Driver ready!");
  _delay(1000);
}

void loop() {
    // setting pwm
    // phase A: 3V
    // phase B: 6V
    driver.setPwm(3,6);
}
```
</div>

<div class="driver driver-4pwm hide"  markdown="1">


```cpp
#include <SimpleFOC.h>

// StepperDriver4PWM(ph1A, ph1B, ph2A, ph2B, (en1, en2 optional))
StepperDriver4PWM driver = StepperDriver4PWM(5, 6, 9,10, 7, 8);

void setup() {
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // Max DC voltage allowed - default voltage_power_supply
  driver.voltage_limit = 12;
  
  // driver init
  if (!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }

  // enable driver
  driver.enable();
  Serial.println("Driver ready!");
  _delay(1000);
}

void loop() {
    // setting pwm
    // phase A: 3V
    // phase B: 6V
    driver.setPwm(3,6);
}
```
</div>

Once you have the code ready, upload it to your board and open the serial terminal. If everything is connected correctly your PWM driver should be ready and you should see the `Driver ready!` message in the serial terminal. If the driver does not initialise correctly, please check the following things:
1. Make sure to change the driver parameters to suit your application, such as pin numbers, PWM frequency, power supply voltage and similar. Make sure to go through the [driver docs](drivers_config) if you are not sure about some of the parameters.  
2. Make sure you use the right driver class and right pwm pins, this one is usually very simple and you can find a lot of docs about these things in our [driver docs](drivers_config) and in our [guide to choosing the PWM pins](choosing_pwm_pins). If you are not sure and cannot seem to make it work please do not hesitate to ask in our [community forum](https://community.simplefoc.com).

<blockquote class="info" markdown="1"> <p class="heading">☑️ Simple tests</p>
1. Open your serial monitor and verify that the `Driver ready!` has been displayed. If the `Driver init failed!` has been displayed, check the debugging output for more information what went wrong.
2. Make sure that all phases output PWM signals, you can try to connect a small led light in between each phase and ground or just measure it with the multimeter.
</blockquote>

## Step 2. Testing the driver + motor combination - open-loop
If you already have your motor connected and if you are sure your driver works well we advise you to test the motor+driver combination using the Open-Loop control examples in the `examples/motion_control/open_loop_motion_control`. If your driver is not the same as the ones provided in the examples, go though the [driver docs](drivers_config) and find the driver and the code that will work for you. Additionally you can browse through the examples in the `examples/utils/driver_standalone_test` folder and see the used in there.

<blockquote class="warning"> <p class="heading">Please Note</p>
Before you try this example, please take at least a short look at the rules just below the code. From here on, actual current is flowing through your electronics and your driver has no way of measuring it (at least not in this example). </blockquote>

So the main aim of this step is to test the motor parameters, such as the number of pole pairs, and verify that the driver is capable of driving the motor.

<a href="javascript:show('bldc3','open');" class="btn btn-open btn-bldc3 btn-primary">BLDC Motor + 3PMW driver</a>
<a href="javascript:show('bldc6','open');" class="btn btn-open btn-bldc6">BLDC Motor + 6PMW driver</a> 
<a href ="javascript:show('stepper2','open');"  class="btn btn-open btn-stepper2">Stepper Motor + 2PMW driver</a> 
<a href ="javascript:show('stepper4','open');"  class="btn btn-open btn-stepper4">Stepper Motor + 4PMW driver</a> 

<div class="open open-bldc3" markdown="1">

Now, here is an example of the open-loop velocity control for the **BLDC motor** using the **3PWM driver**. 

```cpp
#include <SimpleFOC.h>

// BLDCMotor(pole pair number, phase resistance (optional) );
BLDCMotor motor = BLDCMotor(11);
// BLDCDriver3PWM(pwmA, pwmB, pwmC, Enable(optional));
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 5, 6, 8);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // limit the maximal dc voltage the driver can set
  // as a protection measure for the low-resistance motors
  // this value is fixed on startup
  driver.voltage_limit = 6;
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // limit the voltage to be set to the motor
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = 3;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="open open-bldc6 hide" markdown="1">


Now, here is an example of the open-loop velocity control for the **BLDC motor** using the **6PWM driver**. 

```cpp
#include <SimpleFOC.h>

// BLDCMotor(pole pair number, phase resistance (optional) );
BLDCMotor motor = BLDCMotor(11);
// BLDCDriver6PWM(pwmAh, pwmAl, pwmBh, pwmBl, pwmCh, pwmCl, (en optional))
BLDCDriver6PWM driver = BLDCDriver6PWM(5, 6, 9,10, 3, 11, 8);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // limit the maximal dc voltage the driver can set
  // as a protection measure for the low-resistance motors
  // this value is fixed on startup
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // limit the voltage to be set to the motor
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = 3;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```

</div>

<div class="open open-stepper2 hide"  markdown="1">


Now, here is an example of the open-loop velocity control for the **Stepper motor** using the **2PWM driver**.

```cpp
#include <SimpleFOC.h>

// StepperMotor(pole pair number, phase resistance (optional) );
StepperMotor motor = StepperMotor(50);
// StepperDriver2PWM(pwm1, dir1, pwm2, dir2,(en1, en2 optional))
StepperDriver2PWM driver = StepperDriver2PWM(9, 5, 6, 8);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // limit the maximal dc voltage the driver can set
  // as a protection measure for the low-resistance motors
  // this value is fixed on startup
  driver.voltage_limit = 6;
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // limit the voltage to be set to the motor
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = 3;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```
</div>

<div class="open open-stepper4 hide"  markdown="1">


Now, here is an example of the open-loop velocity control for the **Stepper motor** using the **4PWM driver**.

```cpp
#include <SimpleFOC.h>

// StepperMotor(pole pair number, phase resistance (optional) );
StepperMotor motor = StepperMotor(50);
// StepperDriver4PWM(ph1A, ph1B, ph2A, ph2B, (en1, en2 optional))
StepperDriver4PWM driver = StepperDriver4PWM(5, 6, 9,10, 7, 8);

// instantiate the commander
Commander command = Commander(Serial);
void doTarget(char* cmd) { command.scalar(&motor.target, cmd); }
void doLimit(char* cmd) { command.scalar(&motor.voltage_limit, cmd); }

void setup() {

  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // limit the maximal dc voltage the driver can set
  // as a protection measure for the low-resistance motors
  // this value is fixed on startup
  driver.voltage_limit = 6;
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link the motor and the driver
  motor.linkDriver(&driver);

  // limiting motor movements
  // limit the voltage to be set to the motor
  // start very low for high resistance motors
  // current = voltage / resistance, so try to be well under 1Amp
  motor.voltage_limit = 3;   // [V]
 
  // open loop control config
  motor.controller = MotionControlType::velocity_openloop;

  // init motor hardware
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }

  // set the target velocity [rad/s]
  motor.target = 6.28; // one rotation per second

  // add target command T
  command.add('T', doTarget, "target velocity");
  command.add('L', doLimit, "voltage limit");

  Serial.println("Motor ready!");
  Serial.println("Set target velocity [rad/s]");
  _delay(1000);
}

void loop() {

  // open loop velocity movement
  motor.move();

  // user communication
  command.run();
}
```
</div>

Once you have the code ready, upload it to your board and open the serial terminal. If everything is connected correctly you should see the motor spinning. If the motor does not spin, please check the following things:
1. Make sure you use the right driver class and right pwm pins, this one is usually very simple and you can find a lot of docs about these things in our [driver docs](drivers_config) and in our [guide to choosing the PWM pins](choosing_pwm_pins). 
2. Make sure to use a proper voltage limit. All of these examples assume you are using a gimbal type motor with high phase resistance (~10 Ohms). For higher power motors, the current will probably be too high, putting your motor+driver at risk. As a rule of thumb, if your motor has a servo type plug, you should be fine.
`motor.voltage_limit` will directly determine the current passing through the motor: `current = phase_resistance*motor.voltage_limit`. So to avoid too high currents please try to find what is the phase resistance of your motor and set the `motor.voltage_limit` such that the current does not surpass 2 Amps for example: `motor.voltage_limit = 2*phase_resistance`. The best option would be to provide your phase resistance value to the motor by setting the parameter `motor.phase_resistance` and then you can use `motor.current_limit` instead of the `motor.voltage_limit` making this issue much simpler. If you wanna try to measure the phase resistance of your motor you can find the our guide [here](phase_resistance).
If you cannot find the phase resistance of your motor and cannot measure it with your multimeter start small: `motor.voltage_limit < 1;`.
3. Make sure to put the right pole pair number. This you can find in most data-sheets, if you are not sure what is the true number don't worry this step is intended to test/find exactly this value. 😄

<blockquote class="info" markdown="1"> <p class="heading">☑️ Simple tests</p> 
1. Verify in your serial terminal that the motor and the driver have been initialised without any issues. That you can see `Motor ready!` and not see `Motor init failed!` or `Driver init failed!`. 
2. In velocity mode: (`motor.controller = MotionControlType::velocity_openloop;`) <br>
    set your target velocity (`T6.28`) to <b>6.28 ($$2\pi$$) rad/s</b> (type `T6.28`), this should be exactly one rotation per second.
3. In position mode, (`motor.controller = MotionControlType::angle_openloop;`)  <br>
    set the target position to <b>6.28 ($$2\pi$$) rad</b> (type `T6.28`),  it should be exactly one rotation.

 If your motor does not spin exactly one rotation per second in velocity mode or does not do exactly one rotation in angle mode, this probably means that your pole pairs number is not good. Try changing it until you get exactly one rotation (or one rotation per second in velocity mode)
 
 </blockquote>

## Step 3. Closed-loop control - torque using voltage

Once you have a working sensor, working motor and driver you can proceed to the Closed-Loop control testing. The first one to test is the torque control mode using voltage, this one is the simplest form of the closed loop control that is available in the <span class="simple">Simple<span class="foc">FOC</span>library</span>. You can find the examples of this torque mode for different sensors in the library examples folder `examples/motion_control/torque_control`.

<a href="javascript:show('temp','cl');" class="btn btn-cl btn-temp btn-primary">Sketch template</a>
<a href="javascript:show('bldc','cl');" class="btn btn-cl btn-bldc">BLDC Motor + 3PMW driver + Encoder</a> 
<a href ="javascript:show('stepper','cl');"  class="btn btn-cl btn-stepper">Stepper Motor + 2PMW driver + Encoder</a> 



<div class="cl cl-temp" markdown="1">


Here is an example sketch with the `TODO` entries that you can implement for your motor, driver and sensor.

```cpp
#include <SimpleFOC.h>

// TODO: motor instance 
// TODO: driver instance

// TODO: sensor instance

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // TODO: driver config
  // init the sensor
  sensor.init()
  // link the motor to the sensor
  motor.linkSensor(&sensor);

  // TODO: driver config
  // init the driver
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link driver
  motor.linkDriver(&driver);

  // TODO: motor conf

  // set motion control loop to be used
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // use monitoring with serial
  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }
  // align sensor and start FOC
  motor.initFOC();

  // set the initial motor target
  motor.target = 2; // Volts 

  // add target command M
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target using serial terminal and command M:"));
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

<div class="cl cl-bldc hide" markdown="1">

Here is an example sketch for the **BLDC motor** using the **3PWM driver** and **Encoder** sensor.

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

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // driver init
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link driver
  motor.linkDriver(&driver);

  // aligning voltage
  motor.voltage_sensor_align = 5;

  // set motion control loop to be used
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }
  // align sensor and start FOC
  motor.initFOC();

  // set the initial motor target
  motor.target = 2; // Volts 

  // add target command M
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target using serial terminal and command M:"));
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

<div class="cl cl-stepper hide" markdown="1">

Here is an example sketch for the **Stepper motor** using the **2PWM driver** and **Encoder** sensor.

```cpp
#include <SimpleFOC.h>

// Stepper motor & driver instance
StepperMotor motor = StepperMotor(11);
StepperDriver2PWM driver = StepperDriver2PWM(9, 5, 6, 8);

// encoder instance
Encoder encoder = Encoder(2, 3, 500);
// channel A and B callbacks
void doA(){encoder.handleA();}
void doB(){encoder.handleB();}

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // driver init
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link driver
  motor.linkDriver(&driver);

  // aligning voltage
  motor.voltage_sensor_align = 5;

  // set motion control loop to be used
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // comment out if not needed
  motor.useMonitoring(Serial);

  // initialize motor
  motor.init();
  // align sensor and start FOC
  motor.initFOC();

  // set the initial motor target
  motor.target = 2; // Volts 

  // add target command M
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target using serial terminal and command M:"));
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

Until now, you already know the good configuration of your sensor, your driver and your motor. If everything worked until this point you most probably will not have any issues with this step as well. You can adapt the above examples to your setup and upload the code to your board.

<blockquote class="info" markdown="1"> <p class="heading">☑️ Simple test</p> 
1.  If everything is connected correctly you should see the motor spinning and you should be able to set the target voltage through the serial terminal (command `M`). 
2.  If the motor is not spinning please verify that the motor and driver have been initialised without any issues by checking the serial terminal output (make sure that you can see `Motor ready!` and not see `Motor init failed!` or `Driver init failed!`). 
</blockquote>

The most common issues that might arise are:
- The motor does not spin at all
   - The driver is probably not initialised correctly, check the serial terminal output for any errors.
- The motor spins during the `motor.initFOC()` function but it does not spin afterwards
   - Verify the output of the `motor.initFOC()` function, there is probably an error with the sensor alignment or pole pairs number.
- The motor spins but is unstable and makes a lot of noise
   - Verify the output of the `motor.initFOC()` function 
      - Check the pole pair check and if it failed it probably means that the pole pairs number is not correct, try to change it.
      - If everything seams OK in the serial terminal the sensor is probably not aligned correctly, try to rise/lower the `motor.voltage_sensor_align` value.

<blockquote class="warning" markdown="1"> <p class="heading">Please Note</p>
Make sure you use not too high `motor.voltage_sensor_align` value to prevent too high currents. The rule is the same as for the `motor.voltage_limit` for the open loop. If you are not sure what is your phase resistance, start small: `motor.voltage_sensor_align < 1`. Additionally, you can define the motor phase resistance as well and then use the `motor.current_limit` variable, this variable will limit the `motor.voltage_sensor_align` and you will not have to worry about it any more. But if you specify the phase resistance value you will no longer be setting voltage command to the motor but current command, see [torque control docs](voltage_torque_mode) for more info.
</blockquote>

### Step 3.1. Velocity and angle control

Once the above code works well, you can proceed to the velocity and angle motion control. 
- To go from the torque control to the velocity control you just need to change the `motor.controller` variable to `MotionControlType::velocity` and set the `motor.target` to the desired velocity (in radians per second `RAD/s`). 
- To go from the torque control to the angle control you just need to change the `motor.controller` variable to `MotionControlType::angle` and set the `motor.target` to the desired angle (in radians `RAD`). 
- Another way of transitioning to the velocity control is using the serial terminal through the `Commander` interface
  - To start the velocity control mode type `MC1` in the serial terminal and press enter.
    - To set the target velocity type `M6.28` in the serial terminal and press enter. This will set the target velocity to one rotation per second.
    - To stop the motor type `M0` in the serial terminal and press enter. This will set the target velocity to zero.
  - To start the angle control mode type `MC2` in the serial terminal and press enter.
    - To set the target angle type `M0` in the serial terminal and press enter. This will set the target angle to one rotation.
    - Once your motor is at the `0` angle, try setting the target angle to `M6.28` in the serial terminal and press enter. This will set the target angle to one rotation.
  - To go back to the torque control mode type `MC0` in the serial terminal and press enter.
  - See more about the `Commander` interface in the [Commander docs](commander_interface).

The velocity and angle motion control modes have additional configuration parameters that might be needed to obtain a smooth and stable operation of your motor. You can find more info about these parameters in the [motion control docs](closed_loop_motion_control).

## Step 4. Testing the current sense - if available
If your setup has current sensing that is supported by the <span class="simple">Simple<span class="foc">FOC</span>library</span> then we would suggest you to still make sure that you can run at least closed-loop torque control using voltage (Step 3.) before doing this step. 

The best way to start is to add the current sensing to the code of the torque control using voltage (Step 3.) and output the d and q currents to the serial terminal using monitoring.

<a href="javascript:show('temp','current');" class="btn btn-current btn-temp btn-primary">Sketch template</a>
<a href="javascript:show('bldc','current');" class="btn btn-current btn-bldc">BLDC Motor + 3PMW driver + Encoder + Inline Current Sensing</a> 
<a href ="javascript:show('stepper','current');"  class="btn btn-current btn-stepper">Stepper Motor + 2PMW driver + Encoder + Inline Current Sensing</a> 



<div class="current current-temp" markdown="1">


Here is an example sketch with the `TODO` entries that you can implement for your motor, driver, current sense and sensor.

```cpp
#include <SimpleFOC.h>

// TODO: motor instance 
// TODO: driver instance

// TODO: sensor instance

// TODO: current sense instance

// instantiate the commander
Commander command = Commander(Serial);
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);

  // TODO: driver config
  // init the sensor
  sensor.init()
  // link the motor to the sensor
  motor.linkSensor(&sensor);

  // TODO: driver config
  // init the driver
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link driver
  motor.linkDriver(&driver);
  // link driver to cs
  current_sense.linkDriver(&driver);

  // TODO: current sense config
  // current sense init
  if(!current_sense.init()){
    Serial.println("Current sense init failed!");
    return;
  }
  // link the current sense to the motor
  motor.linkCurrentSense(&current_sense);

  // TODO: motor conf

  // set motion control loop to be used
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // use monitoring with serial
  // comment out if not needed
  motor.useMonitoring(Serial);
  motor.monitor_downsampling = 100; // set downsampling can be even more > 100
  motor.monitor_variables = _MON_CURR_Q | _MON_CURR_D; // set monitoring of d and q currents

  // initialize motor
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }
  // align sensor and start FOC
  motor.initFOC();

  // set the initial motor target
  motor.target = 2; // Volts 

  // add target command M
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target using serial terminal and command M:"));
  _delay(1000);
}

void loop() {
  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // display the currents
  motor.monitor();
  // user communication
  command.run();
}
```
</div>

<div class="current current-bldc hide" markdown="1">

Here is the an example code for a **BLDC motor** using the **3PWM driver**, **Encoder** and **Inline Current Sensing**.

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
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // driver init
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link driver
  motor.linkDriver(&driver);
  // link driver to cs
  current_sense.linkDriver(&driver);

  // current sense init hardware
  if(!current_sense.init()){
    Serial.println("Current sense init failed!");
    return;
  }
  // link the current sense to the motor
  motor.linkCurrentSense(&current_sense);

  // aligning voltage
  motor.voltage_sensor_align = 5;

  // set motion control loop to be used
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // use monitoring with serial
  // comment out if not needed
  motor.useMonitoring(Serial);
  motor.monitor_downsampling = 100; // set downsampling can be even more > 100
  motor.monitor_variables = _MON_CURR_Q | _MON_CURR_D; // set monitoring of d and q currents

  // initialize motor
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }
  // align sensor and start FOC
  motor.initFOC();

  // set the initial motor target
  motor.target = 2; // Volts 

  // add target command M
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target using serial terminal and command M:"));
  _delay(1000);
}

void loop() {
  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // display the currents
  motor.monitor();
  // user communication
  command.run();
}
```
</div>

<div class="current current-stepper hide" markdown="1">

Here is the an example code for a **Stepper motor** using the **2PWM driver**, **Encoder** and **Inline Current Sensing**.
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
void doMotor(char* cmd) { command.motor(&motor, cmd); }

void setup() { 
  
  // use monitoring with serial 
  Serial.begin(115200);
  // enable more verbose output for debugging
  // comment out if not needed
  SimpleFOCDebug::enable(&Serial);
  
  // initialize encoder sensor hardware
  encoder.init();
  encoder.enableInterrupts(doA, doB); 
  // link the motor to the sensor
  motor.linkSensor(&encoder);

  // driver config
  // power supply voltage [V]
  driver.voltage_power_supply = 12;
  // driver init
  if(!driver.init()){
    Serial.println("Driver init failed!");
    return;
  }
  // link driver
  motor.linkDriver(&driver);
  // link driver to cs
  current_sense.linkDriver(&driver);

  // current sense init hardware
  if(!current_sense.init()){
    Serial.println("Current sense init failed!");
    return;
  }
  // link the current sense to the motor
  motor.linkCurrentSense(&current_sense);

  // aligning voltage
  motor.voltage_sensor_align = 5;

  // set motion control loop to be used
  motor.torque_controller = TorqueControlType::voltage;
  motor.controller = MotionControlType::torque;

  // use monitoring with serial
  // comment out if not needed
  motor.useMonitoring(Serial);
  motor.monitor_downsampling = 100; // set downsampling can be even more > 100
  motor.monitor_variables = _MON_CURR_Q | _MON_CURR_D; // set monitoring of d and q currents

  // initialize motor
  if(!motor.init()){
    Serial.println("Motor init failed!");
    return;
  }
  // align sensor and start FOC
  motor.initFOC();

  // set the initial motor target
  motor.target = 2; // Volts 

  // add target command M
  command.add('M', doMotor, "Motor");

  Serial.println(F("Motor ready."));
  Serial.println(F("Set the target using serial terminal and command M:"));
  _delay(1000);
}

void loop() {
  // main FOC algorithm function
  motor.loopFOC();

  // Motion control function
  motor.move();

  // display the currents
  motor.monitor();
  // user communication
  command.run();
}
```
</div>

This example code is almost exactly the same as the Step 3. code so you should not have much trouble configuring motor, sensor and driver. In this step you will be testing if your current sense is working well. In the call of the `motor.monitor()` function the current sense will be read and the current d and q are going to be printed to the serial terminal. You can open the Serial Plotter to visualise them. 

<blockquote class="info" markdown="1"> <p class="heading">☑️ Simple test</p> 
1. If everything is connected correctly you should see the motor spinning and you should be able to set the target voltage through the serial terminal (command `M`). 
2. Hold the motor with your hand and set different target voltage/current values. Make sure that the current d is very close to 0 when the motor is static. And make sure that the current q is proportional to the voltage you are setting to the motor. <br>
3. Leave the motor to rotate. See that your currents d and q drop to a lower level than for static motor. Also try to see that the current d is almost 0 for low velocities and starts rising proportionally to the motor velocity. 
</blockquote>
  
If the motor is not spinning please verify that the motor and driver have been initialised without any issues by checking the serial terminal output (make sure that you can see `Motor ready!` and not see `Motor init failed!`, `Driver init failed!` or `Current sense init failed!`). Our debugging interface will output a lot of information during the driver, current sense and motor init that can help you debug potential issues with your setup. If you are having issues with the current sense please go through the [current sense docs](current_sense) to see the supported sensors and all the configuration parameters. 

The most common issues that might arise are:
- Current sense pins are not ADC pins - see our [guide to choosing ADC pins](choosing_adc_pins) for more info.
- The driver is not linked to the current sense - make sure you have the `current_sense.linkDriver(&driver);` line in your code.
- PWM timer and ADC cannot be synchronised - see our [guide to choosing PWM pins](choosing_pwm_pins) for more info.
- Current sense alignment has failed - verify the debugging output of the `motor.initFOC()` function and try to change the `motor.voltage_sensor_align` value. 

## Step 5. Full FOC motion control using current sensing - if available

Once you have your motor, position sensor, driver and current sense configured and tested you can now proceed to try out true field oriented control (FOC). 

- To go from the torque control using voltage to the torque control using current you just need to change the `motor.torque_controller` variable to `TorqueControlType::foc_current` and set the `motor.controller` variable to `MotionControlType::torque`.
- Another way of transitioning to the current control is using the serial terminal through the `Commander` interface
  - To start the current control mode type `MT2` in the serial terminal and press enter.
    - To set the target current type `M0.5` (in amperes) in the serial terminal and press enter. This will set the target current to 0.5 Amps.
    - To stop the motor type `M0` in the serial terminal and press enter. This will set the target current to zero.
  - To go back to the voltage control mode type `MT0` in the serial terminal and press enter.
  - See more about the `Commander` interface in the [Commander docs](commander_interface).


<blockquote class="info"> <p class="heading">☑️ Simple test</p> 
1. Set your target current to <b>0Amps</b> and try to move the motor by hand, make sure it feels like there is absolutely no resistance, like the motor is disabled. 
2. Then try to set a small current (<b><0.5A</b>) value and see if you can feel the motor force acting on your hand. If you can feel it you should be ready to go! 
</blockquote>


The current control mode has additional configuration parameters that might be needed to obtain a smooth and stable operation of your motor. You can find more info about these parameters in the [torque control docs](torque_control).

However the most important tip is still to use the [commander interface](commander_interface) to tune the torque control PID controller and Low pass filter parameters. This way you will be able to change and test the parameters of controllers in real-time and see what will happen. Once you are satisfied, you can write these values into your code and quit using the commander. 

### Step 5.1. Velocity and angle control

Once the current control works well you can proceed to the velocity and angle motion control.
- To go from the current control to the velocity control you just need to change the `motor.controller` variable to `MotionControlType::velocity` and set the `motor.target` to the desired velocity (in radians per second `RAD/s`).
- To go from the current control to the angle control you just need to change the `motor.controller` variable to `MotionControlType::angle` and set the `motor.target` to the desired angle (in radians `RAD`).
- You can also use the `Commander` interface to transition to the velocity and angle control modes using the `MC1` and `MC2` commands.
