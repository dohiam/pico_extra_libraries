#
# Example CMake file to use CMSIS-NN on Raspberry Pi Pico
# David Hamilton, 2023 This is free software (see LICENSE.txt in root directory), provided "AS IS" without any warranty, express or implied.
# 
#

set(myprojectname "nn-test")
set(CMSISNNDIR "${CMAKE_CURRENT_LIST_DIR}/../CMSIS-NN")
set (CMSISNNBIN "cmsis-nn-bin")
set(CMSIS_PATH "${CMAKE_CURRENT_LIST_DIR}/../CMSIS_5")
set(TARGET_CPU cortex-m0)


cmake_minimum_required(VERSION 3.12)
include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)
project(${myprojectname} C CXX ASM)
pico_sdk_init()
add_executable(${PROJECT_NAME} ${PROJECT_NAME}.c)
target_include_directories(${PROJECT_NAME} PRIVATE ${CMSISDSP}/Include ${CMSISCORE}/Include)
pico_add_extra_outputs(${PROJECT_NAME})
pico_enable_stdio_usb(${PROJECT_NAME} 1)
pico_enable_stdio_uart(${PROJECT_NAME} 0)

#project(CMSISNN)
add_library(cmsis-nn STATIC)
#avoid warning about new behavior
cmake_policy(SET CMP0076 NEW)
add_subdirectory(${CMSISNNDIR}/Source CMSISNNBIN)
target_compile_options(cmsis-nn PRIVATE -Ofast)
target_include_directories(cmsis-nn PUBLIC ${CMSISNNDIR}/Include)

target_link_libraries(${PROJECT_NAME} PRIVATE pico_stdlib cmsis-nn)

# if using pio and/or multicore
#pico_generate_pio_header(${PROJECT_NAME} ${CMAKE_CURRENT_LIST_DIR}/${PROJECT_NAME}.pio)
#target_link_libraries(${PROJECT_NAME} PRIVATE pico_stdlib hardware_pio pico_multicore)
