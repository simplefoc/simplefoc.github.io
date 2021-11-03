---
layout: default
title: PlatformIO
parent: Installation
nav_order: 2
description: "Arduino Simple Field Oriented Control (FOC) library ."
permalink: /library_platformio
grand_parent: Arduino <span class="simple">Simple<span class="foc">FOC</span>library</span>
---

# Installing <span class="simple">Simple<span class="foc">FOC</span>library</span> with PlatformIO

Using SimpleFOC from PlatformIO is not hard at all! The following instructions should get you set up and compiling your code in a matter of minutes.

## Prerequisites

- You need an active internet connection throughout this process, until your project is set up.
- First, install [Visual Studio Code](https://code.visualstudio.com/download)
- Run Visual Studio Code, and install PlatformIO using the Extensions Manager:

<img src="extras/Images/platformio_screenshot1.png">
1. Select extension manager
2. Enter "PlatformIO" in the search
3. Choose PlatformIO extension from the list (should be the first result)
4. Click install - after a few moments PlatformIO will be installed, you will be prompted to reload Visual Studio Code

Congratulations, you're ready to start your first project!

## Creating a project

To create a new project that uses SimpleFOC, use the following steps:

<img src="extras/Images/platformio_screenshot2.png">
1. Select the PlatformIO Menu on the left sidebar
2. Choose Open to open the platformIO home screen
3. Click "New Project"

You will be taken to the new project wizard:

<img src="extras/Images/platformio_screenshot3.png" class="width50">
1. Give your project a name, e.g. simplefoc_test_project
2. Choose your board. SimpleFOC supports many MCU architectures, see [hardware/mcus]
3. Once you have chosen your board, make sure the Framework "Arduino" is selected. PlatformIO supports other Frameworks, but SimpleFOC is an Arduino Library and must be run in Arduino framework.
4. Click finish

At this point, if it is the first project you are creating for this board, PlatformIO will download all the required compilers, tools and core library files. Depending on your internet speed, this can take quite a few minutes, so please be patient!

## Add <span class="simple">Simple<span class="foc">FOC</span>library</span> to your project

When everything is finished installing, you should be presented with a view like this:

<img src="extras/Images/platformio_screenshot4.png">

You can now add the SimpleFOC library to the project:

<img src="extras/Images/platformio_screenshot5.png">
1. Click on the PlatformIO icon, and choose "Libraries" from the Menu
2. Enter "Simple FOC" in the search
3. Click on the library

<img src="extras/Images/platformio_screenshot6.png">
1. Click on "Add Library" to display the "Add Library" wizard

<img src="extras/Images/platformio_screenshot7.png" class="width50">
1. Choose your project from the drop-down
2. Click "Add" to add the library

Cool! The SimpleFOC library is now associated with your project.

## Configure your project

Open the platformio.ini file in the root directory of your project. It should look something like this, depending on the board you chose:

```
; PlatformIO Project Configuration File
;
;   Build options: build flags, source filter
;   Upload options: custom upload port, speed and extra flags
;   Library options: dependencies, extra library storages
;   Advanced options: extra scripting
;
; Please visit documentation for the other options and examples
; https://docs.platformio.org/page/projectconf.html

[env:mkrwifi1010]
platform = atmelsam
board = mkrwifi1010
framework = arduino
lib_deps = askuric/Simple FOC@^2.2
```

Important!
You have to add the line *lib_archive = false* to make SimpleFOC compile correctly with PlatformIO.

You may also want to add some other options, like *monitor_speed* to set the serial port speed for debug output.

```
lib_archive = false
monitor_speed = 115200
```

The available options depend on your board, and we cannot cover them all here, but they are documented in PlatformIO's documentation.

## Code away!

You can begin to write your code in the *src/main.cpp* file.

## Youtube tutorial by [@owennewo](https://github.com/owennewo)

<iframe class="youtube" src="https://www.youtube.com/embed/3B88qCny7Kg" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## Advanced Usage

PlatformIO makes it easy for you to use the source version of the SimpleFOC library. This lets you modify the code, or use the most recent "dev" branch version of the library to try out new features before they are officially released.

To use the source code version of the library, try the following steps:

1. Using your favourite git client, clone the simplefoc library from https://github.com/simplefoc/Arduino-FOC.git
2. This will create a folder "Arduino-FOC" on your file-system, containing the library source code
3. Go to your PlatformIO project, and inside the project go to the lib folder. You can use your operating system's shell, or the built-in shell in Visual Studio Code.
4. Inside the lib folder of your project, create a symlink to your Arduino-FOC folder<br/>
Mac/Linux:<br/>
`% ln -s /path/to/Arduino-FOC`<br/>
Windows:<br/>
`% mklink /J Arduino-FOC C:\path\to\Arduino-FOC`

Note: If you had previously added the release version of the library to the project using PlatformIO's library management, you will have to remove this reference from the platformio.ini file