cmake_minimum_required(VERSION 3.27)

include(GCC-AVR-CMakeLists.cmake)

#set Project name
PROJECT(SMART_LIGHT)

set(PPROJECT_DIR   ${CMAKE_CURRENT_SOURCE_DIR}/APPLICATION)
# set(STARTUP_SCRIPT ${CMAKE_CURRENT_SOURCE_DIR})
# set(LINKER_SCRIPT  ${CMAKE_CURRENT_SOURCE_DIR}/APP)

#######################################################
set(EXECUTABLE ${CMAKE_PROJECT_NAME}.elf)
#Set the Language Standard
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
#####################################################

set(HEADER_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/HAL
    ${CMAKE_CURRENT_SOURCE_DIR}/MCAL
    ${CMAKE_CURRENT_SOURCE_DIR}/LIB
    ${CMAKE_CURRENT_SOURCE_DIR}/_General_SpecificDrivers
)
set(PROJECT_INCLUDE_DIRECTORIES
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${PPROJECT_DIR}
)
########################################################
file(GLOB_RECURSE SRC_FILES 
    ${CMAKE_CURRENT_SOURCE_DIR}/HAL/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/MCAL/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/LIB/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/_General_SpecificDrivers/*.c
)
file(GLOB_RECURSE APP_SRC_FILES RELATIVE
    ${CMAKE_CURRENT_SOURCE_DIR}/*.c
    ${PPROJECT_DIR}/*.c
)

add_executable(${EXECUTABLE}
    ${SRC_FILES}
    ${APP_SRC_FILES}
    # ${STARTUP_SCRIPT}
    # ${LINKER_SCRIPT}
)

target_include_directories(${EXECUTABLE} PRIVATE
    ${HEADER_FILES}
    ${PROJECT_INCLUDE_DIRECTORIES}
)

target_compile_options(${EXECUTABLE} PRIVATE
    ${CPU_PARAMETERS}
    ${COMPILE_FEATURES}
)
target_link_options(${EXECUTABLE} PRIVATE
    ${LINKER_OPTIONS}
    -Wl,-Map=${CMAKE_PROJECT_NAME}.map
)
add_custom_target(FLASH ALL
    DEPENDS ${EXECUTABLE}
    COMMAND ${CMAKE_OBJCOPY} -j .text -j .data -O ihex ${EXECUTABLE} ${PROJECT_NAME}.hex
)