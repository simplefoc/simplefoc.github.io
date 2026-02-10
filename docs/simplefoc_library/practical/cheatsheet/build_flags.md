---
layout: default
title: Build Flags
nav_order: 2
permalink: /cheatsheet/build_flags
parent: Options Cheat Sheet
grand_parent: Practical guides
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: False
has_toc: False
---


# Build flags

<span class="simple">Simple<span class="foc">FOC</span>library</span> supports a few compiler options which might be interesting to advanced users. They are described below.

## Available build flags

Build flags control the way the compiler generates the code for <span class="simple">Simple<span class="foc">FOC</span>library</span>. Some are supported on all architectures, while some are MCU-architecture dependent.

Flag | Architecture | Description
--- | --- | ---
`SIMPLEFOC_DISABLE_DEBUG` | All | set this to disable the entire debugging code
`SIMPLEFOC_PWM_ACTIVE_HIGH` | STM32, RP2040, ESP32 | sets PWM polarity on phase PWM outputs - default is active high (true) but can be set to false for active low polarity. Affects both high and low side in 6-PWM mode (STM32, RP2040)
`SIMPLEFOC_PWM_LOWSIDE_ACTIVE_HIGH` | STM32, RP2040, ESP32 | sets PWM polarity on low side phase PWM outputs - default is active high (true) but can be set to false for active low polarity. Affects only low side FETs in 6-PWM mode. No effect in other modes.
`SIMPLEFOC_PWM_HIGHSIDE_ACTIVE_HIGH` | STM32, RP2040, ESP32 | sets PWM polarity on high side phase PWM outputs - default is active high (true) but can be set to false for active low polarity. Affects only high side FETs in 6-PWM mode. No effect in other modes.
`SIMPLEFOC_STM32_DEBUG` | STM32 | set to enable extra debug output for STM32 MCUs.
`SIMPLEFOC_STM32_MAX_PINTIMERSUSED` | STM32 | maximum number of PWM pins configurable, default is 12 (up to 2x 6PWM, normally that's plenty)
`SIMPLEFOC_SAMD_DEBUG` | SAMD21 / SAMD51 | set to enable extra debug output for SAMD MCUs.
`SIMPLEFOC_SAMD_MAX_TCC_PINCONFIGURATIONS` | SAMD21 / SAMD51 | maximum number of PWM pins configurable, default is 24 (for up to 4x6PWM, that should be enough ;-) )
`SIMPLEFOC_SAMD51_DPLL_FREQ` | SAMD21 / SAMD51 | expected frequency on DPLL, since we don't configure it ourselves. Typically this is the CPU frequency. For custom boards or overclockers you can override it using this define. Default is 120000000
`SIMPLEFOC_DEBUG_RP2040` | RP2040 | set to enable extra debug output on Raspberry Pico.
`SIMPLEFOC_ESP32_USELEDC` | ESP32 | force use of the LEDC PWM driver even on ESP32s that support MCPWM. Mainly useful for testing purposes, normally you would prefer MCPWM if it is available.
`SIMPLEFOC_ESP32_HW_DEADTIME` | ESP32 | Select between MCPWM hardware deadtime and a software implementation allowing phase_state configuration. Defaults to hardware for being tested more.
`SIMPLEFOC_TEENSY_DEBUG` | Teensy 3.x / 4.x | Set to enable extra debug output for Teensy 3.x / 4.x MCUs.   
`SIMPLEFOC_TEENSY4_ADC_INTERRUPT_DEBUG` | Teensy 4 | Enables a simple debugging for low-side current sense interrupts which sets the pin `30` to high each time the ADC1 fires an interrupt.
`SIMPLEFOC_TEENSY4_FORCE_CENTER_ALIGNED_3PWM` | Teensy 4 | Force center aligned 3PWM mode on Teensy 4. Normally 3PWM mode is not center aligned and the timers are not synchronized. 