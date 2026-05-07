cmake_minimum_required(VERSION 3.20)

project(LaserOSCHOP LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(LASERDOCKLIB_BUILD_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../laserdocklib/build-macos" CACHE PATH "Existing laserdocklib build directory")
set(LASERDOCKLIB_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../laserdocklib/lib/include" CACHE PATH "laserdocklib headers directory")
set(LASERDOCKLIB_LIBRARY "${LASERDOCKLIB_BUILD_DIR}/lib/liblaserdocklib.dylib" CACHE FILEPATH "laserdocklib shared library")

if(NOT APPLE)
    message(FATAL_ERROR "This CMake build is currently intended for macOS. Continue using the Visual Studio project on Windows.")
endif()

if(NOT EXISTS "${LASERDOCKLIB_INCLUDE_DIR}/laserdocklib/LaserdockDevice.h")
    message(FATAL_ERROR "laserdocklib headers were not found. Set LASERDOCKLIB_INCLUDE_DIR to a checkout of laserdocklib/lib/include.")
endif()

if(NOT EXISTS "${LASERDOCKLIB_LIBRARY}")
    message(FATAL_ERROR "laserdocklib library was not found. Build ../laserdocklib first or set LASERDOCKLIB_LIBRARY to liblaserdocklib.dylib.")
endif()

add_library(laserdocklib SHARED IMPORTED GLOBAL)
set_target_properties(laserdocklib PROPERTIES
    IMPORTED_LOCATION "${LASERDOCKLIB_LIBRARY}"
    INTERFACE_INCLUDE_DIRECTORIES "${LASERDOCKLIB_INCLUDE_DIR}"
)

add_library(LaserOSCHOP SHARED
    CPlusPlus/CHOP_Buffer.cpp
    CPlusPlus/dllmain.cpp
    CPlusPlus/Laser_OS_CHOP.cpp
    CPlusPlus/pch.cpp
)

target_include_directories(LaserOSCHOP PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/CPlusPlus
)

target_link_libraries(LaserOSCHOP PRIVATE laserdocklib)

set_target_properties(LaserOSCHOP PROPERTIES
    OUTPUT_NAME "Laser OS CHOP"
    PREFIX ""
)

target_compile_options(LaserOSCHOP PRIVATE
    -Wall
    -Wextra
)