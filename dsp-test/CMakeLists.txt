#
# Example CMake file to use CMSIS-DSP on Raspberry Pi Pico
# David Hamilton, 2023 
# 
#
set(myprojectname "dsp-test")
set(CMSIS_DSP "${CMAKE_CURRENT_LIST_DIR}/../CMSIS-DSP")
set(CMSISCORE "${CMAKE_CURRENT_LIST_DIR}/../CMSIS_5/CMSIS/Core")
set(dsp_src_dir "${CMAKE_CURRENT_LIST_DIR}/../CMSIS-DSP/Source")
set(dsp_bin_dir "cms-dsp-bin")

cmake_minimum_required(VERSION 3.12)
include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)
project(${myprojectname} C CXX ASM)
pico_sdk_init()
add_executable(${PROJECT_NAME} ${PROJECT_NAME}.c)
target_include_directories(${PROJECT_NAME} PRIVATE  ${CMSISCORE}/Include)
pico_add_extra_outputs(${PROJECT_NAME})
pico_enable_stdio_usb(${PROJECT_NAME} 1)
pico_enable_stdio_uart(${PROJECT_NAME} 0)

add_subdirectory(${CMSIS_DSP}/Source ${dsp_bin_dir})

target_link_libraries(${PROJECT_NAME} PRIVATE CMSISDSP pico_stdlib)

# if using pio and/or multicore
#pico_generate_pio_header(${PROJECT_NAME} ${CMAKE_CURRENT_LIST_DIR}/${PROJECT_NAME}.pio)
#target_link_libraries(${PROJECT_NAME} PRIVATE pico_stdlib hardware_pio pico_multicore)
