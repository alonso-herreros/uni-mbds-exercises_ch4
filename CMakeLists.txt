set(CMAKE_SYSTEM_VERSION 1)
cmake_minimum_required(VERSION 3.27)

set(USERPROFILE C:/Users/$ENV{USERNAME})

include(${USERPROFILE}/.cmake/NUCLEO-L152RE.cmake)

# project settings
project(Ch4_DevEnv C CXX ASM)
set(PROJ_PATH ${CMAKE_CURRENT_SOURCE_DIR})
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 11)

include_directories(
    "Core/Inc"
)

file(GLOB_RECURSE SOURCES
    "Core/*.*"
)

set(LINKER_SCRIPT ${PROJ_PATH}/STM32Cube/STM32L152RETX_FLASH.ld)

add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)
add_link_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)
add_link_options(-T ${LINKER_SCRIPT})

add_executable(${PROJECT_NAME} ${SOURCES} ${NUCLEO_L152RE_HAL_SOURCES} ${LINKER_SCRIPT})

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")
