---
layout: default
title: Generic Current Sense
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /generic_current_sense
nav_order: 4
parent: Current Sensing
grand_parent: Writing the Code
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---



# Implementing a custom current sense

`GenericCurrentSense` is a helper class that makes it easy to integrate a custom current-sensing setup into the <span class="simple">Simple<span class="foc">FOC</span>library</span>. You provide callbacks to read phase currents (and optionally initialize your hardware), and the library handles offsets, filtering, and integration with FOC control.

For advanced users, you can also implement your own class by inheriting `CurrentSense`, but `GenericCurrentSense` is usually faster to get working.

[See library source code for more details (Advanced)](current_sense_support){: .btn .btn-docs}

## Step 1. Implement the current-reading callback

Provide a function that returns phase currents in **amps**. If you only measure two phases, set the third one to `0`.

```cpp
PhaseCurrent_s readCurrentSense(){
        PhaseCurrent_s c;
        // Read your ADCs and convert to amps
        c.a = ...; // phase A current [A]
        c.b = ...; // phase B current [A]
        c.c = ...; // phase C current [A] (set to 0 if not measured)
        return c;
}
```

You can also provide an optional initialization callback:

```cpp
void initCurrentSense(){
	// Configure ADCs, GPIOs, or any hardware setup
}
```

## Step 2. Instantiate `GenericCurrentSense`

```cpp
// GenericCurrentSense constructor
//  - readCallback pointer to the function reading phase currents
//  - initCallback pointer to the optional init function
GenericCurrentSense current_sense = GenericCurrentSense(readCurrentSense, initCurrentSense);
```

If you prefer, you can also assign callbacks after construction:

```cpp
GenericCurrentSense current_sense;
current_sense.readCallback = readCurrentSense;
current_sense.initCallback = initCurrentSense;
```

## Step 3. Use as a standalone current sensor

Once initialized, you can read phase currents and DC current directly:

```cpp
current_sense.init();

PhaseCurrent_s currents = current_sense.getPhaseCurrents();
float current_magnitude = current_sense.getDCCurrent();

Serial.print(currents.a);
Serial.print("\t");
Serial.print(currents.b);
Serial.print("\t");
Serial.print(currents.c);
Serial.print("\t");
Serial.println(current_magnitude);
```

<blockquote markdown="1" class="info">
<p class="heading" markdown="1">Offset calibration</p>
`GenericCurrentSense::init()` automatically runs a zero-offset calibration by sampling your callback multiple times. Make sure the motor is not driven during initialization.
</blockquote>

## Step 4. Use with FOC control

To use current sensing for torque control (dc_current or foc_current), link it to the motor and driver:

```cpp
// Driver and motor
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 10, 11, 8);
BLDCMotor motor = BLDCMotor(7);

void setup() {
	Serial.begin(115200);

	// Driver init
	driver.voltage_power_supply = 12;
	driver.init();
	motor.linkDriver(&driver);

	// Current sense init and link
	current_sense.init();
	current_sense.linkDriver(&driver);
	motor.linkCurrentSense(&current_sense);

	// Select torque control mode
	motor.torque_controller = TorqueControlType::foc_current;
	// or TorqueControlType::dc_current

	motor.init();
	motor.initFOC();
}

void loop() {
	motor.loopFOC();
	motor.move();
}
```

## Tips and troubleshooting

- **Units matter**: your callback must return currents in **amps**.
- **Only two shunts?** Set one of the phases to `0` and the library will estimate it.
- **Phase inversion**: if a phase appears inverted, you can flip it:
	```cpp
	current_sense.gain_a *= -1; // or gain_b / gain_c
	```
- **Alignment**: `initFOC()` will not run the alignment for this sensor type, so make sure your callback returns correct angles from the start.

## Example: Generic current sensing test

```cpp
#include <SimpleFOC.h>

PhaseCurrent_s readCurrentSense(){
	PhaseCurrent_s c;
	c.a = analogRead(A0);
	c.b = analogRead(A1);
	c.c = analogRead(A2);
	return c;
}

void initCurrentSense(){
	pinMode(A0, INPUT);
	pinMode(A1, INPUT);
	pinMode(A2, INPUT);
}

GenericCurrentSense current_sense = GenericCurrentSense(readCurrentSense, initCurrentSense);

void setup() {
	Serial.begin(115200);
	current_sense.init();
	Serial.println("Current sense ready");
}

void loop() {
	PhaseCurrent_s currents = current_sense.getPhaseCurrents();
	float current_magnitude = current_sense.getDCCurrent();

	Serial.print(currents.a);
	Serial.print("\t");
	Serial.print(currents.b);
	Serial.print("\t");
	Serial.print(currents.c);
	Serial.print("\t");
	Serial.println(current_magnitude);
}
```
