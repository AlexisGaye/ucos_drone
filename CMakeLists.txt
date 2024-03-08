cmake_minimum_required(VERSION 3.10)

project(Drone C ASM)

# output compile_commands.json
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

#language
enable_language(C ASM)
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)

# add startup file
set(STARTUP_FILE ${CMAKE_CURRENT_SOURCE_DIR}/CORE/startup_stm32f401xe.s)
# set(OS_CPU_A_FILE ${CMAKE_CURRENT_SOURCE_DIR}/os/os_cpu_a.s)

# add .h
set(LINKER_SCRIPT ${CMAKE_CURRENT_SOURCE_DIR}/CORE/STM32F401RETx_FLASH.ld)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/CORE)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/FWLIB/inc)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/HARDWARE/inc)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/SYSTEM/inc)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/USER/inc)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/os/inc)

# add .c
file(GLOB SOURCE 
"${CMAKE_CURRENT_SOURCE_DIR}/USER/src/*.c" 
"${CMAKE_CURRENT_SOURCE_DIR}/FWLIB/src/*.c"
"${CMAKE_CURRENT_SOURCE_DIR}/HARDWARE/src/*.c"
"${CMAKE_CURRENT_SOURCE_DIR}/SYSTEM/src/*.c"
"${CMAKE_CURRENT_SOURCE_DIR}/os/src/*.c" 
)
set(SOURCE_FILES ${SOURCE})

# set compiler and linker
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)                                                        
set(CMAKE_LINKER arm-none-eabi-ld)

#set compiler option
set(CMAKE_C_FLAGS "-mthumb -mcpu=cortex-m4 -std=c99 -g -Os \
    -ffunction-sections -fdata-sections -Wall -Wextra \
    -Wno-self-assign -Wno-unused-parameter -Wno-empty-body")
set(CMAKE_ASM_FLAGS "-mthumb -mcpu=cortex-m4 -g -x assembler-with-cpp")

# set linker option
set(CMAKE_EXE_LINKER_FLAGS "-mthumb -mabi=aapcs -mcpu=cortex-m4 \
    --specs=nano.specs --specs=nosys.specs -u _printf_float -u _scanf_float \
    -Wl,--gc-sections -Wl,-Map=${PROJECT_NAME}.map -T${LINKER_SCRIPT}")


# ${OS_CPU_A_FILE}
add_executable(Drone ${SOURCE_FILES} ${STARTUP_FILE} )
target_link_libraries(Drone -Wl,-Map=${PROJECT_NAME}.map -Wl,--gc-sections -Wl,--print-memory-usage -Wl,-lc -lm)
