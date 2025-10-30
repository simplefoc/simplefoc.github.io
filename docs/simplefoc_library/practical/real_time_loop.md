---
layout: default
title: Real-time loop
nav_order: 6
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /real_time_loop
parent: Practical guides
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
toc: true
---

# Hard real-time FOC loop using timers

In this guide we will focus on the real-time loop implementation in the SimpleFOC library. As the library is implemented to be completely cross-platform, we have opted for a generic asynchronous implementation of the FOC loop, where the `motor.loopFOC()` (Field oriented control runtime) and `motor.move()` (motion control) functions are called in the main loop of the application as fast as possible. 

Typically the users program will look like this:

```cpp
...
void setup() {
    ...
}

void loop() {
    // run the FOC algorithm
    motor.loopFOC();
    
    // run the motion control algorithm
    motor.move();

    // motor monitoring
    motor.monitor();
    // user communication
    command.run();
}
```

However, depending on the application, it might be necessary to run the FOC loop in hard real-time, rather than in the main loop where its execution time might be delayed by other code. One very simple way to achieve this is to use the hardware timers of the microcontroller to call the `motor.loopFOC()` and `motor.move()` functions at a fixed frequency. But, this requires a bit more advanced knowledge of the microcontroller and its hardware timers, and is usually very specific to the microcontroller family you are using.

In this guide we will focus on the STM32 and ESP32 families, as they are the most commonly used microcontrollers in the SimpleFOC community.

## STM32 

For STM32 you can use the `HardwareTimer` class to create a timer that will call the `motor.loopFOC()` and `motor.move()` functions at a fixed frequency. Here is an example of how to do this:

```cpp
#include <SimpleFOC.h>

...
// instantiate the motor, driver, sensor and other components
... 

void setup() {
  
  // initialize all the components


  // create a hardware timer
  // For example, we will create a timer that runs at 10kHz on the TIM5
  HardwareTimer* timer = new HardwareTimer(TIM5);
  // Set timer frequency to 10kHz
  timer->setOverflow(10000, HERTZ_FORMAT); 
  // add the loopFOC and move to the timer
  timer->attachInterrupt([](){
    // call the loopFOC and move functions
    motor.loopFOC();
    motor.move();
  });
  // start the timer
  timer->resume();

  _delay(1000);
}

// the loop will now do only the monitoring and user communication
void loop() {
  // motor monitoring
  motor.monitor();
  // user communication
  command.run();
}

```

<blockquote class="info">
  <p class="heading">ℹ️ Which timer to use?</p>
  You can use any timer that is available on the microcontroller, but make sure not to use the timers that are used by the motor driver or the position sensor. For the STM32 family, the timers used with the PWM will be displayed in the serial monitor when you run the `motor.init()` function. You can also see the timers used by the pins in the docs (find your mcufamily and pins) [see here](https://docs.simplefoc.com/stm32pinouts/)
</blockquote>


## ESP32 

Esp32 microcontrollers have a similar approach to the STM32 family, but the implementation is a bit different. You can use the `hw_timer` library to create a timer that will call the `motor.loopFOC()` and `motor.move()` functions at a fixed frequency. Here is an example of how to do this:

```cpp
#include <SimpleFOC.h>

...
// instantiate the motor, driver, sensor and other components
...

hw_timer_t *timer = NULL;

void IRAM_ATTR foc_loop() {
  // call the loopFOC and move functions
  motor.loopFOC();
  motor.move();
}

void setup() {

  // initialize all the components

  // create a hardware timer
  // 1mhz timer
  timer = timerBegin(1000000);
  // attach the interrupt
  timerAttachInterrupt(timer, &foc_loop);
  // Set alarm to call foc_loop every 100us (10kHz)
  timerAlarm(timer, 100, true, 0);


  _delay(1000);
}

// the loop will now do only the monitoring and user communication
void loop() {
  // motor monitoring
  motor.monitor();
  // user communication
  command.run();
}
```

<blockquote class="info">
  See more info in the <a href="https://espressif-docs.readthedocs-hosted.com/projects/arduino-esp32/en/latest/api/timer.html#example-applications">ESP32 Espressif API documentation</a>.
</blockquote>

<blockquote class="warning" markdown="1">
  <p class="heading">⚠️ Warning:</p>
  Make sure not to set the timer frequency too high. If the `motor.loopFOC()` and `motor.move()` functions take longer to execute than the timer interval, the timer may miss calls or overload the microcontroller. This can cause the motor to malfunction or even crash the microcontroller.
</blockquote>

## Other microcontrollers

Most of the other microcontrollers have similar approaches to the STM32 and ESP32 families, but the implementation might be different. You can check the documentation of the specific microcontroller you are using to see how to create a timer that will call the `motor.loopFOC()` and `motor.move()` functions at a fixed frequency.

Examples: 

- Teensy: - [IntervalTimer](https://www.pjrc.com/teensy/td_timing_IntervalTimer.html)
- Raspberry Pi pico - [RPI_PICO_TimerInterrupt](https://github.com/khoih-prog/RPI_PICO_TimerInterrupt?tab=readme-ov-file#13-set-hardware-timer-frequency-and-attach-timer-interrupt-handler-function)


## Troubleshooting



### Monitoring and user communication no longer working (or any other functionality)
If you notice that the motor monitoring and user communication are no longer working properly, it is likely that the timer frequency is too high and the `motor.loopFOC()` and `motor.move()` functions are taking too long to execute. In this case, you can try to reduce the timer frequency or increase the execution time of the `motor.loopFOC()` and `motor.move()` functions by adding a delay in the timer interrupt.
