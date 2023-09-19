# Some Useful Third Party Libraries for the Raspberry Pi Pico

This project contains examples of using some third party libraries/frameworks that will run unchanged on the Raspberry Pi Pico, if one only has the right incantations in the CMakeLists.txt to correctly use them. The idea of this project is to provide those incantations in the form of a working example of using each library/framework.  After following the instructions to verify a working copy of each library/framework, one can then just copy the CMakeLists.txt file from that example to another directory in the same top level directory and just change the name of the project in this file. 

Rather than clone each library/framework, these instructions work with the latest version, unchanged (at least the latest version at the time this repository was created).  As long as these commands are issued in the top level directory of wherever this repository was cloned to, the CMakeLists.txt will find them using relative references, and one has the benefit of using the latest version.

The only prerequisites are git and the Raspberry Pi Pico SDK (with the PICO_SDK_PATH set appropriately).

Note: All the instructions assume you have done a cd into the top level directory of wherever this repository was cloned.

## CMSIS-DSP

This library provides not only some useful digital signal process functions, but it provides some machine learning algorithms as well (ones other than neural networks). Although this library performs much better with an Arm m4 or Arm m7 processor, it will work with the Arm m0 processor in the Raspberry Pi Pico. Although the Pico doesn't have a floating point processor, it does have a floating point library. Note that one will get much better performance using the fixed point capability of the CMS-DSP library than floating point.

Instructions:

```
git clone https://github.com/ARM-software/CMSIS-DSP.git
git clone https://github.com/ARM-software/CMSIS_5.git
cd dsp-test
cd build
cmake ..
make
```

Then one can copy the dsp-test.uf2 file to a Raspberry Pi Pico and bring up minicom to view the results to find out if it is working or not. Note that there is a five second delay at the start of the program to give one time to bring up a mincom session before running the test and printing the results.

If success is printed, then one can copy the CMakeLists.txt file, change the project name, and use it with custom code that uses both the Raspberry Pi Pico and the latest CMSIS-DSP library that was cloned.

### References:

1. Library documentation:
   https://arm-software.github.io/CMSIS_5/DSP/html/index.html
2. Implement classical machine learning
   with Arm CMSIS-DSP libraries: 
   https://developer.arm.com/-/media/Arm%20Developer%20Community/PDF/Machine%20Learning/Machine%20Learning%20PDF%20Tutorials/Implement%20classical%20ML%20with%20Arm%20CMSIS-DSP%20libraries.pdf?revision=c1ab2e5a-ce8a-4457-ac67-7b79f57aed9f



## CMSIS-NN

This library provides a collection of neural network functionality. 

Instructions (note that the second git command is unnecessary if it was already done successfully as part of getting CMSIS-DSP :

```
git clone https://github.com/ARM-software/CMSIS-NN.git
git clone https://github.com/ARM-software/CMSIS_5.git
cd nn-test
cd build
cmake ..
make
```

Then one can copy the nn-test.uf2 file to a Raspberry Pi Pico and bring up minicom to view the results to find out if it is working or not. Note that there is a five second delay at the start of the program to give one time to bring up a mincom session before running the test and printing the results.

If success is printed, then one can copy the CMakeLists.txt file, change the project name, and use it with custom code that uses both the Raspberry Pi Pico and the latest CMSIS-NN library that was cloned.

### References:

1. Library documentation:
   https://arm-software.github.io/CMSIS_5/NN/html/index.html
   
   



## FreeRTOS

FreeRTOS is a library of functions to enable multi-tasking on microcontrollers. It works on a wide variety of hardware, with somewhat different capabilities depending on the hardware; it uses hardware capabilities such as timers and interrupts, so it is a bit more complicated than the CMSIS DSP or NN libraries which are just number crunching libraries.

Instructions (although it can be obtained from github, for stability the long term build is used here):

```
navigate to: https://freertos.org/lts-libraries.html, click Download LTS 202210 Libraries
cd freertos-test
cd build
cmake ..
make
```

Then one can copy the freertos-test.uf2 file to a Raspberry Pi Pico and bring up minicom to view the results to find out if it is working or not. Note that there is a five second delay at the start of the program to give one time to bring up a mincom session before running the test and printing the results. If there is a bunch of output showing things being sent and received, with semaphores being triggered and output from a task on Core 1 all printing out intermingled, then success is achieved. Because this is a bit more complicated, there is a bit more than one file to be copied to a new directory when starting a new project based on this. The following should all be copied:

1. CMakeLists.txt
2. library.cmake
3. the include directory, including FreeRTOSConfig.h



## TensorFlow Lite

Tensorflow Lite is a neural network framework, somewhat like CMSIS NN on the device side, but TensorFlow has much more functionality in the form of offline training of models and a lot of pre-existing models, examples, etc. TensorFlow Lite for Microcontrollers is a very trimmed version of the full TensorFlow and even smaller than the regular TensorFlow Lite, but it will take quite a bit of the available memory on a Raspberry Pi Pico.

Unlike the previous libraries, this TensorFlow provides a command to copy what is needed from the source directories to a destination folder. In the instructions below, this is used to copy the right files directly into the tflite-test directory:

```
git clone https://github.com/tensorflow/tflite-micro.git
cd tflite-micro
python ./tensorflow/lite/micro/tools/project_generation/create_tflm_tree.py -e hello_world ../tflite-test
cd ../tflite-test
cd build
cmake ..
make
```

Note: due to the issue described in https://github.com/tensorflow/tflite-micro/issues/2078, there is a manual copy of some files inside CMakeLists.txt. This may not be needed in more recent versions of tensorflow.

The make command in the above instructions will take quite a while, but when it is done, assuming it is successful, then one can copy the tflite-test.uf2 file to a Raspberry Pi Pico and bring up minicom to view the results to find out if it is working or not. Note that there is a five second delay at the start of the program to give one time to bring up a mincom session before running the test and printing the results.

It should run very quickly (after the 5 second delay) and if it is successful, it will print a line saying "ALL TESTS PASSED". Once you know this is working, you can either:

1. Repeat the python command from the tflite-micro directory to generate files to a new directory, and copy the CMakeLists.txt file from the tflite-test directory, modifying the CMakeLists file to as needed for the custom project.
2. Copy the signal, tensorflow, and third_party folders from the tflite-test directory along with the CMakeLists.txt file. 

The first choice above will allow some additional options in how tensorflow is generated, like working with a different built-in example, but the second choice will be a bit faster.

### References

There is a lot of documentation online from Google and third parties about using Tensorflow Lite on microcontrollers:

1. Google's main page from TensorFlow Lite:
   https://www.tensorflow.org/lite/guide

2. Google's main page for the microcontroller specific version:
   https://www.tensorflow.org/lite/microcontrollers

3. An example of using it on a RP 2040 board:
   https://blog.tensorflow.org/2021/09/TinyML-Audio-for-everyone.html

   

