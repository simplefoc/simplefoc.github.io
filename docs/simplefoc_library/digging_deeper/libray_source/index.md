---
layout: default
title: Library Source
nav_order: 1
parent: Digging deeper
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /source_code
has_children: True
has_toc: false
toc: true
---


# Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> Source Code [v2.4.0](https://github.com/simplefoc/Arduino-FOC/releases)
The library follows the standard [Arduino library structure](https://github.com/arduino/Arduino/wiki/Library-Manager-FAQ) and provides FOC control for three motor types:

- **BLDCMotor**: Standard 3-phase BLDC motors
- **StepperMotor**: 2-phase stepper motors  
- **HybridStepperMotor**: 2-phase stepper motors with BLDC drivers

The codebase is organized into functional modules:

- **sensors/**: Position sensors (encoders, magnetic sensors, Hall sensors)
- **drivers/**: Motor drivers (3PWM, 6PWM, 2PWM, 4PWM configurations)
- **current_sense/**: Current sensing implementations (inline, low-side, high-side)
- **communication/**: Communication protocols (Commander, Step/Dir)
- **common/**: Utility classes and base classes (PID, LPF, FOCMotor base) 

The library is **cross-platform** and supports multiple microcontroller architectures, with hardware-specific implementations abstracted in the `hardware_specific/` folders.

## Library Source Structure

```sh
src/
├─ SimpleFOC.h                 # Main include file
├─ BLDCMotor.cpp/h             # BLDC motor class
├─ StepperMotor.cpp/h          # Stepper motor class
├─ HybridStepperMotor.cpp/h    # Hybrid stepper motor class
│
├─── common/                   # Utility classes and base classes
│    ├─ base_classes/          # Abstract base classes
│    │   ├─ FOCMotor.cpp/h     # Base motor class
│    │   ├─ BLDCDriver.h       # Base BLDC driver interface
│    │   ├─ StepperDriver.h    # Base stepper driver interface
│    │   ├─ Sensor.h           # Base sensor interface
│    │   └─ CurrentSense.h     # Base current sense interface
│    ├─ pid.cpp/h              # PID controller implementation
│    ├─ lowpass_filter.cpp/h   # Low-pass filter implementation
│    ├─ foc_utils.cpp/h        # FOC utility functions
│    └─ time_utils.cpp/h       # Time measurement utilities
│
├─── drivers/                  # Motor driver implementations
├─── sensors/                  # Position sensor implementations
├─── current_sense/            # Current sensing implementations
└─── communication/            # Communication protocols
```

<blockquote class="info">For more info visit <a href="http://source.simplefoc.com/" target="_blank"> full source code documentation <i class="fa fa-external-link fa-sm"></i></a></blockquote>

## Motor Classes

### Base class: `FOCMotor.cpp/h`
Base motor class with common FOC control logic, used as a parent class for all motor types
- Multiple torque control modes (voltage, dc_current, foc_current, estimated_current)
- 7 motion control types (torque, velocity, angle, etc.)
- Open-loop and closed-loop operation
- Position sensor and current sense integration nad alignment procedures

There are three child classes that extend `FOCMotor` with specific implementations for different motor types:
- `BLDCMotor.cpp/h`: Standard 3-phase BLDC motors
- `StepperMotor.cpp/h`: 2-phase stepper motors
- `HybridStepperMotor.cpp/h`: 2-phase stepper motors optimized for high pole counts

### `BLDCMotor.cpp/h`
BLDC motor implementation for standard 3-phase brushless DC motors:
- FOC algorithm with 4 modulation types (SinePWM, SpaceVectorPWM, Trapezoid_120, Trapezoid_150)
- Can be paired with any position sensor and current sense implementation
- Can be paired with any BLDC driver (3PWM or 6PWM) only

### `StepperMotor.cpp/h`
Stepper motor implementation for standard 2-phase stepper motors:
- FOC-based stepper control (SinePWM only)
- Can be paired with any position sensor and current sense implementation
- Can be paired with any Stepper driver (2PWM or 4PWM) only

### `HybridStepperMotor.cpp/h`
Hybrid stepper motor implementation for standard 2-phase stepper motors used wiht BLDC drivers:
- FOC-based stepper control (SinePWM and SpaceVectorPWM)
- Can be paired with any position sensor and current sense implementation
- Can be paired with any BLDC driver (3PWM or 6PWM) only

---

### 📚 Implementation Documentation

[FOC workflow Implementation](foc_implementation){: .btn .btn-primary}
[Motion Control Implementation](motion_control_implementation){: .btn .btn-primary}
[Torque Control Implementation](torque_control_implementation){: .btn .btn-primary}

[PID Controller Implementation](pid_implementation){: .btn .btn-docs}
[Low-Pass Filter Implementation](lpf_implementation){: .btn .btn-docs}


## Motor Drivers

All motor drivers are located in the `drivers/` directory:

```sh
drivers/
├─ BLDCDriver3PWM.cpp/h       # 3-PWM BLDC driver (3 half-bridges)
├─ BLDCDriver6PWM.cpp/h       # 6-PWM BLDC driver (3 full H-bridges)
├─ StepperDriver2PWM.cpp/h    # 2-PWM stepper driver (1 per phase)
├─ StepperDriver4PWM.cpp/h    # 4-PWM stepper driver (2 per phase)
│      
├─ hardware_api.h             # MCU-specific PWM configuration API
│
└─── hardware_specific/       # MCU-specific implementations
     └─ (various MCU implementations)
```

### BLDC Driver Interface

All BLDC drivers implement the `BLDCDriver` abstract class:

```cpp
class BLDCDriver {
public:
    /** Initialize hardware */
    virtual int init();
    /** Enable driver */
    virtual void enable();
    /** Disable driver */
    virtual void disable();

    long pwm_frequency;          // PWM frequency in Hz
    float voltage_power_supply;  // Power supply voltage
    float voltage_limit;         // Maximum voltage to motor
        
    /** Set phase voltages to hardware */
    virtual void setPwm(float Ua, float Ub, float Uc) = 0;
    
    /** Set phase state (active/high-impedance) */
    virtual void setPhaseState(PhaseState sa, PhaseState sb, PhaseState sc) = 0;
};

