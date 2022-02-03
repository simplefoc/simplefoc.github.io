---
layout: default
title: Custom commands
nav_order: 6
permalink: /commander_custom
parent: Commander Interface
grand_parent: Communication
grand_grand_parent: Writing the Code
grand_grand_grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Extending commander with custom functionality

To extend the commander interface with your own functionality that has not been implemented by the <span class="simple">Simple<span class="foc">FOC</span>library</span> you only really need to:
1. implement your callback function `void myFunc(char*){}`
2. add it to the commander `commander.add('.',myFunc,"..")`

```cpp
void myFunc(char*){
  // do something useful
}

Commander commander = Commander(...)
void setup(){
...
  commander.add('A',myFunc,"my functionality");
...
}
void loop(){
  ...
  commander.run()
}
```

## Example 

This is an example code of extending the commander interface with two new functionalities, turning on and off a led light and reading 5 analog pins.
```cpp

#include <SimpleFOC.h>

// instantiate the commander
Commander command = Commander(Serial);

// led control function
void doLed(char* cmd){ 
    if(atoi(cmd)) digitalWrite(LED_BUILTIN, HIGH); 
    else digitalWrite(LED_BUILTIN, LOW); 
};
// get analog input 
void doAnalog(char* cmd){ 
    if (cmd[0] == '0') Serial.println(analogRead(A0));
    else if (cmd[0] == '1') Serial.println(analogRead(A1));
    else if (cmd[0] == '2') Serial.println(analogRead(A2));
    else if (cmd[0] == '3') Serial.println(analogRead(A3));
    else if (cmd[0] == '4') Serial.println(analogRead(A4));
};

void setup() {
    // define pins
    pinMode(LED_BUILTIN, OUTPUT);
    pinMode(A0, INPUT);
    pinMode(A1, INPUT);
    pinMode(A2, INPUT);
    pinMode(A3, INPUT);
    pinMode(A4, INPUT);

    // Serial port to be used
    Serial.begin(115200);

    // add new commands
    command.add('L', doLed, "led on/off");
    command.add('A', doAnalog, "analog read A0-A4");

    Serial.println(F("Commander listening"));
    Serial.println(F(" - Send ? to see the node list..."));
    Serial.println(F(" - Send L0 to turn the led off and L1 to turn it off"));
    Serial.println(F(" - Send A0-A4 to read the analog pins"));
    _delay(1000);
}


void loop() {

    // user communication
    command.run(); 
    _delay(10);
}
```

Then in your serial terminal you can just
```sh 
$ ?            # list the commands
L: led on/off
A: analog read A0-A4
$ L0           # led off
$ A1           # read A1
321            
$ A3           # read A3
1023            
$ L1           # led on
$ L0           # led off
``` 
