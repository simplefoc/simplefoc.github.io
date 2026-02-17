---
layout: default
title: Library Examples
parent: Library Source
nav_order: 5
permalink: /library_examples
grand_parent: Digging deeper
grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---


# Library Examples [v2.4+](https://github.com/simplefoc/Arduino-FOC/releases)

The <span class="simple">Simple<span class="foc">FOC</span>library</span> includes **88+ documented examples** available in the [examples folder on GitHub](https://github.com/simplefoc/Arduino-FOC/tree/master/examples) or through the Arduino IDE at `File > Examples > Simple FOC`.

These examples demonstrate the basic usage of the library with:
- **different microcontroller architectures**: 
    - Arduino UNO, Mega, Nano, DUE
    - STM32 (Bluepill, Nucleo, B-G431B-ESC1)
    - ESP32 and ESP8266
    - Teensy (3.x and 4.x)
    - SAMD (Nano 33 IoT)
    - Silicon Labs (Arduino Nano Matter)
    - Raspberry Pi Pico
    - HMBGC gimbal controller
- **different position sensors**: 
  - Encoders (hardware and software interrupts)
  - Magnetic sensors (SPI, I2C, Analog)
  - Hall sensors
  - Open-loop control (sensorless)
- **different BLDC/Stepper drivers**: 
  - <span class="simple">Simple<span class="foc">FOC</span>Shield</span> (v1, v2, v3)
  - <span class="simple">Simple<span class="foc">FOC</span>Mini</span>
  - <span class="simple">Simple<span class="foc">FOC</span>PowerShield</span>
  - HMBGC gimbal controller
  - DRV8302/DRV8305 drivers
  - ODrive boards
  - Smart Stepper
- **different motion control modes**: 
  - Torque control (voltage and current-based)
  - Velocity control
  - Position/angle control
  - Open-loop velocity and position
  - Custom motion control
- **comprehensive utility functions**:
  - Sensor testing and calibration
  - Finding pole pair number
  - Finding zero offset and sensor orientation
  - Alignment and cogging test
  - Finding KV rating
  - Measuring inductance and resistance
  - Current sense alignment and testing
  - Driver standalone testing
  - Communication testing (Commander, Step/Dir)
  - OSC (Open Sound Control) integration

## Examples folder structure
```shell
> examples
├───hardware_specific_examples                    # https://github.com/simplefoc/Arduino-FOC/tree/master/examples/hardware_specific_examples
│   ├───B_G431B_ESC1                              # STM32 B-G431B-ESC1 board examples
│   ├───Bluepill_examples                         # STM32 Bluepill examples
│   │   ├───encoder
│   │   └───magnetic_sensor
│   ├───DRV8302_driver                            # DRV8302/DRV8305 driver examples
│   │   ├───3pwm_example
│   │   ├───6pwm_example
│   │   ├───esp32_current_control_low_side
│   │   ├───stm32_current_control_low_side
│   │   └───teensy4_current_control_low_side
│   ├───ESP32                                     # ESP32 controller examples
│   │   ├───encoder 
│   │   └───magnetic_sensor
│   ├───HMBGC_example                             # HMBGC gimbal controller examples
│   │   ├───position_control
│   │   └───voltage_control
│   ├───Odrive_examples                           # ODrive board examples
│   │   ├───odrive_example_encoder
│   │   └───odrive_example_spi
│   ├───SAMD_examples                             # SAMD21/51 (Nano 33 IoT) examples
│   │   └───nano33IoT
│   ├───Silabs                                    # Silicon Labs (Arduino Nano Matter) examples
│   │   ├───efr32_hall_sensor_velocity_6pwm
│   │   ├───efr32_open_loop_velocity_6pwm
│   │   └───efr32_torque_velocity_6pwm
│   ├───SimpleFOC-PowerShield                     # SimpleFOC PowerShield examples
│   │   └───version_v02
│   ├───SimpleFOCMini                             # SimpleFOC Mini board examples
│   │   ├───angle_control
│   │   └───open_loop
│   ├───SimpleFOCShield                           # SimpleFOC Shield examples
│   │   ├───version_v1
│   │   ├───version_v2
│   │   └───version_v3
│   ├───Smart_Stepper                             # Smart Stepper examples
│   │   └───smartstepper_control
│   └───Teensy                                    # Teensy board examples
│       ├───Teensy3
│       └───Teensy4
├───motion_control                                # https://github.com/simplefoc/Arduino-FOC/tree/master/examples/motion_control
│   ├───custom_motion_control                     # Custom motion control loop example
│   ├───open_loop_motor_control                   # Open-loop motor control
│   │   ├───open_loop_position_example
│   │   └───open_loop_velocity_example
│   ├───position_motion_control                   # Position/angle motion control
│   │   ├───encoder
│   │   ├───hall_sensor
│   │   └───magnetic_sensor
│   ├───torque_control                            # Torque control examples
│   │   ├───encoder
│   │   ├───hall_sensor
│   │   └───magnetic_sensor
│   └───velocity_motion_control                   # Velocity motion control
│       ├───encoder
│       ├───hall_sensor
│       └───magnetic_sensor
├───motor_commands_serial_examples                # Serial commander examples - https://github.com/simplefoc/Arduino-FOC/tree/master/examples/motor_commands_serial_examples
│   ├───encoder
│   │   └───full_control_serial
│   ├───hall_sensor
│   │   └───full_control_serial
│   └───magnetic_sensor
│       └───full_control_serial
├───osc_control_examples                          # Open Sound Control (OSC) examples - https://github.com/simplefoc/Arduino-FOC/tree/master/examples/osc_control_examples
│   ├───osc_esp32_3pwm
│   └───osc_esp32_fullcontrol
└───utils                                         # Utility and testing examples - https://github.com/simplefoc/Arduino-FOC/tree/master/examples/utils
    ├───calibration                               # Motor and sensor calibration
    │   ├───alignment_and_cogging_test
    │   ├───find_kv_rating
    │   ├───find_pole_pair_number
    │   ├───find_sensor_offset_and_direction
    │   └───measure_inductance_and_resistance
    ├───communication_test                        # Communication interface testing
    │   ├───commander
    │   └───step_dir
    ├───current_sense_test                        # Current sensing testing
    │   ├───align_current_sense
    │   ├───generic_current_sense
    │   └───inline_current_sense_test
    ├───driver_standalone_test                    # Driver testing (without motor)
    │   ├───bldc_driver_3pwm_standalone
    │   ├───bldc_driver_6pwm_standalone
    │   ├───stepper_driver_2pwm_standalone
    │   └───stepper_driver_4pwm_standalone
    └───sensor_test                               # Sensor testing
        ├───encoder
        │   ├───encoder_example
        │   └───encoder_software_interrupts_example
        ├───hall_sensors
        │   ├───hall_sensor_example
        │   └───hall_sensor_software_interrupts_example
        └───magnetic_sensors
            ├───magnetic_sensor_analog_example
            ├───magnetic_sensor_i2c_example
            └───magnetic_sensor_spi_example
```