enum PhaseState {
    PHASE_ON,   // Phase actively driven
    PHASE_OFF   // Phase in high-impedance (for trapezoidal commutation)
};
```


### Stepper Driver Interface

And all the stepper drivers implement the `StepperDriver` abstract class.
```cpp
class StepperDriver{
    public:
        
        /** Initialise hardware */
        virtual int init();
        /** Enable hardware */
        virtual void enable();
        /** Disable hardware */
        virtual void disable();

        long pwm_frequency; //!< pwm frequency value in hertz
        float voltage_power_supply; //!< power supply voltage 
        float voltage_limit; //!< limiting voltage set to the motor
            
        /** 
         * Set phase voltages to the hardware 
         * 
         * @param Ua phase A voltage
         * @param Ub phase B voltage
        */
        virtual void setPwm(float Ua, float Ub);
};
```

Furthermore all the supported MCU architectures with the simplefoc library have to implement the header file `hardware_api.h`. The off-the-shelf supported architectures will have implementation of the `hardware_api.h` placed in the `hardware_specific` folder. If you wish to implement a new MCU please do create a new instance of the `my_new_mcu.cpp` and implement all the functions from the `hardware_api.h`, or at least the ones that you need.

## Position Sensors

```sh
sensors/
├─ Encoder.cpp/h              # Quadrature encoder (A/B, optional index)
├─ MagneticSensorSPI.cpp/h    # SPI magnetic sensors (AS5047, AS5048, MA730)
├─ MagneticSensorI2C.cpp/h    # I2C magnetic sensors (AS5600)
├─ MagneticSensorAnalog.cpp/h # Analog magnetic sensors
├─ MagneticSensorPWM.cpp/h    # PWM output magnetic sensors
├─ HallSensor.cpp/h           # Hall effect sensors (3-sensor commutation)
└─ GenericSensor.cpp/h        # Wrapper for custom sensor implementations
```

All position sensors implement the `Sensor` abstract class, making them interchangeable with any motor class:

```cpp
class Sensor {
public:
    /** Get current angle in radians [0, 2π] */
    virtual float getAngle() = 0;
    
