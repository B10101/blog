---
title: " Line Follower robot with PID control. Part 2: Working with the line tracking sensor"
date: 2024-02-17
tags: [Line Follower]
social_image: '/media/pico.jpg'
description: I have made the exciting decision to embark on a project; crafting a line follower robot utilizing the Pico W and C SDK and to try implement PID control on it. This endeavor merges my passion for robotics with the challenge of programming and hardware tinkering. The Pico W's compact yet powerful design, coupled with the versatility of the C SDK, offers an ideal platform for this venture. With its robust capabilities and extensive community support, I'm confident in my ability to bring this concept to fruition. Stay tuned as I delve into the intricacies of building and programming this line follower robot, documenting my progress and discoveries along the way.

---
## Introduction to the sensor
The sensor of choice is a 5 channel TCRT5000 line tracking sensor.

A 5-channel TCRT sensor typically refers to a sensor that consists of five individual TCRT (Transistor Coupled Reflective Optocoupler) modules. TCRT sensors are often used for proximity sensing and object detection applications. Here's a general explanation of how such a sensor might work:

![Rocket launch](/media/T.jpg)

## How a TCRT sensor works
1. Emitter: The TCRT sensor contains an infrared (IR) emitter that emits infrared light towards the surface where the object is supposed to be detected.
2. Receiver: Opposite the emitter, there's a phototransistor receiver. This receiver detects the infrared light that is reflected back from the surface.
3. Detection Principle: When there's no object in front of the sensor, the emitted IR light doesn't encounter any obstacle and does not reflect back to the receiver. Thus, the receiver receives minimal or no IR light.
4. Object Presence: When an object is placed in front of the sensor within its detection range, it reflects some of the emitted IR light back towards the sensor.
5. Output Signal: The phototransistor receiver detects the reflected IR light. The amount of light received depends on factors such as the distance, reflectivity, and surface characteristics of the object. This detected light intensity is then converted into an electrical signal.
6. Channel Sensitivity: A 5-channel TCRT sensor typically contains multiple receiver channels, each with varying sensitivity to different light intensities. This allows for more nuanced detection and differentiation between objects of different colors, reflectivity, or materials.
7. Output Processing: The electrical signal from the phototransistor is then processed by the sensor's electronics. This processing might involve amplification, filtering, or thresholding to convert the analog signal into a digital signal suitable for interfacing with microcontrollers or other digital systems.
8. Output Response: Based on the processed signal, the sensor generates an output that indicates the presence or absence of an object and, in some cases, additional information such as the intensity of the detected light or the channel from which the signal was received.

## Theory and code for my Implementation

In my case, I intend to use the sensor array to detect and differentiate between a black line and a white line. Since we have 5 outputs, And I plan to add PID control, I figured it is best if we use the sensor to calculate for error. The reading from the array will be stored in an array (hehe) the used to determine the error as per the table below.

| Sensor reading     | error |
| -------- | ------- |
| 10000  | 4    |
| 11000 | 3     |
| 01000    | 2    |
|01100   | 1|
|00100 | 0 |
|00110| -1|
|00010 | -2 |
|00011 | -3 |
|00001 | -4 |

The negative side just indicates error in the opposite direction.

From this, The error will now be an input to the pid which will perform a corrective measure. 

The code is as follows


> cpp
```cpp
#include "pico/stdlib.h"
#include "hardware/gpio.h"
#include "pico/cyw43_arch.h"

#define in1 6
#define in2 7
#define in3 8
#define in4 9
#define in5 10


void init(){
    stdio_init_all();
    gpio_init(in1);
    gpio_set_dir(in1, GPIO_IN);
    gpio_init(in2);
    gpio_set_dir(in2, GPIO_IN);
    gpio_init(in2);
    gpio_set_dir(in3, GPIO_IN);
    gpio_init(in3);
    gpio_set_dir(in4, GPIO_IN);
    gpio_init(in4);
    gpio_set_dir(in5, GPIO_IN);
    gpio_init(in5);


}

int sensor() {
    int LFSensor[5]={0,0,0,0,0};
    int error = 0;

    LFSensor[0] = gpio_get(in1);
    LFSensor[1] = gpio_get(in2);
    LFSensor[2] = gpio_get(in3);
    LFSensor[3] = gpio_get(in4);
    LFSensor[4] = gpio_get(in5);
    if((LFSensor[0] == 1)&&(LFSensor[1] == 0)&&(LFSensor[2] == 0)&&(LFSensor[3] == 0)&&(LFSensor[4] == 0)) error = 4; 
    if((LFSensor[0] == 1)&&(LFSensor[1] == 1)&&(LFSensor[2] == 0)&&(LFSensor[3] == 0)&&(LFSensor[4] == 0)) error = 3;
    if((LFSensor[0] == 0)&&(LFSensor[1] == 1)&&(LFSensor[2] == 0)&&(LFSensor[0] == 0)&&(LFSensor[4] == 0)) error = 2;
    if((LFSensor[0] == 0)&&(LFSensor[1] == 1)&&(LFSensor[2] == 1)&&(LFSensor[3] == 0)&&(LFSensor[4] == 0)) error = 1;
    if((LFSensor[0] == 0)&&(LFSensor[1] == 0)&&(LFSensor[2] == 1)&&(LFSensor[3] == 0)&&(LFSensor[4] == 0)) error = 0;
    if((LFSensor[0] == 0)&&(LFSensor[1] == 0)&&(LFSensor[2] == 1)&&(LFSensor[3] == 1)&&(LFSensor[4] == 0)) error = -1; 
    if((LFSensor[0] == 0)&&(LFSensor[1] == 0)&&(LFSensor[2] == 0)&&(LFSensor[3] == 1)&&(LFSensor[4] == 0)) error = -2;
    if((LFSensor[0] == 0)&&(LFSensor[1] == 0)&&(LFSensor[2] == 0)&&(LFSensor[3] == 1)&&(LFSensor[4] == 1)) error = -3;
    if((LFSensor[0] == 0)&&(LFSensor[1] == 0)&&(LFSensor[2] == 0)&&(LFSensor[3] == 0)&&(LFSensor[4] == 1)) error = -4;


    return error;

}

int main() {
    
    init();
   

    while(true){
        int error = sensor();
        
    }
}

```
And the CMakeLists.txt file is as follows

```CMakeLists
# set minimum version of cmake
cmake_minimum_required(VERSION 3.12)

#include build functions from the pico sdk
include(pico_sdk_import.cmake)
set(PICO_BOARD pico_w)


# set name of project And c/cpp standards

project(linetrackingsensor C CXX ASM)
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
    hardware_gpio
    pico_cyw43_arch_none
)

```

And with that, we have written the program for the sensor module. Next up, we will look at pwm and motor control. 


Thanks for reading.