---
layout: default
title: Build Flags
nav_order: 2
permalink: /reference/build_flags
parent: Reference
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
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
`SIMPLEFOC_STM32_DEBUG` | STM32 | set to enable extra debug output for STM32 MCUs.
`SIMPLEFOC_STM32_MAX_PINTIMERSUSED` | STM32 | maximum number of PWM pins configurable, default is 12 (up to 2x 6PWM, normally that's plenty)
`SIMPLEFOC_SAMD_DEBUG` | SAMD21 / SAMD51 | set to enable extra debug output for SAMD MCUs.
`SIMPLEFOC_SAMD_MAX_TCC_PINCONFIGURATIONS` | SAMD21 / SAMD51 | maximum number of PWM pins configurable, default is 24 (for up to 4x6PWM, that should be enough ;-) )
`SIMPLEFOC_SAMD51_DPLL_FREQ` | SAMD21 / SAMD51 | expected frequency on DPLL, since we don't configure it ourselves. Typically this is the CPU frequency. For custom boards or overclockers you can override it using this define. Default is 120000000
`SIMPLEFOC_DEBUG_RP2040` | RP2040 | set to enable extra debug output on Raspberry Pico.
`SIMPLEFOC_ESP32_USELEDC` | ESP32 | force use of the LEDC PWM driver even on ESP32s that support MCPWM. Mainly useful for testing purposes, normally you would prefer MCPWM if it is available.
