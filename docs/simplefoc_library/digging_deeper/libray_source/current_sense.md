---
layout: default
title: Current sense support
parent: Library Source
grand_parent: Digging deeper
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
nav_order: 4
permalink: /current_sense_support
toc: true
---

# Generic current sense definition

The `CurrentSense` base class defines the interface for all current sensing implementations in the <span class="simple">Simple<span class="foc">FOC</span>library</span>. It is intentionally small and focuses on the minimum requirements needed by the FOC control loop.

Implementations must provide:
- `init()` to configure ADCs, triggers, and calibration
- `getPhaseCurrents()` to return phase currents **in amps**

The base class handles DQ transformations, DC current magnitude computation, and alignment helpers.

```cpp
class CurrentSense {
public:
    /** Initialize ADC hardware and synchronization */
    virtual int init() = 0;

    /** Link current sense to driver (sync if needed) */
    void linkDriver(FOCDriver *driver);

    /** Read phase currents (A, B, C) */
    virtual PhaseCurrent_s getPhaseCurrents() = 0;

    /** Read total DC current magnitude (for dc_current torque mode) */
    virtual float getDCCurrent(float angle_el = 0);

    /** Read d-q currents (for foc_current torque mode) */
    DQCurrent_s getFOCCurrents(float angle_el);

    /** Align current sense with motor driver */
    virtual int driverAlign(float align_voltage, bool modulation_centered = false);
};
```

# Supporting additional current sensing [v2.4.0](https://github.com/simplefoc/Arduino-FOC/releases)

To add a new current sensing method, extend `CurrentSense` and implement the required methods.

## Step 1. Header file `MyCurrentSense.h`

```cpp
#include <SimpleFOC.h>

class MyCurrentSense : public CurrentSense {
    public:
    MyCurrentSense(...);

    // Initialize ADCs and hardware
    int init() override;

    // Read phase currents in amps
    PhaseCurrent_s getPhaseCurrents() override;
};
```

## Step 2. Class implementation file `MyCurrentSense.cpp`

```cpp
#include "MyCurrentSense.h"

MyCurrentSense::MyCurrentSense(...) {
    // Store pins, gains, or hardware parameters
}

int MyCurrentSense::init() {
    // Configure ADCs, GPIOs, DMA, or sampling triggers
    // Optionally measure offsets
    return 1; // 1 = success, 0 = failure
}

PhaseCurrent_s MyCurrentSense::getPhaseCurrents() {
    PhaseCurrent_s c;
    c.a = ...; // phase A current [A]
    c.b = ...; // phase B current [A]
    c.c = ...; // phase C current [A] (set to 0 if not measured)
    return c;
}
```

## Step 3. Arduino program

```cpp
#include <SimpleFOC.h>
#include "MyCurrentSense.h"

// motor + driver
BLDCMotor motor = BLDCMotor(7);
BLDCDriver3PWM driver = BLDCDriver3PWM(9, 10, 11, 8);

// current sense
MyCurrentSense current_sense = MyCurrentSense(...);

void setup(){
    // driver config
    driver.voltage_power_supply = 12;
    driver.init();
    motor.linkDriver(&driver);

    // init current sense
    current_sense.init();
    current_sense.linkDriver(&driver);
    motor.linkCurrentSense(&current_sense);

    // enable current control mode
    motor.torque_controller = TorqueControlType::foc_current;

    // get ready for FOC
    motor.init();
    motor.initFOC();
}
void loop(){
    motor.loopFOC();
    motor.move();
}

```

## (Optional) Arduino program - stand-alone current sensing

```cpp
#include <SimpleFOC.h>
#include "MyCurrentSense.h"

MyCurrentSense current_sense = MyCurrentSense(...);

void setup(){
    Serial.begin(115200);
    current_sense.init();
}
void loop(){
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

## Notes and best practices

- **Units**: `getPhaseCurrents()` must return values in **amps**.
- **Two shunts**: If only two phases are measured, set the third to `0`.
- **Alignment**: `initFOC()` runs current-sense alignment unless `skip_align` is set.
- **Synchronization**: For low-side or high-side sensing, ensure ADC sampling is synchronized to PWM switching.

For built-in implementations, see:

[Inline Current Sense](inline_current_sense){: .btn .btn-docs}
[Low-Side Current Sense](low_side_current_sense){: .btn .btn-docs}
[High-Side Current Sense](high_side_current_sense){: .btn .btn-docs}
[Generic Current Sense Guide](generic_current_sense){: .btn .btn-docs}