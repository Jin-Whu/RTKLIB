cmake_minimum_required(VERSION 3.13)
project(rtk)

set(CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "Limited configurations" FORCE)

if(NOT CMAKE_BUILD_TYPE)
set(CMAKE_BUILD_TYPE Release CACHE STRING "Set build type to Debug or Release (default Release)" FORCE)
endif()

# option for build shared library
option(BUILD_SHARED_LIBS "Build shared libs" OFF)

# option for GNSS System
option(RTKLIB_ENABLE_GLO "Define if rtklib should build with support for GLONASS" ON)
option(RTKLIB_ENABLE_GAL "Define if rtklib should build with support for Galileo" ON)
option(RTKLIB_ENABLE_BDS "Define if rtklib should build with support for BDS" ON)
option(RTKLIB_ENABLE_QZS "Define if rtklib should build with support for QZSS" OFF)
option(RTKLIB_ENABLE_IRN "Define if rtklib should build with support for IRNSS" OFF)
option(RTKLIB_ENABLE_LEO "Define if rtklib should build with support for LEO" OFF)
option(RTKLIB_ENABLE_LEX "Define if rtklib should build with support QZSS LEX extension" OFF)

# GNSS observation set
set(RTKLIB_NFREQ 3 CACHE STRING "Set number of obs codes/frequencise (default 3)")
set(RTKLIB_NEXOBS 0 CACHE STRING "Set number of extended obs codes (default 0)")
set(RTKLIB_MAXOBS 64 CACHE STRING "Set max number of obs data in an epoch (default 64)")

# output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)

# windows runtime library configuration
if(MSVC)
option(MSVC_STATIC_RUNTIME "link static runtime libraries" ON)
if(MSVC_STATIC_RUNTIME)
    foreach(flag_var
            CMAKE_C_FLAGS_DEBUG
            CMAKE_C_FLAGS_RELEASE
    )
        if(${flag_var} MATCHES "/MD")
            string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
        endif()
    endforeach()
endif()
endif()

if(BUILD_SHARED_LIBS)
add_library(${PROJECT_NAME} SHARED "")
else()
add_library(${PROJECT_NAME} STATIC "")
endif()

# source files
target_sources(${PROJECT_NAME}
    PRIVATE
        ${PROJECT_SOURCE_DIR}/src/convgpx.c
        ${PROJECT_SOURCE_DIR}/src/convkml.c
        ${PROJECT_SOURCE_DIR}/src/convrnx.c
        ${PROJECT_SOURCE_DIR}/src/datum.c
        ${PROJECT_SOURCE_DIR}/src/download.c
        ${PROJECT_SOURCE_DIR}/src/ephemeris.c
        ${PROJECT_SOURCE_DIR}/src/geoid.c
        ${PROJECT_SOURCE_DIR}/src/gis.c
        ${PROJECT_SOURCE_DIR}/src/ionex.c
        ${PROJECT_SOURCE_DIR}/src/lambda.c
        ${PROJECT_SOURCE_DIR}/src/options.c
        ${PROJECT_SOURCE_DIR}/src/pntpos.c
        ${PROJECT_SOURCE_DIR}/src/postpos.c
        ${PROJECT_SOURCE_DIR}/src/ppp.c
        ${PROJECT_SOURCE_DIR}/src/ppp_ar.c
        ${PROJECT_SOURCE_DIR}/src/ppp_corr.c
        ${PROJECT_SOURCE_DIR}/src/preceph.c
        ${PROJECT_SOURCE_DIR}/src/qzslex.c
        ${PROJECT_SOURCE_DIR}/src/rcvraw.c
        ${PROJECT_SOURCE_DIR}/src/rinex.c
        ${PROJECT_SOURCE_DIR}/src/rtcm.c
        ${PROJECT_SOURCE_DIR}/src/rtcm2.c
        ${PROJECT_SOURCE_DIR}/src/rtcm3.c
        ${PROJECT_SOURCE_DIR}/src/rtcm3e.c
        ${PROJECT_SOURCE_DIR}/src/rtkcmn.c
        ${PROJECT_SOURCE_DIR}/src/rtkpos.c
        ${PROJECT_SOURCE_DIR}/src/rtksvr.c
        ${PROJECT_SOURCE_DIR}/src/sbas.c
        ${PROJECT_SOURCE_DIR}/src/solution.c
        ${PROJECT_SOURCE_DIR}/src/stream.c
        ${PROJECT_SOURCE_DIR}/src/tides.c
        ${PROJECT_SOURCE_DIR}/src/tle.c
        ${PROJECT_SOURCE_DIR}/src/rcv/binex.c
        ${PROJECT_SOURCE_DIR}/src/rcv/cmr.c
        ${PROJECT_SOURCE_DIR}/src/rcv/crescent.c
        ${PROJECT_SOURCE_DIR}/src/rcv/gw10.c
        ${PROJECT_SOURCE_DIR}/src/rcv/javad.c
        ${PROJECT_SOURCE_DIR}/src/rcv/novatel.c
        ${PROJECT_SOURCE_DIR}/src/rcv/nvs.c
        ${PROJECT_SOURCE_DIR}/src/rcv/rcvlex.c
        ${PROJECT_SOURCE_DIR}/src/rcv/rt17.c
        ${PROJECT_SOURCE_DIR}/src/rcv/septentrio.c
        ${PROJECT_SOURCE_DIR}/src/rcv/skytraq.c
        ${PROJECT_SOURCE_DIR}/src/rcv/ss2.c
        ${PROJECT_SOURCE_DIR}/src/rcv/tersus.c
        ${PROJECT_SOURCE_DIR}/src/rcv/ublox.c
    PUBLIC
        ${PROJECT_SOURCE_DIR}/src/rtklib.h
)

if(NOT BUILD_SHARED_LIBS)
if(WIN32)
target_link_libraries(${PROJECT_NAME} PUBLIC winmm ws2_32)
endif()
endif()

target_include_directories(${PROJECT_NAME} PUBLIC ${PROJECT_SOURCE_DIR}/src)

# rtklib complie definitions
if(RTKLIB_ENABLE_GLO)
target_compile_definitions(${PROJECT_NAME} PUBLIC -DENAGLO)
endif()

if(RTKLIB_ENABLE_GAL)
target_compile_definitions(${PROJECT_NAME} PUBLIC -DENAGAL)
endif()

if(RTKLIB_ENABLE_BDS)
target_compile_definitions(${PROJECT_NAME} PUBLIC -DENACMP)
endif()

if(RTKLIB_ENABLE_QZS)
target_compile_definitions(${PROJECT_NAME} PUBLIC -DENAQZS)
endif()

if(RTKLIB_ENABLE_IRN)
target_compile_definitions(${PROJECT_NAME} PUBLIC -DENAIRN)
endif()

target_compile_definitions(${PROJECT_NAME} PUBLIC -DNFREQ=${RTKLIB_NFREQ} -DNEXOBS=${RTKLIB_NEXOBS} -DMAXOBS=${RTKLIB_MAXOBS})

if(WIN32)
target_compile_definitions(${PROJECT_NAME} PUBLIC -DWIN32)
endif()

if(BUILD_SHARED_LIBS)
target_compile_definitions(${PROJECT_NAME} PRIVATE -DWIN_DLL)
endif()

# install
install(TARGETS rtk DESTINATION lib)
install(FILES ${PROJECT_SOURCE_DIR}/src/rtklib.h DESTINATION include)