    /** Get current angular velocity in rad/s */
    virtual float getVelocity();
    
    /** Check if sensor needs index search */
    virtual int needsSearch();
    
    /** Initialize sensor */
    virtual void init();
    
    /** Update sensor reading (for sensors requiring polling) */
    virtual void update();
};
```

Link a sensor to a motor:
```cpp
motor.linkSensor(&sensor);
```

### Custom Sensor Implementation

To implement a custom sensor there are two approaches:

- Extend the `Sensor` class **(Advanced users only, not recommended for most users)**

[See a quick guide to implement your own sensor class (Advanced)](sensor_support){: .btn .btn-docs}

- Implement the `GenericSensor` callbacks. This approach allows you to use the `GenericSensor` class without creating a new class **(Recommended for most users)**

[See a quick guide how use Generic Sensor class (Recommended)](generic_sensor){.: .btn .btn-docs}



## Current Sensing

```sh
current_sense/
├─ InlineCurrentSense.cpp/h       # Inline (shunt) current sensing
├─ LowsideCurrentSense.cpp/h      # Low-side current sensing
├─ GenericCurrentSense.cpp/h      # Wrapper for custom implementations
│
├─ hardware_api.h                 # MCU-specific ADC configuration API
│
└─── hardware_specific/           # MCU-specific ADC implementations
     └─ (various MCU implementations)
```

All current sense classes implement the `CurrentSense` interface:

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

    /** Align current sense with motor driver (default implementation available) */
    virtual int driverAlign(float align_voltage, bool modulation_centered = false);

    /** Enable/disable current sense (optional override) */
    virtual void enable();
    virtual void disable();

    /** Utility: read averaged currents */
    PhaseCurrent_s readAverageCurrents(int N = 100);

    bool skip_align = false;  // Skip alignment during initFOC()
};
```

All the current sensing implementations extend this base class, are interchangeable, and can be used with any motor type. 

### Current Sensing Methods

You can also create your own current sensing strategy by either:
-  Extending the `CurrentSense` class **(Advanced users only, not recommended for most users)**

[See library source code for more details (Advanced)](current_sense_support){: .btn .btn-docs}
-  Implementing the `GenericCurrentSense` callbacks. This approach allows you to use the `GenericCurrentSense` class without creating a new class **(Recommended for most users)**

[Generic Current Sense Guide (Recomended)](generic_current_sense){: .btn .btn-docs}



### Torque Control Modes

Current sensing enables advanced torque control modes:

- **dc_current**: Uses `getDCCurrent()` - measures total current magnitude
- **foc_current**: Uses `getFOCCurrents()` - measures d and q axis currents separately
- **estimated_current**: Estimates current from voltage and motor resistance (no sensor needed)

Link current sense to motor:
```cpp
motor.linkCurrentSense(&current_sense);
```

## Communication Protocols

```sh
communication/
├─ Commander.cpp/h          # G-code-like serial command interface
├─ commands.h               # Built-in command definitions
├─ StepDirListener.cpp/h    # Step/direction interface
└─ SimpleFOCDebug.h         # Debug output utilities
```

### Commander Interface

The `Commander` class provides a powerful ASCII command interface:

- **G-code-style commands**: Human-readable motor control
- **Parameter access**: Runtime tuning of PID, limits, and control parameters
- **Multi-motor support**: Control multiple motors from one serial interface
- **Built-in commands**: For `FOCMotor`, `PIDController`, and `LowPassFilter` classes

[Commander Interface Documentation](commander_interface){: .btn .btn-docs}
[Motor Commands Reference](commands_source){: .btn .btn-docs}

### Step/Direction Listener

The `StepDirListener` class enables standard stepper motor interfaces:

- **Step/direction protocol**: Compatible with CNC controllers
- **Pulse counting**: Tracks position from step pulses
- **Direction control**: Responds to direction pin changes

