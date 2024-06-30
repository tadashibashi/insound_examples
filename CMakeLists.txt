cmake_minimum_required(VERSION 3.20)
project(insound_examples)

set(CMAKE_POLICY_DEFAULT_CMP0077 NEW CACHE STRING "")
cmake_policy(SET CMP0077 NEW)

# C++ Standard
set(CMAKE_CXX_STANDARD_REQUIRED 17)
if (NOT DEFINED CMAKE_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD 17)
endif()

# No C++20 modules in this project
if (CMAKE_CXX_STANDARD GREATER_EQUAL 20)
    set(CMAKE_CXX_SCAN_FOR_MODULES OFF)
endif()

# Add ./cmake to CMake modules path
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

add_subdirectory(insound)
add_subdirectory(examples)

# Support for Sublime LSP-clangd and VSCode clangd
if (CMAKE_EXPORT_COMPILE_COMMANDS AND EXISTS ${CMAKE_SOURCE_DIR}/build)
    cmake_language(DEFER DIRECTORY ${CMAKE_SOURCE_DIR} CALL _end_of_configure())
    function(_end_of_configure)
        execute_process(COMMAND ${CMAKE_COMMAND} -E copy
            ${CMAKE_BINARY_DIR}/compile_commands.json ${CMAKE_SOURCE_DIR}/build/compile_commands.json)
    endfunction()
endif()
