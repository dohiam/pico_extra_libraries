#
# Example CMake file to use FreeRTOS on Raspberry Pi Pico
# David Hamilton, 2023 This is free software (see LICENSE.txt in root directory), provided "AS IS" without any warranty, express or implied.
# 
#

set(myprojectname "freertos-test")
set(FREERTOS_KERNEL_PATH "${CMAKE_CURRENT_LIST_DIR}/../FreeRTOS-LTS/FreeRTOS/FreeRTOS-Kernel")	
set(RP2040_DIR "${CMAKE_CURRENT_LIST_DIR}/../FreeRTOS-LTS/FreeRTOS/FreeRTOS-Kernel/portable/ThirdParty/GCC/RP2040")
set(freertos_bin_dir "freertos_bin")


cmake_minimum_required(VERSION 3.12)
include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)
project(${myprojectname} C CXX ASM)
pico_sdk_init()
add_executable(${PROJECT_NAME} ${PROJECT_NAME}.c)
target_include_directories(${PROJECT_NAME} PRIVATE "./include" ${FREERTOS_KERNEL_PATH}/include ${RP2040_DIR}/include)
pico_add_extra_outputs(${PROJECT_NAME})
pico_enable_stdio_usb(${PROJECT_NAME} 1)
pico_enable_stdio_uart(${PROJECT_NAME} 0)

# defines library called FreeRTOS-Kernel-Core
cmake_policy(SET CMP0076 NEW)
include(library.cmake)
#add_subdirectory(${RP2040_DIR} ${freertos_bin_dir})

#target_link_libraries(${PROJECT_NAME} PRIVATE pico_stdlib pico_multicore)
target_link_libraries(${PROJECT_NAME} PRIVATE FreeRTOS-Kernel FreeRTOS-Kernel-Core FreeRTOS-Kernel-Heap4 pico_stdlib pico_multicore )

# if using pio and/or multicore
#pico_generate_pio_header(${PROJECT_NAME} ${CMAKE_CURRENT_LIST_DIR}/${PROJECT_NAME}.pio)
#target_link_libraries(${PROJECT_NAME} PRIVATE pico_stdlib hardware_pio pico_multicore)
