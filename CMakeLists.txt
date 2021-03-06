cmake_minimum_required (VERSION 2.8)

project (codeReason)
set(CMAKE_BUILD_TYPE Debug)

#lets use boost for smart pointers
set(BOOST_USE_MULTITHREADED ON)
if(WIN32)
    set(Boost_USE_STATIC_LIBS ON)
endif(WIN32)
find_package(Boost COMPONENTS thread system filesystem program_options date_time regex REQUIRED)
find_package(Threads REQUIRED)
find_package(Protobuf REQUIRED)

#find the place where VEX lives ... 
find_path(VEX_INCLUDE_DIR libvex.h REQUIRED PATHS ./vexTRUNK/pub NO_DEFAULT_PATH)

#define capstone's includes
set(CAPSTONE_SRC_DIR "./capstone-3.0.3/capstone-3.0.3")
set(PE-PARSE_SRC_DIR "./libs/pe-parse/parser-library")
set(ELFIO_INCLUDE_DIR "./libs/elfio")

include_directories(${VEX_INCLUDE_DIR})
include_directories(${Boost_INCLUDE_DIRS})
include_directories(${PROTOBUF_INCLUDE_DIR})
include_directories(${ELFIO_INCLUDE_DIR})

IF(WIN32)
    #on win32, vex is pre-built
    find_library(VEX_LIB VEX.lib HINTS ./build_for_windows/lib32 NO_DEFAULT_PATH)
ELSE(WIN32)
    find_library(VEX_LIB libvex.a HINTS ./vexTRUNK NO_DEFAULT_PATH)
    find_library(CAPSTONE_LIB libcapstone.a HINTS ${CAPSTONE_SRC_DIR} NO_DEFAULT_PATH)
    find_library(PE-PARSE_LIB libpe-parser-library.a HINTS ${PE-PARSE_SRC_DIR} NO_DEFAULT_PATH)
ENDIF(WIN32)

add_definitions(-D__i386__)
# gtest fix on osx
IF(APPLE)
add_definitions(-DGTEST_USE_OWN_TR1_TUPLE=1)
ENDIF(APPLE)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

set(gtest_force_shared_crt ON)

add_subdirectory(tools)
add_subdirectory(libs)
add_subdirectory(lua-5.3.0)
add_subdirectory(gtest-1.6.0)