[Step/Direction Listener Documentation](step_dir_interface){: .btn .btn-docs}


## Common Utilities

```sh
common/
├─── base_classes/            # Abstract base classes
│    ├─ FOCMotor.cpp/h        # Base motor class (shared by all motor types)
│    ├─ BLDCDriver.h          # Base BLDC driver interface
│    ├─ StepperDriver.h       # Base stepper driver interface  
│    ├─ Sensor.h              # Base sensor interface
│    ├─ CurrentSense.h        # Base current sense interface
│    └─ FOCDriver.h           # Common driver interface
│
├─ pid.cpp/h                  # PID controller implementation
├─ lowpass_filter.cpp/h       # Low-pass filter implementation
├─ foc_utils.cpp/h            # FOC algorithm utilities
├─ time_utils.cpp/h           # Time measurement and delays
└─ defaults.h                 # Default configuration parameters
```

### Base Classes

All motors, drivers, sensors, and current sensors extend abstract base classes defined in `base_classes/`. This architecture ensures:

- **Interchangeable components**: Any sensor works with any motor
- **Consistent interfaces**: Same methods across all implementations
- **Easy extension**: Add new hardware by implementing base class methods

### Signal Processing Classes

**PIDController** (`pid.cpp/h`):
- Discrete PID implementation with Tustin integration
- Anti-windup protection via output clamping
- Output rate limiting (ramp)
- Used for velocity, position, and current control

[PID Implementation Details](pid_implementation){: .btn .btn-docs}

**LowPassFilter** (`lowpass_filter.cpp/h`):
- First-order discrete low-pass filter
- Configurable time constant (Tf)
- Used for velocity and current filtering

[LPF Implementation Details](lpf_implementation){: .btn .btn-docs}

### Default Configuration

The `defaults.h` file defines sensible starting values:

```cpp
// Power supply
#define DEF_POWER_SUPPLY 12.0           // Default voltage [V]

// Velocity PID defaults
#define DEF_PID_VEL_P 0.5               // Proportional gain
#define DEF_PID_VEL_I 10.0              // Integral gain
#define DEF_PID_VEL_D 0.0               // Derivative gain
#define DEF_PID_VEL_U_RAMP 1000.0       // Output ramp [V/s]

// Position P controller defaults
#define DEF_P_ANGLE_P 20.0              // Proportional gain
#define DEF_VEL_LIM 20.0                // Velocity limit [rad/s]

// Sensor alignment
#define DEF_VOLTAGE_SENSOR_ALIGN 3.0    // Alignment voltage [V]
#define DEF_INDEX_SEARCH_TARGET_VELOCITY 1.0  // Index search velocity

// Filtering
#define DEF_VEL_FILTER_Tf 0.005         // Velocity LPF time constant [s]
```

These values work well for gimbal motors but may need tuning for your specific application.



## Additional Resources

### Implementation Documentation

Detailed explanations of key library components:

[FOC Workflow Implementation](foc_implementation){: .btn .btn-primary}
[Motion Control Implementation](motion_control_implementation){: .btn .btn-primary}
[Torque Control Implementation](torque_control_implementation){: .btn .btn-primary}

[PID Controller Implementation](pid_implementation){: .btn .btn-docs}
[Low-Pass Filter Implementation](lpf_implementation){: .btn .btn-docs}

### Examples

The library includes 88+ examples covering:
- Hardware-specific configurations (ESP32, STM32, Teensy, SAMD, etc.)
- Motion control modes (torque, velocity, position)
- Current sensing and torque control
- Communication interfaces
- Multi-motor systems

[Browse All Examples](library_examples){: .btn .btn-primary}

### API Documentation

Complete API reference with Doxygen:

<div class="image_icon width80">
    <a href="http://source.simplefoc.com/" target="_blank">
        <img src="extras/Images/source_docs.jpg">
        <i class="fa fa-external-link-square fa-2x"></i>
    </a>
</div>

[Full API Documentation <i class="fa fa-external-link"></i>](http://source.simplefoc.com/){: target="_blank"}

