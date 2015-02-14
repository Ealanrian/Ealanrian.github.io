Cmake for Arduino and Clion
===========================

For a project I just started working on I wanted to use Clion with Arduino, since there is no
Arduino plugin or support for Clion yet I had to figure out a way to do this with Cmake. In this
post I'll try to explain what to do.

Required resources
------------------

Ofcourse the Arduino SDK needs to be installed and a Cmake library is required. For this I found 
Arduino Cmake which can be found at https://github.com/queezythegreat/arduino-cmake.

The project
-----------------

So lets get started. First create a project directory, mkdir ~/workspace/ArduinoCmake and move into the directory.
cd ~/workspace/ArduinoCmake. Let's get the toolchain by going a git clone git@github.com:queezythegreat/arduino-cmake.git
This will get the CmakeFiles needed to do arduino related tasks like uploading it to the Arduino.
Next we create a source file: nano main.cpp in this file we put a simple demo:
`#include <HardwareSerial.h>
#include <Arduino.h>
int incomingByte = 0;    // for incoming serial data

void setup() {

    Serial.begin(9600);    // opens serial port, sets data rate to 9600 bps

}

void loop() {
    // send data only when you receive data:

    if (Serial.available() > 0) {

        // read the incoming byte:

        incomingByte = Serial.read();

        // say what you got:

        Serial.print((char)incomingByte);

    }

}
`
This demo will do a serial echo so we can send data to the Arduino which it will echo back.
Next we create the CmakeLists.txt: nano CMakeLists.txt and in this file put the following:
cmake_minimum_required(VERSION 2.8.4)

set(CMAKE_TOOLCHAIN_FILE arduino-cmake/cmake/ArduinoToolchain.cmake)

project(projectName)

set(ARDUINO_DEFAULT_BOARD mega)

set(ARDUINO_DEFAULT_PORT /dev/ttyUSB0)

link_directories(arduino-libraries)

generate_arduino_firmware(projectName

	SRCS main.cpp

)

I will highlight the important lines:
The set(CMAKE_TOOLCHAIN_FILE line tells Cmake where the Arduino toolchain is located. This line must be before the project line.
The next lines, ARDUINO_DEFAULTs, tell the Arduino toolchain which board is used and on which port it is located.
In the generate_arduino_firmware command we tell the toolchain which sourcfiles to use.

Building the project
--------------------
To build the project create a build directory: mkdir build and move into it: cd build. 
Next use cmake to build the project: cmake ../ and this creates make file. To build the project use make and to upload the project use make upload
