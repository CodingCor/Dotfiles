cmake_minimum_required(VERSION 3.0)

# -------------
# SETUP PROJECT
# --------------

project(XXXXX)

# set build type as cache so it can be set from outside
set(CMAKE_BUILD_TYPE CACHE STRING "Build Type")
# if no type is spefially set from outside use Debug mode
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Debug")
endif()

# compile_commands.json is needed by Neovim LSP
set(CMAKE_EXPORT_COMPILE_COMMANDS true)

# --------------
# CREATE PROJECT
# --------------

list(APPEND SOURCE_FILES 
    main.cpp
)

# create the executable 
add_executable(${PROJECT_NAME} ${SOURCE_FILES})
target_include_directories(${PROJECT_NAME} PRIVATE include/)

# compiler flags
list(APPEND GCC_OPTIONS 
    -Wall
    -Werror
    -Wextra
    -Wconversion
)

# debug specific compiler flags
if("${CMAKE_BUILD_TYPE}" STREQUAL "Debug")
    list(APPEND GCC_OPTIONS
        -Og
    )
# release specific compiler flags
elseif("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
    list(APPEND GCC_OPTIONS
        -O2
    )
endif()


# -------------
# USER MESSAGES
# -------------
message("BUILD TYPE: ${CMAKE_BUILD_TYPE}")
message("Building with following flags: ${GCC_OPTIONS}")

# add compile options to target
target_compile_options(${PROJECT_NAME} PRIVATE ${GCC_OPTIONS})

# ---------
# POST WORK
# ---------

# clear the built type from cache
unset(CMAKE_BUILD_TYPE CACHE)
# link the compile_commands.json file to the project root
execute_process(
    COMMAND ${CMAKE_COMMAND} -E create_symlink
        ${CMAKE_BINARY_DIR}/compile_commands.json
        ${CMAKE_SOURCE_DIR}/compile_commands.json
)

