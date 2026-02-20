---
layout: default
title: Microcontrollers
nav_order: 4
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /microcontrollers
parent: Supported Hardware
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
has_children: true
has_toc: false
toc: true
---


# Supported microcontrollers

Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> supports over 20 different microcontroller architectures, giving you flexibility to choose the best MCU for your specific project needs.

## Supported Families

The main families supported *off-the-shelf* are:
- [Arduino](arduino_mcu) (8-bit AVR, UNO R4, DUE, Nano 33)
- [STM32](stm32_mcu) (F1, F4, G4, L4, F7, H7 families)
- [ESP32 and ESP8266](esp_mcu) (ESP32, ESP32-S2, ESP32-S3, ESP32-C3, ESP32-C6)
- [Teensy](teensy_mcu) (3.x and 4.x)
- [SAMD21/SAMD51](samd_mcu) (Arduino Zero, MKR series)
- [Raspberry Pi Pico](rpi_mcu) (RP2040/RP2350)
- [Silicon Labs](https://github.com/SiliconLabs) (Arduino Nano Matter) - *new in v2.4.0*
- [Portenta H7](portenta_mcu) - *initial support*
- [nRF52](nrf52_mcu) - *initial support*

And we continue extending support to more architectures! 😃

---

# Choosing the Right Microcontroller

There are three main parameters when choosing the microcontroller for your project:

1. **Processing power** (clock speed, architecture, etc.) - this will determine the performance of the FOC algorithm and the maximum speed of your motor.
2. **PWM generation capabilities** - this will determine the number of motors you can control and the control modes you can use.
3. **ADC sensing capabilities** - this will determine the current sensing options you have for your project.

## 1. Processing power

When it comes to the processing power, the rule of thumb is:
- The higher the clock speed the better.
- Aim for the FOC execution time of `loopFOC()` to be above 1kHz (ideally above 5kHz) depending on the motor and sensor combination you are using.

This table gives you a rough comparison of the performance of different MCUs with this library. It gives an estimated loop frequency (`loopFOC() + move()`) for one motor and sensor combination (BLDC + sensor) that you can expect to achieve with the library on each MCU. The values are approximate and can vary depending on the specific board, motor, sensor, and other factors. 

MCU Family | Clock | Bit | Performance | Expected loop frequency | Example boards | Recommended For
--- | --- | --- | --- | --- | --- | ---
**STM32 H7** | 480MHz | 32 | 🟢 Prime | >10kHz | [Nucleo-144](https://www.st.com/en/evaluation-tools/stm32-nucleo-boards/products.html?querycriteria=productId=LN1847$$1574=Nucleo-144&aggOrder=0%7C1%7C2%7C3%7C4%7C5%7C6%7C7%7C8%7C9%7C10%7C11%7C12%7C13%7C14%7C15%7C16%7C17%7C18%7C19%7C20%7C21) | High-performance, multi-motor systems, High-end solution
**STM32 F4/G4/F7** | 150-220 | 32 | 🟢 Excellent | >5kHz | [Nucleo-64](https://www.st.com/en/evaluation-tools/stm32-nucleo-boards/products.html?querycriteria=productId=LN1847$$1574=Nucleo-64&aggOrder=0%7C1%7C2%7C3%7C4%7C5%7C6%7C7%7C8%7C9%7C10%7C11%7C12%7C13%7C14%7C15%7C16%7C17%7C18%7C19%7C20%7C21), [Nucleo-144](https://www.st.com/en/evaluation-tools/stm32-nucleo-boards/products.html?querycriteria=productId=LN1847$$1574=Nucleo-144&aggOrder=0%7C1%7C2%7C3%7C4%7C5%7C6%7C7%7C8%7C9%7C10%7C11%7C12%7C13%7C14%7C15%7C16%7C17%7C18%7C19%7C20%7C21), [B-G431B-ESC1](https://www.st.com/en/evaluation-tools/b-g431b-esc1.html) | Best overall choice for FOC control
**STM32 F1** | 72Hz | 32 | 🟢 Good | 1-5kHz | [Bluepill](https://de.aliexpress.com/w/wholesale-bluepill.html?spm=a2g0o.home.search.0), [Blackpill](https://de.aliexpress.com/w/wholesale-blackpill.html?spm=a2g0o.productlist.search.0) | Budget-friendly, good performance (⚠️ sometimes limited in memory)
**STM32 L4** | 80MHz | 32 | 🟢 Good | 1-5kHz | Nucleo-64 | Low-power applications, good performance
**Teensy 4.x** | 600MHz | 32 | 🟢 Prime | >10kHz | [Teensy 4.0, 4.1](https://www.pjrc.com/store/teensy40.html) | High-performance, High-end solution, comparable to STM32H7<br> (limited low-side ADC support)
**Teensy 3.x** | 72-180MHz | 32 | 🟢 Good | 1-5kHz | [Teensy 3.2, 3.5, 3.6](https://www.pjrc.com/store/teensy32.html) | Robotics, reliable real-time control
**ESP32, ESP32-S2/S3** | 240MHz | 32 | 🟢 Excellent | >5kHz | [ESP32 DevKit](https://www.espressif.com/en/products/devkits), [ESP3S2 QtPy](https://www.adafruit.com/product/5426)| Great performance, IoT integration, Dual-Core
**ESP32-C2/C3/C6** | 120-160MHz | 32 | 🟢 Good | 1-5kHz | [ESP3C6 Xiao](https://wiki.seeedstudio.com/XIAO_ESP32C3_Getting_Started/), [ESP3C6 Xiao](https://wiki.seeedstudio.com/xiao_esp32c6_getting_started/)| Good performance<br>Much worse than ESP32 or ESP32-S2/3 though
**RP2040 (Pico)** | 125MHz | 32 | 🟢 Good | 5-10kHz | [Raspberry Pi Pico](https://www.raspberrypi.com/documentation/microcontrollers/pico-series.html) | Dual-core projects, cost-effective
**Arduino DUE** | 84MHz | 32 | 🟡 Fair | 1-5kHz | [Arduino DUE](https://docs.arduino.cc/hardware/due/) | 32-bit Arduino, good option if you already have one on hand
**SAMD51** | 120MHz | 32 | 🟢 Good | 5-10kHz | [Adafruit Feather M4](https://www.adafruit.com/product/3857), [SparkFun Thing Plus](https://www.sparkfun.com/thing-plus), [Metro M4](https://learn.adafruit.com/adafruit-metro-m4-express-featuring-atsamd51/overview) | Good performance, not common 
**SAMD21** | 48MHz | 32 | 🟡 Fair | 1-5kHz | [Nano 33 IoT](https://docs.arduino.cc/hardware/nano-33-iot/), [QtPy SAMD21](https://learn.adafruit.com/adafruit-qt-py/overview), [XIAO SAMD21](https://wiki.seeedstudio.com/Seeeduino-XIAO/)  | Budget-friendly, OK performance (⚠️ sometimes limited in memory)
**Renesas** | 48MHz | 32 | 🟡 Fair | 1-5kHz | [Arduino UNO R4](https://store.arduino.cc/pages/uno-r4) | Modern Arduino, not the best performance but a significant upgrade over the AVR-based UNO
**Silabs MGM240S** | 78MHz | 32 | 🟢 Good | 5-10kHz | [Arduino Nano Matter](https://docs.arduino.cc/hardware/nano-matter/) | Matter/IoT integration (v2.4.0+)
**AVR (8-bit)** | 16MHz | 8 | 🔴 Limited | <1kHz | [Arduino UNO](https://docs.arduino.cc/hardware/uno-rev3/), [Arduino MEGA](https://docs.arduino.cc/hardware/mega-2560/), [Arduino Nano](https://docs.arduino.cc/hardware/nano/), [Arduino Leonardo](https://docs.arduino.cc/hardware/leonardo/) | Learning FOC, simple single-motor projects
**ESP8266** | 80MHz | 32 | 🔴 Limited | <1kHz | [ESP8266 NodeMCU](https://www.nodemcu.com/index_en.html#fr_54747661d775ef1a3600009e), [Wemos D1 Mini](https://www.wemos.cc/en/latest/d1/d1_mini.html) | Good for IoT projects, very limited real-time performance

<blockquote class="warning">
<p class="heading">⚠️ Arduino UNO/MEGA Encoder Limitation</p>
For Arduino UNO/MEGA with encoders, keep total system pulses under <b>20,000 pulses/second</b> to avoid execution issues.
<p class="heading">Example Calculation</p>
With a 10,000 CPR encoder: Maximum motor speed = 120 RPM (2 rotations/second)
<br><br>
<b>Recommendation:</b> Use magnetic sensors (SPI/I2C) instead of encoders with 8-bit Arduino boards.
</blockquote>

**Performance Guidelines:**
- ✅ Optimal: Loop time < 0.5ms (>2kHz)
- ⚠️ Acceptable: Loop time 1-4ms (250-1000Hz)
- ❌ Poor: Loop time > 4ms (<250Hz)


**Multi-Motor Systems:**
If you need to control multiple motors, remember to account for the total system load. Divide the expected loop frequency by the number of motors to ensure you stay within performance limits.
The same performance guidelines apply.

---

## 2. PWM Generation Capabilities

Most supported MCUs can generate all PWM modes (2, 3, 4, and 6 PWM) with configurable frequencies. 
2 and 4 PWM modes are used Stepper drivers (Stepper motors), while 3 and 6 PWM modes are used for BLDC drivers (BLDC motors and Steppers using hybrid control).

MCU | 2 PWM | 4 PWM | 3 PWM | 6 PWM | Frequency Config
--- | --- | --- | --- | --- | ---
Arduino (8-bit) | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ (4kHz or 32kHz)
STM32 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
ESP32 (MCPWM/LEDC) | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
Teensy | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
SAMD21/51 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
Raspberry Pi Pico | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
Renesas (UNO R4) | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
Arduino Nano Matter | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
nRF52 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️
Arduino DUE | ✔️ | ✔️ | ✔️ | ❌ | ✔️
ESP8266 | ✔️ | ✔️ | ✔️ | ❌ | ✔️
Portenta H7 | ✔️ | ✔️ | ✔️ | ❌ | ✔️

**Note:** If you need 6 PWM mode (for specific drivers), avoid MCUs marked with ❌.

[See the guide to choosing PWM-capable pins](choosing_pwm_pins){: .btn .btn-docs}

---

## 3. Current Sensing (ADC) Capabilities

Most of the supported MCUs can perform inline current sensing using the ADC, which is the a very common method for FOC control. However, low-side current sensing is equaly as common for high-performance applications and is supported by a smaller subset of high-end MCUs. Make sure to check the table below if you need low-side sensing for your project.

MCU | Inline | Low-side | High-side | Notes
--- | --- | --- | --- | ---
**STM32 F1/F4/G4/L4/F7/H7** | ✔️ | ✔️ | ❌ | Best current sensing support
**ESP32/ESP32-S3** | ✔️ | ✔️ | ❌ | Good ADC, dual-core advantage
**Teensy 4** | ✔️ | ✔️ (one motor) | ❌ | High-speed ADC
**SAMD21** | ✔️ | ✔️ (one motor) | ❌ | Limited testing
**Arduino Nano Matter** | ✔️ | ✔️ (one motor) | ✔️ (untested) | Newest support (v2.4.0)
Arduino AVR (8-bit) | ✔️ | ❌ | ❌ | Inline only
Arduino DUE | ✔️ | ❌ | ❌ | Inline only
SAMD51 | ✔️ | ❌ | ❌ | Inline only
Teensy 3 | ✔️ | ❌ | ❌ | Inline only
Raspberry Pi Pico | ✔️ | ❌ | ❌ | Inline only
ESP32-S2/C3 | ✔️ | ❌ | ❌ | Inline only
Portenta H7 | ✔️ | ❌ | ❌ | Inline only
nRF52 | ✔️ | ❌ | ❌ | Inline only
Renesas (UNO R4) | ❌ | ❌ | ❌ | No current sensing yet
ESP8266 | ❌ | ❌ | ❌ | No ADC support for sensing
STM32 B-G431B-ESC1 | ❌ | ✔️ (one motor) | ❌ | Dedicated ESC board

**Most boards support inline current sensing.** Low-side sensing is available primarily on STM32, ESP32, Teensy 4, and SAMD21.

[See the guide to choosing ADC-capable pins](choosing_adc_pins){: .btn .btn-docs}

---

## Quick Selection Guide

### General Recommendations

1. **If possible, use STM32** - Best overall choice for FOC control
2. **ESP32 or Teensy** - Excellent alternatives, each one with their own strengths and limitations 
3. **Other 32-bit boards** - Fine if current sensing isn't required or not crucial for your project
4. **8-bit Arduino** - Acceptable for learning and simple single-motor projects 
5. **Custom optimization** - If you have specific requirements, start with what you have and optimize the library for your use case


### Specific Use Cases

**Best Overall Performance:**
- **STM32 H7** - Top-tier performance for demanding applications and multi-motor systems
- **STM32 F4/G4/F7** - Excellent performance and features for most FOC projects

**Alternative High-Performance Options with some limitations**
- **Teensy 4.x** - Amazing performance (comparable to STM32 H7), can handle fast multi-motor systems 
    - ⚠️ limited low-side sensing support (one motor only)
- **ESP32** - Excellent for IoT/wireless projects, great performance 
    - If possible, choose ESP32 or ESP32-S3 for better ADC performance and low-side sensing support
    - If using ESP32C2/C3/C6, be aware of the significantly lower CPU performance and lack of low-side sensing support
    - ⚠️ Aim for one motor with current sensing for optimal performance, 
        - multi-motor systems with current sensing may require optimization and careful load management
        - multiple motors without current sensing should be fine

**Budget-Friendly with Good Performance:**
- **Need current sensing?** 
    - **STM32 F1** - Best price/performance ratio (especially Bluepill/Blackpill boards)
        - ⚠️ check memory limits (use 128KB flash versions)
    - **SAMD21** - Affordable with good enough performance for simple projects
        - ⚠️ check memory limits (should have more than 64kB flash)
- **Don't need current sensing?** 
    - **Arduino DUE** - 32-bit Arduino with good performance if you already have one on hand
    - **Raspberry Pi Pico (RP2040)** - Dual-core capability at low cost, good for projects without current sensing
    - **nRF52** - Good for low-power wireless projects without current sensing
    - **SAMD51** - Good performance but less common, check availability

**Other Options:**
- **Arduino UNO/MEGA** - Acceptable for learning and simple single-motor projects
    - ⚠️ limited performance with encoders, use magnetic sensors instead
    - ⚠️ poor current sensing capabilities (inline only, no low-side sensing)
- **ESP8266** - Good for IoT projects, very limited real-time performance, not recommended for FOC control
    - ⚠️ no ADC support for sensing, not suitable for FOC control



---

<h2><i class="fa fa-lg"><svg id="fab-discourse" style="width:20px;fill:#44a8fa" viewBox="0 0 448 512"><path d="M225.9 32C103.3 32 0 130.5 0 252.1 0 256 .1 480 .1 480l225.8-.2c122.7 0 222.1-102.3 222.1-223.9C448 134.3 348.6 32 225.9 32zM224 384c-19.4 0-37.9-4.3-54.4-12.1L88.5 392l22.9-75c-9.8-18.1-15.4-38.9-15.4-61 0-70.7 57.3-128 128-128s128 57.3 128 128-57.3 128-128 128z"></path> </svg></i> <span class="simple">Simple<span class="foc">FOC</span> Community</span></h2>

If you have ported the library to another device or need help porting to a specific platform, please visit our [community forum](https://community.simplefoc.com)!

You'll find answered questions, implementation stories, and helpful community members ready to assist.

<div class="image_icon width80" >
    <a href="https://community.simplefoc.com" target="_blank">
        <img src="extras/Images/community.png" >
        <i class="fa fa-external-link-square fa-2x"></i>
    </a>
</div>
