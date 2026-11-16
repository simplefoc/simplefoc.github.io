---
layout: default
title: Toolchain and flashing
parent: Microspora Code
grand_parent: <span class="simple">Simple<span class="foc">FOC</span>-microspora</span>
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "Setting up PlatformIO or the Arduino IDE and flashing the Microspora board over USB DFU."
nav_order: 1
permalink: /microspora_setup
has_children: false
has_toc: false
toc: true
---

# Toolchain and flashing

<span class="simple">Simple<span class="foc">FOC</span>-microspora</span> is built around the **STM32G431CBU6** and is programmed over USB-C using the STM32 built-in DFU bootloader, so no external programmer is needed. You can use either [PlatformIO](https://platformio.org/) (recommended) or the Arduino IDE.

## Required libraries

| Library | Used for |
| --- | --- |
| [<span class="simple">Simple<span class="foc">FOC</span>library</span>](installation) | Motor control |
| [SimpleFOCDrivers](drivers_library) | DRV8316 driver, MT6701 sensor, CAN commander |
| [SimpleCANio](https://github.com/simplefoc/SimpleCANio) | CAN bus (only if you use CAN) |

## PlatformIO (recommended)

The easiest way to start is to clone the [Microspora firmware repository](https://github.com/simplefoc/microspora_simplefoc_firmware) and open it in VS Code with the PlatformIO extension. If you are starting a new project, use this `platformio.ini`:

```ini
[env:SIMPLEFOC_MICROSPORA]
platform = ststm32
board = genericSTM32G431CB
framework = arduino
lib_archive = false
monitor_speed = 250000
upload_protocol = dfu
monitor_dtr = 1
lib_deps =
    Simple FOC
    https://github.com/simplefoc/Arduino-FOC-drivers.git#dev
    SimpleCANio

build_flags =
    -D PIO_FRAMEWORK_ARDUINO_ENABLE_CDC  ; USB serial
    -D USBCON
    -D HAL_FDCAN_MODULE_ENABLED          ; CAN (required, lib_archive = false builds the CAN code too)
    -D LED_BUILTIN=PC6
```

The reference firmware also defines all the board pins as build flags (`PHA_H`, `ENC_CS`, `VSENS`, ...), see the full list in the [pinout reference](microspora_pinout). The code examples in these docs use the pin names directly, so they work with or without those flags.

## Arduino IDE

1. Install the [STM32duino core](https://github.com/stm32duino/Arduino_Core_STM32) via the Boards Manager.
2. Install the libraries listed above via the Library Manager.
3. In the **Tools** menu select:
   - **Board**: Generic STM32G4 series
   - **Board part number**: Generic G431CBUx
   - **USB support**: CDC (generic 'Serial' supersede U(S)ART)
   - **Upload method**: STM32CubeProgrammer (DFU)

<blockquote class="warning" markdown="1"><p class="heading">STM32CubeProgrammer is required</p>
The Arduino IDE uploads using ST's [STM32CubeProgrammer](https://www.st.com/en/development-tools/stm32cubeprog.html), which is not installed with the STM32duino core. Install it and add its `bin` folder to your `PATH`, otherwise the upload fails with `STM32CubeProgrammer not found`.

Alternatively, use **Sketch > Export Compiled Binary** and flash the `.bin` file with [dfu-util](https://dfu-util.sourceforge.net/) while the board is in DFU mode:

```bash
dfu-util -a 0 -s 0x08000000:leave -D your_sketch.bin
```
</blockquote>

<blockquote class="info" markdown="1"><p class="heading">CAN in the Arduino IDE</p>
The FDCAN HAL module is not enabled by default in the Arduino IDE. To use CAN, add a `hal_conf_extra.h` file next to your sketch containing `#define HAL_FDCAN_MODULE_ENABLED`.
</blockquote>

## Flashing in DFU mode

<img src="./extras/Images/dfu_btns.png" class="width60" alt="Microspora DFU buttons"/>

1. Connect the board to the computer using USB-C.
2. Enter DFU mode: hold **BOOT**, press and release **RESET**, wait one or two seconds, then release **BOOT**.
3. Upload the firmware from PlatformIO or the Arduino IDE.
4. Press **RESET** to start the new firmware.

<blockquote class="warning" markdown="1"><p class="heading">Linux users</p>
If the board is in DFU mode but the upload fails with `Target device not found` or a permission error, your user can't access the USB device. Check that the board is visible with `lsusb` (it shows up as `0483:df11 STMicroelectronics STM Device in DFU Mode`), then add a udev rule:

```bash
echo 'SUBSYSTEM=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="df11", MODE="0666", GROUP="plugdev", TAG+="uaccess"' | sudo tee /etc/udev/rules.d/50-stm32-dfu.rules
sudo udevadm control --reload-rules && sudo udevadm trigger
```

Unplug the board, plug it back in, put it in DFU mode again and retry the upload.
</blockquote>

## Serial monitor

The reference firmware uses **250000 baud**. Since the board uses native USB, the serial port disappears while the board is resetting. Add a short delay at the start of `setup()` so you don't miss the first messages:

```cpp
Serial.begin(250000);
_delay(3000); // give the USB serial time to connect
```

[Next: write your first code](microspora_code){: .btn .btn-docs}
