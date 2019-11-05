cmake_minimum_required(VERSION 3.6)
project(rtk)


if(WIN32)
add_definitions(-DWIN32 -DWIN_DLL)
endif()

set(SRC_DIR ${PROJECT_SOURCE_DIR}/src)

file(GLOB_RECURSE C_SOURCES ${SRC_DIR}/*.c)
file(GLOB C_HEADERS ${SRC_DIR}/*.h)

add_definitions(-DENAGLO -DENAGAL -DENACMP -DEXOBS=3)
include_directories(${SRC_DIR})
add_library(${PROJECT_NAME} SHARED ${C_SOURCES} ${C_HEADERS})
if(WIN32)
target_link_libraries(${PROJECT_NAME} winmm ws2_32)
endif()
