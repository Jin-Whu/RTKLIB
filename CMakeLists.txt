cmake_minimum_required(VERSION 3.6)
project(rtk)

set(CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "Limited configurations" FORCE)

if(NOT CMAKE_BUILD_TYPE)
set(CMAKE_BUILD_TYPE Release CACHE STRING "Set build type to Debug or Release (default Release)" FORCE)
endif()

option(BUILD_SHARED_LIBS "Build shared libs" OFF)


if(WIN32)
add_definitions(-DWIN32)
endif()

if(BUILD_SHARED_LIBS)
add_definitions(-DWIN_DLL)
endif()

# option for GNSS System
option(RTKLIB_ENABLE_GLO "Define if rtklib should build with support for GLONASS" ON)
option(RTKLIB_ENABLE_GAL "Define if rtklib should build with support for Galileo" ON)
option(RTKLIB_ENABLE_BDS "Define if rtklib should build with support for BDS" ON)
option(RTKLIB_ENABLE_QZS "Define if rtklib should build with support for QZSS" OFF)
option(RTKLIB_ENABLE_IRN "Define if rtklib should build with support for IRNSS" OFF)
option(RTKLIB_ENABLE_LEO "Define if rtklib should build with support for LEO" OFF)
option(RTKLIB_ENABLE_LEX "Define if rtklib should build with support QZSS LEX extension" ON)

if(RTKLIB_ENABLE_GLO)
add_definitions(-DENAGLO)
endif()

if(RTKLIB_ENABLE_GAL)
add_definitions(-DENAGAL)
endif()

if(RTKLIB_ENABLE_BDS)
add_definitions(-DENACMP)
endif()

if(RTKLIB_ENABLE_QZS)
add_definitions(-DENAQZS)
endif()

if(RTKLIB_ENABLE_IRN)
add_definitions(-DENAIRN)
endif()

# GNSS observation set
set(RTKLIB_NFREQ 3 CACHE STRING "Set number of obs codes/frequencise (default 3)")
set(RTKLIB_NEXOBS 0 CACHE STRING "Set number of extended obs codes (default 0)")
set(RTKLIB_MAXOBS 64 CACHE STRING "Set max number of obs data in an epoch (default 64)")

if(RTKLIB_NFREQ)
add_definitions(-DNFREQ=${RTKLIB_NFREQ})
endif()

if(RTKLIB_NEXOBS)
add_definitions(-DNEXOBS=${RTKLIB_NEXOBS})
endif()

if(RTKLIB_MAXOBS)
add_definitions(-DMAXOBS=${RTKLIB_MAXOBS})
endif()

# output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)

# source files
set(SRC_DIR ${PROJECT_SOURCE_DIR}/src)
file(GLOB_RECURSE C_SOURCES ${SRC_DIR}/*.c)
file(GLOB C_HEADERS ${SRC_DIR}/*.h)

# include directories
include_directories(${SRC_DIR})

if(BUILD_SHARED_LIBS)
add_library(${PROJECT_NAME} SHARED ${C_SOURCES} ${C_HEADERS})
if(WIN32)
target_link_libraries(${PROJECT_NAME} winmm ws2_32)
endif()
else()
add_library(${PROJECT_NAME} STATIC ${C_SOURCES} ${C_HEADERS})
endif()
