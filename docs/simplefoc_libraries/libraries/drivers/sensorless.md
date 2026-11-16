---
layout: default
title: Sensorless flux observer
description: "Sensorless FOC with SimpleFOC: the MXLEMMING flux observer from the SimpleFOCDrivers library estimates the rotor position from the phase currents."
nav_order: 3
permalink: /drivers_sensorless
parent: <span class="simple">Simple<span class="foc">FOC</span>Drivers</span>
grand_parent: libraries
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
toc: true
---

# Sensorless control with the flux observer

FOC needs to know the rotor position. Usually it comes from a position sensor, but at higher speeds it can also be estimated from what the motor does electrically: a spinning motor generates a back-EMF whose angle follows the rotor. The `MXLEMMINGObserverSensor` from the <span class="simple">Simple<span class="foc">FOC</span>Drivers</span> library does exactly that. It estimates the rotor angle from the measured phase currents, the applied voltages and the motor parameters, so you can run FOC **without any position sensor**.

The observer is ported from the [MESC firmware](https://github.com/davidmolony/MESC_Firmware) by David Molony, and is also the default flux observer in the VESC firmware. The [MESC documentation](https://davidmolony.github.io/MESC_Firmware/operation/CONTROL.html#the-sensorless-observer) explains the maths behind it.

[Source and README](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/encoders/MXLEMMING_observer){: .btn .btn-docs} [Full example project](sensorless_foc_nucleo_example){: .btn .btn-docs}

## When to use it

Sensorless control is a good fit when:

- the motor runs mostly at **medium to high speed**: fans, pumps, propellers, wheels, spindles
- there is no room for a sensor, or the sensor would be too expensive or unreliable (heat, dirt, vibration)

It is **not** a good fit when:

- you need **position control**, holding torque at standstill, or smooth motion at very low speeds
- the motor has to start under a heavy load

The reason is physical: at standstill and very low speed the back-EMF is close to zero, so there is almost nothing to estimate the angle from. Above a certain speed, which depends on the motor and on the quality of the current sensing, the estimate becomes reliable.

<blockquote class="info" markdown="1"><p class="heading">Observer as a sensor</p>
In <span class="simple">Simple<span class="foc">FOC</span></span>, the observer is implemented as a `Sensor`. You link it to the motor with `motor.linkSensor()` like any other sensor, and the rest of your code stays the same.
</blockquote>

## Requirements

- **Current sensing**: the observer only works with phase current measurements. Any [current sense](current_sense) type works: inline, low-side or high-side.
- **FOC current control** (`TorqueControlType::foc_current`) is recommended.
- **Motor parameters**, all four are required:

| Parameter | Unit | How to get it |
| --- | --- | --- |
| Pole pairs | - | Datasheet, or count the magnets and divide by 2 |
| Phase resistance | Ω | Datasheet, a [multimeter measurement](phase_resistance), or [`characteriseMotor()`](motor_characterisation) |
| Phase inductance | H | Datasheet or [`characteriseMotor()`](motor_characterisation) |
| KV rating | rpm/V | Datasheet, or [measure it](motor_params_test) |

The observer won't produce an angle if any of these is missing. The accuracy of the estimate depends directly on how accurate they are, so it is worth measuring them rather than guessing.

## Usage

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>
#include "encoders/MXLEMMING_observer/MXLEMMINGObserverSensor.h"

// motor with all its parameters: pole pairs, phase resistance [Ω], KV [rpm/V], phase inductance [H]
BLDCMotor motor = BLDCMotor(11, 5.94, 100, 0.0005);
BLDCDriver3PWM driver = BLDCDriver3PWM(PWM_A, PWM_B, PWM_C, EN);
LowsideCurrentSense current_sense = LowsideCurrentSense(185.0f, CS_A, CS_B);

// the observer takes the motor, to read its parameters, voltages and currents
MXLEMMINGObserverSensor observer = MXLEMMINGObserverSensor(motor);

void setup() {
  Serial.begin(115200);
  SimpleFOCDebug::enable(&Serial);

  motor.linkSensor(&observer);

  driver.voltage_power_supply = 20;
  driver.init();
  motor.linkDriver(&driver);

  current_sense.linkDriver(&driver);
  current_sense.init();
  motor.linkCurrentSense(&current_sense);

  motor.torque_controller = TorqueControlType::foc_current;
  motor.controller = MotionControlType::torque;
  motor.init();

  // the observer gives the electrical angle directly, so no sensor alignment is needed
  motor.sensor_direction = Direction::CW;
  motor.zero_electric_angle = 0;
  motor.initFOC();
}

void loop() {
  motor.loopFOC();
  motor.move();
}
```

Setting `sensor_direction` and `zero_electric_angle` before `initFOC()` skips the usual alignment, which wouldn't work anyway because the observer can't measure the angle at standstill.

<blockquote class="warning" markdown="1"><p class="heading">Set the motor parameters in the constructor</p>
The observer calculates the motor flux linkage from the KV rating and the pole pairs **once, when it is created**. If you set `motor.KV_rating` later in `setup()`, the flux linkage stays at zero and the observer silently does nothing. Either give all the parameters in the `BLDCMotor` constructor, as above, or set the flux linkage yourself after changing the KV rating:

```cpp
motor.KV_rating = 100;
observer.flux_linkage = 60 / (_SQRT3 * _PI * motor.KV_rating * motor.pole_pairs * 2);
```
</blockquote>

### Using characteriseMotor()

You can measure the resistance and inductance with [`characteriseMotor()`](motor_characterisation), but note that the observer reads the inductance from `motor.phase_inductance`, while the characterisation stores it in `motor.axis_inductance`. Copy it over after the measurement:

```cpp
motor.init();
motor.sensor_direction = Direction::CW;
motor.zero_electric_angle = 0;
motor.initFOC();

motor.characteriseMotor(2.0f);                        // measures R and L
motor.phase_inductance = motor.axis_inductance.q;     // the observer uses phase_inductance
```

The KV rating still has to be known in advance, it can't be measured this way.

## Auto-tuning the current controller

Sensorless control depends on good current control even more than sensored control does. The observer estimates the angle from the measured currents and the applied voltages, so a noisy or oscillating current loop directly disturbs the angle estimate. And without a sensor, nothing corrects a bad angle estimate.

The good news is that the observer already needs the phase resistance and inductance, which is exactly what is needed to tune the current PI controllers automatically. `tuneCurrentController()` calculates the gains for a chosen bandwidth:

```cpp
motor.init();
motor.sensor_direction = Direction::CW;
motor.zero_electric_angle = 0;
motor.initFOC();

motor.tuneCurrentController(500.0f); // current loop bandwidth in Hz
```

It sets the P gain from the inductance and the I gain from the resistance, for both the q and d axes, and the current filters to 5 times the bandwidth. The resulting gains are printed to the serial monitor when debugging is enabled. If the resistance or inductance are not set, it runs `characteriseMotor()` first to measure them.

### Choosing the bandwidth

The bandwidth must be well below the frequency of the FOC loop, i.e. how often `loopFOC()` is called. A good starting point is **5-10% of the loop frequency**, for example 500Hz for a 10kHz loop.

| Bandwidth | Effect |
| --- | --- |
| Too low | Slow current response, the torque lags behind changes and the motor can lose sync with the observer at high acceleration |
| Too high | The current loop amplifies measurement noise, which makes the angle estimate noisy, and can oscillate |

`tuneCurrentController()` only checks the bandwidth against the measured loop frequency once `loopFOC()` has run. When called in `setup()`, the loop frequency isn't known yet, so choose the bandwidth carefully. If you're unsure of your loop frequency, run the motor and call `tuneCurrentController()` again later, for example from a [Commander](commander_interface) command: it then refuses bandwidths higher than half the loop frequency.

### Characterise, tune and save

Putting it together, the full parameter identification for a sensorless setup is:

```cpp
motor.initFOC();
motor.characteriseMotor(2.0f);                    // measure R and L
motor.phase_inductance = motor.axis_inductance.q; // for the observer, see above
motor.tuneCurrentController(500.0f);              // tune the current PIs
```

Measuring the motor on every startup is not necessary. Note the printed values once and put them in the `BLDCMotor` constructor, or save them with [settings storage](drivers_settings).

[Read more about tuning the current loop](tuning_current_loop){: .btn .btn-docs}

## Starting the motor

Since the observer can't see the rotor at standstill, the motor starts "blind". In torque mode, a moderate target current is usually enough to get the motor spinning, after which the observer locks on and the control becomes smooth:

```
M0.5    apply 0.5A, the motor accelerates and the observer locks on
```

Some things that help with a reliable start:

- Start with **no load** or a light load.
- Give a **high enough starting current**, so the motor accelerates quickly through the low-speed range where the estimate is poor.
- Use **torque mode** first. Switch to velocity mode once the motor runs well, and keep the velocity target above the speed where the observer is reliable.

## Tuning and troubleshooting

| Symptom | Likely cause |
| --- | --- |
| Motor doesn't move, the angle stays at 0 | A motor parameter is missing, current sensing not linked, or the flux linkage is 0 (see the warning above) |
| Motor vibrates or stalls at start | Starting current too low, or too much load at start |
| Motor runs but draws a lot of current / runs hot | Wrong motor parameters, mostly resistance or KV. The estimated angle is off, so part of the current produces no torque |
| Motor runs rough at low speed but fine at high speed | Expected: the back-EMF is too small at low speed. Increase the minimum operating speed |
| Audible noise, current oscillates | Current controller not tuned, see [auto-tuning the current controller](#auto-tuning-the-current-controller) |
| Unstable at high speed | Current sensing too noisy or too slow, or the FOC loop too slow. Run `loopFOC()` as fast as possible |

The observer has a few parameters you can adjust:

| Parameter | Description |
| --- | --- |
| `flux_linkage` | Motor flux linkage, calculated from KV and pole pairs. Fine-tune it if the KV rating is not accurate |
| `sensor_downsample` | Only update the observer every n-th call. Leave at 0 (every call) unless the MCU is too slow |

## Example project

For a complete project with hardware setup, see the [sensorless FOC example](sensorless_foc_nucleo_example) using the SimpleFOCShield and an STM32 Nucleo board.
