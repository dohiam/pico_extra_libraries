#
# Example CMake file to use TensorFlow Lite for Microcontrollers on Raspberry Pi Pico
# David Hamilton, 2023 This is free software (see LICENSE.txt in root directory), provided "AS IS" without any warranty, express or implied.
# 
#

set(myprojectname "tflite-test")
set(helloworld "examples/hello_world")
set(flatbuffers "third_party/flatbuffers/include")
set(kissfft "third_party/kissfft")
set(gemmlowp "third_party/gemmlowp")
set(ruy "third_party/ruy")

cmake_minimum_required(VERSION 3.5)

# These are needed due to issue described in: https://github.com/tensorflow/tflite-micro/issues/2078
execute_process(COMMAND cmake -E copy "../../tflite-micro/tensorflow/lite/array.h"  "../tensorflow/lite/array.h"  )
execute_process(COMMAND cmake -E copy "../../tflite-micro/tensorflow/lite/array.cc"  "../tensorflow/lite/array.cc"  )

# Grab all files to build
file(GLOB_RECURSE tf_src_files CONFIGURE_DEPENDS "signal/*.cc" "tensorflow/*.cc" "third_party/*.cc" "examples/hello_world/models/*.cc")

# Version of C++ required by TensorFlow
set(CMAKE_CXX_STANDARD 17)

include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)
project(${myprojectname} C CXX ASM)
pico_sdk_init()
add_executable(${PROJECT_NAME} ${PROJECT_NAME}.cpp ${tf_src_files})

target_include_directories(${PROJECT_NAME} PRIVATE 
   ${CMAKE_CURRENT_SOURCE_DIR} 
   ${CMAKE_CURRENT_SOURCE_DIR}/${helloworld} 
   ${CMAKE_CURRENT_SOURCE_DIR}/${flatbuffers} 
   ${CMAKE_CURRENT_SOURCE_DIR}/${gemmlowp}
   ${CMAKE_CURRENT_SOURCE_DIR}/${kissfft}
   ${CMAKE_CURRENT_SOURCE_DIR}/${ruy})
   
pico_add_extra_outputs(${PROJECT_NAME})
pico_enable_stdio_usb(${PROJECT_NAME} 1)
pico_enable_stdio_uart(${PROJECT_NAME} 0)

target_link_libraries(${PROJECT_NAME} PRIVATE pico_stdlib)

# if using pio and/or multicore
#pico_generate_pio_header(${PROJECT_NAME} ${CMAKE_CURRENT_LIST_DIR}/${PROJECT_NAME}.pio)
#target_link_libraries(${PROJECT_NAME} PRIVATE pico_stdlib hardware_pio pico_multicore)
