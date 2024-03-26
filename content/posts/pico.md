---
title: Line Follower robot with PID control. Part 1
date: 2024-02-15
tags: [Line Follower]
social_image: '/media/pico.png'
description: I have made the exciting decision to embark on a project; crafting a line follower robot utilizing the Pico W and C SDK and to try implement PID control on it. This endeavor merges my passion for robotics with the challenge of programming and hardware tinkering. The Pico W's compact yet powerful design, coupled with the versatility of the C SDK, offers an ideal platform for this venture. With its robust capabilities and extensive community support, I'm confident in my ability to bring this concept to fruition. Stay tuned as I delve into the intricacies of building and programming this line follower robot, documenting my progress and discoveries along the way.

---
## Project Requirements
1. The robot should be able to follow a black on white line
2. PID control should be intergrated to ensure stability in maintaining the line

## Parts List
1. 1 Raspberry pi pico w
2. 1 L298N motor driver
3. 1 Chasis
4. 2 Motors
5. 2 Wheels
6. 1 optocoupler
7. I breadboard
8. 2 3.7V batteries
9. Line tracking sensor

![Rocket launch](/media/pico.png)

## Approach
- I decided to do it in modules where I will learn about a sensor or device, try to implement it then write its function. Then at the end of it all, I will intergrate all functions and assemble the robot.
- In this module, we will start with rasp berry pi pico w. We'll install the C sdk and try to write some code for it to blink an inbuilt led.

## Instaling Pico c sdk

The Pico C SDK (Software Development Kit) is a set of tools, libraries, and documentation provided by Raspberry Pi for developing software applications specifically for the Raspberry Pi Pico microcontroller board. It includes various libraries and APIs (Application Programming Interfaces) that enable developers to interact with the hardware features of the Pico, such as GPIO (General Purpose Input/Output) pins, UART (Universal Asynchronous Receiver-Transmitter) communication, SPI (Serial Peripheral Interface), I2C (Inter-Integrated Circuit) communication, and more.

For my project, I am doing the installation on an Ubuntu linux machine. </br>

First, we install the prerequisites</br>


> bash
```bash
sudo apt update
sudo apt install cmake gcc-arm-none-eabi libnewlib-arm-none-eabi build-essential libstdc++-arm-none-eabi-newlib

```
We then obtain the c sdk from github. For my case, I am installing the sdk on my home directory but feel free to place it anywhere.

```bash
mkdir -p  ~/pico
cd ~/pico/
git clone -b master https://github.com/raspberrypi/pico-sdk.git
cd pico-sdk
git submodule update --init
# Add SDK path to your environment
echo 'export PICO_SDK_PATH=$HOME/pico/pico-sdk' >> ~/.bashrc 

```

That is it. We have now installed pico c sdk on our machines. 
Now to try blinking an LED on our board. 

## Blinking an LED
>
```bash
cd ~/pico
mkdir projects/blink
cd projects/blink/
touch main.c
touch CMakeLists.txt
cp $PICO_SDK_PATH/external/pico_sdk_import.cmake .

```


In pico_w, the inbuild led pin is shared with thw wifi module so in order to blink the led, we will have to initialise the wifi module.
The code is as follows:
>main.c
```bash
#include "pico/stdlib.h"
#include "pico/cyw43_arch.h"

int main() {
    stdio_init_all();
    if (cyw43_arch_init()) {
        printf("Wi-Fi init failed");
        return -1;
    }
    while (true) {
        cyw43_arch_gpio_put(CYW43_WL_GPIO_LED_PIN, 1);
        sleep_ms(250);
        cyw43_arch_gpio_put(CYW43_WL_GPIO_LED_PIN, 0);
        sleep_ms(250);
    }
}

```

we first start by including the pico stdlib library that provides standard library functions and the cyw43 library which provides functions specific to the wif module on pico w </br>
In the main function, we first initialise all the standard I/O resources.</br>
cyw43_arch_init() tries to initialize thw wifi module. 
inside the while loop, we toggle the led pin every 250 milliseconds.

We now have to create the CMakeLists.txt to provide instructions for the compilation of our code

>CMakeLists.txt
```bash
# set minimum version of cmake
cmake_minimum_required(VERSION 3.12)

#include build functions from the pico sdk
include(pico_sdk_import.cmake)
set(PICO_BOARD pico_w)


# set name of project And c/cpp standards

project(blink C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

#create a pico-sdk subdirectory for libraries
pico_sdk_init()

# where the excecutable is
add_executable(${PROJECT_NAME}
    main.c
)

#create map for other compiled formats

pico_add_extra_outputs(${PROJECT_NAME})

# link to pico_stdlib
target_link_libraries(${PROJECT_NAME}
    pico_stdlib
    pico_cyw43_arch_none
)

```
In this code:
cmake_minimum_required(VERSION 3.12): Specifies the minimum version of CMake required to build the project.

include(pico_sdk_import.cmake): Includes the build functions from the Pico SDK by importing the pico_sdk_import.cmake file.

set(PICO_BOARD pico_w): Sets the target board to "pico_w", indicating that the project is intended for the Raspberry Pi Pico W.

project(blink C CXX ASM): Defines the project name as "blink" and specifies the languages used in the project (C, C++, and Assembly).

set(CMAKE_C_STANDARD 11): Sets the C standard to C11.

set(CMAKE_CXX_STANDARD 17): Sets the C++ standard to C++17.

pico_sdk_init(): Initializes the Pico SDK.

add_executable(${PROJECT_NAME} main.c): Creates an executable named "blink" from the source file "main.c".

pico_add_extra_outputs(${PROJECT_NAME}): Specifies additional outputs to generate (e.g., .hex, .uf2 files) for the project.

target_link_libraries(${PROJECT_NAME} pico_stdlib pico_cyw43_arch_none): Links the executable with the Pico standard library (pico_stdlib) and the CYW43 architecture-specific library (pico_cyw43_arch_none).

Now to run the code:

>
```bash
mkdir build && cd build
cmake ..

```

After it finishes excecuting, run:

>
```bash
make 

```

This will compile the file into an executable that we can copy to the pico_w and that should blink the led.

To do this, press the reset button on the board as you plug in the sub cable to the computer. this will open the pico folder where we can copy the elf file to the pico board and the inbuild LED will blink.

Thanks for reading.