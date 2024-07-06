cmake_minimum_required(VERSION 3.20)

if (APPLE AND NOT EMSCRIPTEN AND NOT ANDROID)
    project(insound_examples C CXX OBJC OBJCXX)
else()
    project(insound_examples C CXX)
endif()



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

# Platform versions
set(CMAKE_XCODE_ATTRIBUTE_IPHONEOS_DEPLOYMENT_TARGET "12.5.7")

set(INSOUND_EXAMPLES_ROOT_DIR ${CMAKE_CURRENT_SOURCE_DIR} CACHE STRING "Insound examples repo root directory")

# Add ./cmake to CMake modules path
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

if (NOT TARGET SDL2::SDL2-static) # user can provide their own SDL2 submodule, but we may want to enforce a minimum version
    include(FetchContent)
    FetchContent_Declare(SDL2
        GIT_REPOSITORY https://github.com/libsdl-org/SDL.git
        GIT_TAG        92fe3b19c868ad062c323dde2cfc9d8b4bfdd785
    )

    set(SDL_STATIC             ON)
    set(SDL_SHARED             OFF)
    set(SDL2_DISABLE_UNINSTALL ON )
    set(SDL2_DISABLE_INSTALL   ON )
    set(SDL_TEST               OFF)
    set(SDL_ASSEMBLY           ON)
    set(SDL_ARMNEON            ON)
    set(SDL_PTHREADS           NOT ${INSOUND_NO_PTHREAD})

    FetchContent_MakeAvailable(SDL2)
endif()

set(INSOUND_BACKEND_SDL2 OFF CACHE BOOL "" FORCE)
set(INSOUND_USE_CLIENT_SDL2 OFF CACHE BOOL "" FORCE)
set(INSOUND_BACKEND_SDL3 OFF CACHE BOOL "" FORCE)
set(INSOUND_BACKEND_PORTAUDIO OFF CACHE BOOL "" FORCE)
set(INSOUND_DECODE_GME OFF CACHE BOOL "" FORCE)
add_subdirectory(insound)

set (INSOUND_SANDBOX_USE_SDL3 OFF CACHE BOOL "" FORCE)
add_subdirectory(examples)

# Support for Sublime LSP-clangd and VSCode clangd
if (CMAKE_EXPORT_COMPILE_COMMANDS AND EXISTS ${CMAKE_SOURCE_DIR}/build)
    cmake_language(DEFER DIRECTORY ${CMAKE_SOURCE_DIR} CALL _end_of_configure())
    function(_end_of_configure)
        execute_process(COMMAND ${CMAKE_COMMAND} -E copy
            ${CMAKE_BINARY_DIR}/compile_commands.json ${CMAKE_SOURCE_DIR}/build/compile_commands.json)
    endfunction()
endif()
