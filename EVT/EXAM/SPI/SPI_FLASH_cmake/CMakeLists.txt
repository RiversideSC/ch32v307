cmake_minimum_required(VERSION 3.15)

if(NOT DEFINED WCH32V307_SDK_ROOT)
    message(FATAL_ERROR "WCH32V307_SDK_ROOT not defined.
    Use -DWCH32V103_SDK_ROOT=/path/to/ch32v103/EVT/EXAM/SRC
    ")
endif()

if(NOT DEFINED RISCV32_TOOLCHAIN_PATH)
    message(FATAL_ERROR "RISCV32_TOOLCHAIN_PATH not defined.
    Use -DRISCV32_TOOLCHAIN_PATH=/path/to/riscv-none-embed-gcc/bin
    ")
endif()

set(CMAKE_TOOLCHAIN_FILE toolchain-riscv-gnu.cmake)

project(spi_flash VERSION 0.0.1 LANGUAGES C CXX ASM)

string(TIMESTAMP BUILD_TIME %Y%m%d-%H%M%S)
configure_file(config.h.in config.h)

set(INC_LIST
    .
    ${PROJECT_BINARY_DIR}
    ${WCH32V307_SDK_ROOT}/Core
    ${WCH32V307_SDK_ROOT}/Debug
    ${WCH32V307_SDK_ROOT}/Peripheral/inc
)

set(SRC_LIST
    ${WCH32V307_SDK_ROOT}/Startup/startup_ch32v30x_D8C.S
    ${WCH32V307_SDK_ROOT}/Core/core_riscv.c
    ${WCH32V307_SDK_ROOT}/Peripheral/src/ch32v30x_rcc.c
    ${WCH32V307_SDK_ROOT}/Peripheral/src/ch32v30x_spi.c
    ${WCH32V307_SDK_ROOT}/Peripheral/src/ch32v30x_usart.c
    ${WCH32V307_SDK_ROOT}/Peripheral/src/ch32v30x_gpio.c
    ${WCH32V307_SDK_ROOT}/Peripheral/src/ch32v30x_dbgmcu.c
    ${WCH32V307_SDK_ROOT}/Debug/debug.c
    main.c
    ch32v30x_it.c
    system_ch32v30x.c
)

add_executable(${PROJECT_NAME}
    ${SRC_LIST}
)

target_include_directories(${PROJECT_NAME} PUBLIC
    ${INC_LIST}
)

target_link_options(${PROJECT_NAME} PUBLIC
    -Wl,-T ${WCH32V307_SDK_ROOT}/Ld/Link.ld
    -Wl,-Map=${PROJECT_NAME}.map
)

set_target_properties(${PROJECT_NAME}
    PROPERTIES OUTPUT_NAME ${PROJECT_NAME}.elf
)
