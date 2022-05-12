cmake_minimum_required(VERSION 2.6)

if (POLICY CMP0053)
cmake_policy(SET CMP0053 OLD)
endif ()

if (POLICY CMP0054)
    cmake_policy(SET CMP0054 OLD)
endif ()

project(ucaandor C)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

find_package(Atcore REQUIRED)
find_package(Atutility REQUIRED) 
find_package(PkgConfig REQUIRED)

include(FindPackageHandleStandardArgs)
include(PkgConfigVars)

add_definitions("-std=c99 -Wall -fPIC")
add_definitions(-DG_LOG_DOMAIN="Uca-Andor")

set(ignoreMe "${ZERO_BASED_ROI}")
if (DEFINED ZERO_BASED_ROI)
    add_definitions(-DZERO_BASED_ROI -DZERO_BASED_AOI)
endif (DEFINED ZERO_BASED_ROI)

pkg_check_modules(GLIB2 glib-2.0 REQUIRED)

pkg_check_modules(UCA libuca>=2.1.0 REQUIRED)
pkg_check_variable(libuca plugindir)

include(Glib2MkEnums)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}
    ${ATCORE_INCLUDE_DIRS}
    ${ATUTILITY_INCLUDE_DIRS}
    ${UCA_INCLUDE_DIRS}
    ${GLIB2_INCLUDE_DIRS}
    ${GLIB2_INCLUDEDIR}/glib-2.0
    ${GLIB2_LIBDIR}/glib-2.0/include)

glib2_mkenums(uca-andor-enums
	uca-andor-enums
	uca-andor-camera.h)

link_directories(
    ${UCA_LIBRARY_DIRS}
    ${ANDOR_LIBRARY_DIRS})

add_library(ucaandor SHARED
	uca-andor-camera.c
	uca-andor-enums.c)

target_link_libraries(ucaandor
    ${UCA_LIBRARIES}
    ${ATUTILITY_LIBRARIES}
    ${ATCORE_LIBRARIES})

install(TARGETS ucaandor
        LIBRARY DESTINATION ${LIBUCA_PLUGINDIR}
        RUNTIME DESTINATION ${LIBUCA_PLUGINDIR})
