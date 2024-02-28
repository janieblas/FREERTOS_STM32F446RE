cmake_minimum_required(VERSION 3.15)

# Project name
project(blink C ASM)

set(EXECUTABLE ${CMAKE_PROJECT_NAME})

set(CPU_PARAMETERS
    -mcpu=cortex-m4
    -mthumb
    #-nostdlib
    --specs=nano.specs 
    -mfloat-abi=hard 
    -mfpu=fpv4-sp-d16
    -g
    -O0
    -Wl,--no-warn-rwx-segments)

set(STARTUP_SCRIPT ${CMAKE_CURRENT_SOURCE_DIR}/Startup/startup.c )
set(LINKER_SCRIPT ${CMAKE_CURRENT_SOURCE_DIR}/Drivers/Linker_script/linker_script.ld)

# Headers files
set(INCS
    Inc
    Drivers/CMSIS/Core 
    Drivers/CMSIS/Device/Include
    Drivers/FreeRTOS/include
    Drivers/stm32f4xx_hal_driver/Inc
)

# Source files
set(SRCS
    Src/main.c
    Src/syscalls.c
    Src/usart.c
    Src/stm32f4xx_it.c
    Src/stm32f4xx_hal_timebase_tim.c    
    Startup/startup.c
    Drivers/CMSIS/Device/Source/system_stm32f4xx.c
    #HAL_Drivers
    Drivers/stm32f4xx_hal_driver/Src/stm32f4xx_hal.c 
    Drivers/stm32f4xx_hal_driver/Src/stm32f4xx_hal_cortex.c
    Drivers/stm32f4xx_hal_driver/Src/stm32f4xx_hal_tim.c
    Drivers/stm32f4xx_hal_driver/Src/stm32f4xx_hal_tim_ex.c
    Drivers/stm32f4xx_hal_driver/Src/stm32f4xx_hal_dma.c
    Drivers/stm32f4xx_hal_driver/Src/stm32f4xx_hal_rcc.c
    Drivers/stm32f4xx_hal_driver/Src/stm32f4xx_hal_rcc_ex.c
    Drivers/stm32f4xx_hal_driver/Src/stm32f4xx_hal_gpio.c
    #FREERTOS
    Drivers/FreeRTOS/list.c
    Drivers/FreeRTOS/queue.c
    Drivers/FreeRTOS/tasks.c
    Drivers/FreeRTOS/timers.c
    Drivers/FreeRTOS/port.c
    Drivers/FreeRTOS/heap_4.c
    Drivers/FreeRTOS/event_groups.c
    Drivers/FreeRTOS/stream_buffer.c

)

# Executable files
add_executable(${EXECUTABLE}
    ${SRCS}
    ${STARTUP_SCRIPT})

# Embedded macros(defines)
target_compile_definitions(${EXECUTABLE} PRIVATE
    STM32F446xx
    USE_HAL_DRIVER)

# Add header directories (AFTER add_executable !!)
target_include_directories(${EXECUTABLE} PRIVATE
    ${INCS})

target_compile_options(${EXECUTABLE} PRIVATE
    ${CPU_PARAMETERS})

target_link_options(${EXECUTABLE} PRIVATE
    -T ${LINKER_SCRIPT}
    -u _printf_float
    ${CPU_PARAMETERS})

