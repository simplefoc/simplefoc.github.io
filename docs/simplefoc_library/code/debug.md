---
layout: default
title: Debugging
nav_order: 9
permalink: /debugging
parent: Writing the Code
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span> 
toc: true
---


# Debugging <span class="simple">Simple<span class="foc">FOC</span>library</span>  sketches

So you hooked everything up, downloaded your sketch, applied power, and... nothing. Or another common scenario: motor jerks around and makes terrible sounds.

Don't worry! Trust us, we've been there. *Everyone* has this experience, *nobody* has a working setup on the first try. BLDC motor control isn't always easy, and there are lots of mistakes to be made.

There are different techniques to help you find problems on the software side, but one extremely useful one is to produce some debug output, and having a way to see debug output in a `Serial` terminal will be invaluable when hunting for problems.

## Debug functionality

<span class="simple">Simple<span class="foc">FOC</span>library</span> provides debug output using the `Serial` port which is enabled by adding the following line to your `setup` function.

Place this function call early in the `setup` function to see the debug output of the motor initialization, which can be important to help you find problems with your setup.


```cpp
SimpleFOCDebug::enable();
```

or specify the `Serial` port instance to use:
```cpp
SimpleFOCDebug::enable(&Serial);
```

<blockquote class="info">
Note: you can also use other serial ports, e.g. Serial1, Serial2, as supported by your MCU.
</blockquote>

### Debugging the motor init

The `motor` will produce debug output about its status during the initialization `motor.init()` and the alignment procedure `motor.initFOC()`. Enabling this functionality will not directly influence the real-time performance because there is no debug output in real time-loop in the functions `motor.loopFOC()` and `motor.move()`.

This is an example of the `motor` initialization monitoring output gone well:
```sh
MOT: Monitor enabled!
MOT: Init
MOT: Enable driver.
MOT: Align sensor.
MOT: sensor direction==CW
MOT: PP check: OK!
MOT: Zero elec. angle: 4.28
MOT: Align current sense.
MOT: Success: 2
MOT: Ready.
```

Failed motor initialization due to the position sensor:
```sh
MOT: Monitor enabled!
MOT: Init
MOT: Enable driver.
MOT: Align sensor.
MOT: Failed to notice movement
MOT: Init FOC failed.
```

And failed motor initialization due to the current sense:
```sh
MOT: Monitor enabled!
MOT: Init
MOT: Enable driver.
MOT: Align sensor.
MOT: sensor direction==CW
MOT: PP check: OK!
MOT: Zero elec. angle: 4.28
MOT: Align current sense.
MOT: Fail!
MOT: Init FOC failed.
```

### Writing your own debug statements

You can use the <span class="simple">Simple<span class="foc">FOC</span>library</span>  easily for your own purposes:

```cpp
SIMPLEFOC_DEBUG("Hello world!");
SIMPLEFOC_DEBUG("Float value: ", fval);
SIMPLEFOC_DEBUG("Int value: ", ival);
```

Using this macro has several advantages, see FlashStringHelper, global disable and rationale, below.

See the [SimpleFOCDebug class header](https://github.com/simplefoc/Arduino-FOC/blob/master/src/communication/SimpleFOCDebug.h) for all available methods.

### FlashStringHelper

The SimpleFOCDebug macro automatically uses FlashStringHelper, and you should not use the F() macro on strings supplied to the `SIMPLEFOC_DEBUG` macro.

### Debug - global disable

Using the build flag `SIMPLEFOC_DISABLE_DEBUG` you can globally disable all debug output, potentially also saving a few bytes of program space if you are short on memory.

### Rationale

Why have our own `SimpleFOCDebug`? Wny not just use `Serial.println`?

When supporting many hardware platforms (as <span class="simple">Simple<span class="foc">FOC</span>library</span> does), we can't make assumptions about the availability of the `Serial` object. While it is a pretty standard feature of Arduino framework, a given board may not support it, or may for whatever reasons have a different name for it. We also can't make assumptions about *which* `Serial` object you would want to use. Some MCUs support 6 or more Serial ports.

We also like the abstraction it provides, making it easier to port <span class="simple">Simple<span class="foc">FOC</span>library</span> to other platforms/frameworks. In future we may abstract the debug output functionality one level further, allowing debug output via SPI, MQTT or other protocols. For this reason it is also good to have a separation between debug and `Serial`.

## Other debugging methods

### Plain old Serial.println

*Can I just use `Serial` output as per normal in my <span class="simple">Simple<span class="foc">FOC</span>library</span> sketch?*

Yes and no... There is no problem with calling Serial.println() in your sketches, you are not forced to use our debug facility as described above.

*But* you have to care not to use too much time for `Serial` output. The `move` and `loopFOC` functions need to called very often in your main `loop` and you can't output to the `Serial` port all time. You will have to write the code in such a way to minimize the bytes you output, and include some timing code to make sure you only output once or twice a second.

### Spare pins

A great way to debug simple things is to use `digitalWrite` and an unused pin:
```cpp
digitalWrite(5, HIGH); // 5 is an unused pin

// ...

digitalWrite(5, LOW);
```

Using this technique will not impact the execution time, and using your oscilloscope or logic analyzer you can check the output pin and see things like how long calls are taking to execute, whether the code is taking certain branches, etc...

### IDE Debugger

Using a real debugger with <span class="simple">Simple<span class="foc">FOC</span>library</span> can be a challenge. The code is very real-time dependent, and can't easily be paused or run more slowly. In the author's experience using the debugger slows execution to the point that BLDC motor control is quite impossible.

But it would depend on the problem you're trying to catch, the MCU and debugging hardware used and other factors, so keeping the real-time considerations in mind, give it a shot in the debugging tool of your choice and see how it goes.

### Crowdsource it!

We can't stress enough how much help the <span class="simple">Simple<span class="foc">FOC</span>library</span> communities can be if you're having problems!

We have a [Discourse Forum](https://community.simplefoc.com/) and a [Discord Server](https://discord.com/invite/JbH772tfnB) you can join any time and ask questions. Our members are both knowledgeable and helpful, so rather than sweat it out by yourself, drop by and ask some like-minded souls for advice.

### Report it!

If you isolate your problem, and find it to be *our* problem, please don't hesitate to [file a bug report on GitHub](https://github.com/simplefoc/Arduino-FOC/issues/new)!
