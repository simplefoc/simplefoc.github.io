---
layout: default
title: Calibrated sensor
description: "Compensating magnetic sensor eccentricity errors with the CalibratedSensor from the SimpleFOCDrivers library."
nav_order: 2
permalink: /drivers_calibrated_sensor
parent: <span class="simple">Simple<span class="foc">FOC</span>Drivers</span>
grand_parent: libraries
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
toc: true
---

# Calibrated sensor

`CalibratedSensor` is a wrapper around any <span class="simple">Simple<span class="foc">FOC</span></span> sensor that compensates the angle errors caused by an imperfectly placed magnet. It measures the error once, stores it in a lookup table (LUT), and corrects every reading from then on.

Originally implemented by [@MarethyuPrefect](https://github.com/MarethyuPrefect), see the [forum thread](https://community.simplefoc.com/t/simplefoc-sensor-eccentricity-calibration/2212) for the background.

[Source and README](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/calibrated){: .btn .btn-github} [Example](https://github.com/simplefoc/Arduino-FOC-drivers/blob/master/examples/encoders/calibrated/sensor_calibration.ino){: .btn .btn-github}

## Why calibrate?

Magnetic sensors measure the angle of a magnet on the motor shaft. If the magnet is not perfectly centred over the sensor, the measured angle is distorted: it runs a bit fast over part of the turn and a bit slow over the rest. As an example, a 0.5mm offset with a 3mm magnet can cause an angle error of around 10%.

For FOC, the angle error means the current is applied at the wrong electrical angle, which shows up as:

- **torque ripple**, the motor feels "notchy" when turned by hand in torque mode
- **noise in velocity control**, especially at low speeds
- **reduced efficiency**, part of the current produces no torque

`CalibratedSensor` doesn't replace good mechanical alignment, but it removes most of the remaining error.

## How it works

1. The motor is turned slowly in open loop through several full turns, in both directions.
2. At fixed electrical angles, the sensor reading is compared to the commanded angle.
3. The differences are stored in a lookup table, 200 entries by default.
4. During operation, every sensor reading is corrected by interpolating the LUT.

During calibration the motor must be able to turn freely through several full turns, ideally without load.

## Usage

Set up and test your motor normally first. Only add the `CalibratedSensor` once everything works without it, so you can see the improvement.

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>
#include "encoders/calibrated/CalibratedSensor.h"

MagneticSensorSPI sensor = MagneticSensorSPI(AS5048_SPI, CS_PIN);
BLDCMotor motor = BLDCMotor(11);
BLDCDriver3PWM driver = BLDCDriver3PWM(PWM_A, PWM_B, PWM_C, EN);

// wrap the real sensor
CalibratedSensor sensor_calibrated = CalibratedSensor(sensor);

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  sensor.init();
  motor.linkSensor(&sensor);   // the real sensor is used during calibration

  driver.voltage_power_supply = 12;
  driver.init();
  motor.linkDriver(&driver);
  motor.init();

  sensor_calibrated.voltage_calibration = 3; // voltage applied during calibration
  sensor_calibrated.calibrate(motor);        // turns the motor, builds the LUT

  motor.linkSensor(&sensor_calibrated);      // use the calibrated sensor from now on
  motor.initFOC();
}
```

The order matters: `motor.init()`, then `calibrate()`, then link the calibrated sensor, then `motor.initFOC()`.

### Calibration voltage

`voltage_calibration` is the voltage applied to turn the motor in open loop during calibration. It should be high enough for the motor to follow the commanded angle smoothly without skipping, but not so high that the motor heats up. The `motor.voltage_sensor_align` value is a good starting point.

### LUT size

The constructor takes an optional LUT size, 200 entries by default:

```cpp
CalibratedSensor sensor_calibrated = CalibratedSensor(sensor, 400);
```

A larger LUT captures finer errors, at the cost of memory (2 bytes per entry) and a longer calibration.

## Skipping the calibration on startup

Calibrating on every startup takes a few seconds and turns the motor. Instead, you can calibrate once, print the result, and paste it into your code:

```cpp
sensor_calibrated.calibrate(motor);
sensor_calibrated.printLUT(motor, Serial);
```

This prints the LUT, the zero electrical angle and the sensor direction, ready to copy:

```cpp
uint16_t calibrationLut[200] = {32701, 32694, 32689, /* ... */ 32708};
float zero_electric_angle = 2.74;
Direction sensor_direction = Direction::CCW;
```

Then pass the LUT to the constructor, set the zero angle and direction on the motor, and don't call `calibrate()`:

```cpp
CalibratedSensor sensor_calibrated = CalibratedSensor(sensor, 200, calibrationLut);

void setup() {
  // ... sensor, driver and motor.init() as before
  motor.linkSensor(&sensor_calibrated);
  motor.zero_electric_angle = zero_electric_angle;
  motor.sensor_direction = sensor_direction;
  motor.initFOC(); // no alignment movement needed
}
```

<blockquote class="warning" markdown="1"><p class="heading">Recalibrate after mechanical changes</p>
The LUT is specific to the exact magnet position. If you remove the board, re-glue the magnet or change the motor, run the calibration again.
</blockquote>
