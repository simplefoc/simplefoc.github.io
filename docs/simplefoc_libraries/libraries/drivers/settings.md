---
layout: default
title: Settings storage
description: "Saving SimpleFOC motor calibration and settings to flash or EEPROM with the SimpleFOCDrivers settings storage."
nav_order: 6
permalink: /drivers_settings
parent: <span class="simple">Simple<span class="foc">FOC</span>Drivers</span>
grand_parent: libraries
grand_grand_parent: <span class="simple">Simple<span class="foc">FOC</span>utils</span>
toc: true
---

# Settings storage

Every time a <span class="simple">Simple<span class="foc">FOC</span></span> motor starts, `initFOC()` moves the motor to find the sensor direction and the zero electrical angle. Settings storage saves this calibration, and any other settings you choose such as PID gains and limits, to non-volatile memory. On the next startup the values are loaded, and the motor is ready immediately without moving.

Settings storage is based on the [registers](drivers_registers), so any register that can be both read and written can be saved.

[Source and README](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/settings){: .btn .btn-docs}

## Supported storage

| Storage | Class | Notes |
| --- | --- | --- |
| [STM32 flash](#stm32-flash) | `STM32FlashSettingsStorage` | STM32G4 on-board flash |
| [SAMD NVM](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/settings/samd) | `SAMDNVMSettingsStorage` | SAMD on-board flash or RWWEE memory |
| [CAT24 I2C EEPROM](https://github.com/simplefoc/Arduino-FOC-drivers/tree/master/src/settings/i2c) | `CAT24I2CFlashSettingsStorage` | External EEPROM, unfinished |

The storage classes share the same interface, so the code below works with any of them. It's also easy to add your own storage by extending the `SettingsStorage` class.

## Usage

The pattern is: set your defaults, try to load the settings, initialise, and save the settings if they weren't loaded.

```cpp
#include <SimpleFOC.h>
#include <SimpleFOCDrivers.h>
#include "settings/stm32/STM32FlashSettingsStorage.h"

BLDCMotor motor = BLDCMotor(7);
STM32FlashSettingsStorage settings = STM32FlashSettingsStorage();

void setup() {
  SimpleFOCDebug::enable(&Serial);

  // 1. initialise the settings early
  settings.addMotor(&motor);
  settings.init();

  // 2. set your defaults
  motor.voltage_limit = 6;

  // 3. try to load the saved settings, overwriting the defaults
  SettingsStatus loadStatus = settings.loadSettings();

  // 4. initialise as usual
  // ... sensor, driver
  motor.init();
  motor.initFOC(); // skips the alignment if the calibration was loaded

  // 5. save if nothing was loaded yet
  if (motor.motor_status == FOCMotorStatus::motor_ready && loadStatus != SFOC_SETTINGS_SUCCESS) {
    settings.saveSettings();
  }
}
```

The first startup calibrates and saves. Every startup after that loads the calibration and skips the alignment movement.

## Choosing what to save

By default, only the calibration is saved: `REG_ZERO_ELECTRIC_ANGLE` and `REG_SENSOR_DIRECTION`. To save more, pass a list of registers before `settings.init()`:

```cpp
SimpleFOCRegister settingsRegisters[] = {
  REG_ZERO_ELECTRIC_ANGLE, REG_SENSOR_DIRECTION,
  REG_VEL_PID_P, REG_VEL_PID_I, REG_VEL_PID_D, REG_VEL_LPF_T,
  REG_VOLTAGE_LIMIT, REG_CONTROL_MODE, REG_TORQUE_MODE,
  REG_PHASE_RESISTANCE, REG_KV, REG_INDUCTANCE
};

void setup() {
  settings.setRegisters(settingsRegisters, sizeof(settingsRegisters) / sizeof(settingsRegisters[0]));
  settings.addMotor(&motor);
  settings.init();
  // ...
}
```

Saving `REG_PHASE_RESISTANCE` and `REG_INDUCTANCE` is useful together with [motor characterisation](motor_characterisation): measure the motor once, save the result, and skip the measurement afterwards.

## Saving from the Commander

To save values you tuned at runtime, for example with the [web controller](webcontroller), add commands to save and load the settings:

```cpp
Commander command = Commander(Serial);
void doSave(char* cmd) { settings.saveSettings(); }
void doLoad(char* cmd) { settings.loadSettings(); }

void setup() {
  // ...
  command.add('s', doSave, "save settings");
  command.add('l', doLoad, "load settings");
}
```

Tune the motor, then send `s` to keep the values.

## STM32 flash

`STM32FlashSettingsStorage` stores the settings in the STM32's own flash memory. It currently supports **STM32G4** MCUs (2kB flash pages). By default it uses the last page of the flash. To use another location, pass the address:

```cpp
// 10kB below the end of the flash
STM32FlashSettingsStorage settings = STM32FlashSettingsStorage(FLASH_BASE + FLASH_SIZE - 10*1024);
```

<blockquote class="warning" markdown="1"><p class="heading">Flash and reprogramming</p>
Flash used for settings can't be used by your program, so make sure your firmware doesn't fill the whole flash. Also, uploading new firmware may erase the whole flash, including the settings, so the motor will calibrate again on the first startup after an upload. To avoid this, reserve the settings page in your board's linker script.

Dual-bank flash configurations have not been tested.
</blockquote>
