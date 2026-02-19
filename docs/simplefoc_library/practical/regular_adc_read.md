---
layout: default
title: Regular ADC reads during FOC
nav_order: 6
description: "Reading extra analog signals while low-side current sensing is running."
permalink: /regular_adc_read
parent: Practical guides
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# Regular ADC reads during FOC

Sometimes you want to read extra analog signals (bus voltage, temperature, potentiometers, etc.) while the motor is running with **low‑side current sensing**. On some MCUs, `analogRead()` can interfere with the ADC configuration used for current sensing, or it can be too slow and add jitter. The library provides MCU‑specific helpers that are safe to use during FOC.

## STM32: `_readRegularADCVoltage()`

On STM32, low‑side current sensing uses **injected ADC conversions**. Injected conversions have hardware priority and will pre‑empt regular conversions. The library exposes a helper that performs a **one‑shot regular conversion** while injected conversions are running:

- Implemented as `_readRegularADCVoltage(const int pin)` in the STM32 backend.
- If low‑side current sensing is already running, the ADC is already configured and this function reuses it.
- If the pin belongs to another ADC, it initializes it for regular reads.
- The function retries if the ADC is busy and returns **voltage in volts**.

**Why `analogRead()` is a bad idea on STM32 during FOC**

`analogRead()` can reconfigure the ADC each call and does not account for injected conversions. That can **break the current‑sensing configuration**, add latency, and introduce jitter in the control loop. The STM32 helper is still slower than injected reads (typically >10µs) but **much faster and safer than `analogRead()`**.

**Example (STM32, low‑side current sensing already running)**

```cpp
#include <SimpleFOC.h>
#include "current_sense/hardware_specific/stm32/stm32_mcu.h"

// ... motor + driver setup omitted for brevity
LowSideCurrentSense current_sense = LowSideCurrentSense(0.01f, 50.0f, A0, A1, A2);

void setup() {
    // driver + motor init ...
    current_sense.init();
    current_sense.linkDriver(&driver);
}

void loop() {
    motor.loopFOC();
    motor.move(target);

    // Read an auxiliary ADC pin while low-side current sensing is active
    float vbus = _readRegularADCVoltage(A3);
    // use vbus...
    }
```

**Notes**
- Use a pin that belongs to the same ADC as your current‑sense pins whenever possible.
- If the pin belongs to another ADC, the helper will initialize it for regular reads.
- The first time you call the helper for a new ADC it will be slower due to initialization, but subsequent calls will be faster.
- Call this helper only when necessary (e.g., once per control loop) to avoid excessive overhead.

## ESP32: `adcRead()`

On ESP32, the library provides `adcRead(pin)` for fast ADC access using direct register reads. It is placed in IRAM and is designed to be safe inside fast control loops. This is the preferred way to read extra analog signals while current sensing is active.

**Why `analogRead()` is a bad idea on ESP32 during FOC**

`analogRead()` is slower and can add overhead or modify ADC configuration (attenuation, resolution). The library’s `adcRead()` keeps the conversion path fast and deterministic.

**Example (ESP32)**

```cpp
#include <SimpleFOC.h>
#include "current_sense/hardware_specific/esp32/esp32_adc_driver.h"

const int vbusPin = 34; // ADC pin

void setup() {
	adcInit(vbusPin); // configure ADC (attenuation + resolution)
}

void loop() {
	// ... FOC loop
	uint16_t raw = adcRead(vbusPin); // 0–4095
	float vbus = raw * (3.3f / 4096.0f);
	// use vbus...
}
```

**Notes**
- Call `adcInit(pin)` once (e.g., in `setup()`), then use `adcRead(pin)` in the loop.
- Call this function as little as possible (e.g., every 10–100ms) to avoid unnecessary overhead in the control loop.
