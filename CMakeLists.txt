#
# Copyright Barco N.V. All Rights Reserved.
#
message(STATUS "\n-- || Processing BatTzdata - starting")

################################################################################
# This CMakeLists.txt created by barco for tzdata is made for 
# Howard Hinant's date lib especially date-tz CMake target.
# The date-tz cmake target of the date lib depends tzdata in different ways:
# - At build time date-tz needs to know the path of tzdata folder on the 
#   target system.
# - At runtime the tzdata folder must be available under the path compiled into 
#   date-tz. That means it must be installed in some ways.
# 
# These requirements are similar to a shared library which location must be known
# at compile and runtime. 
#
# So in analogy to a CMakeLists.txt for a shared object this file takes care of:
#
# 1. Installing on target system.
# It installs tzdata onto a target system via the typical 
# configure/build/install procedure. This is espacially usefull on 
# Windows where the OS itself does not provide tzdata. 
# It can also be useful to install a newer or more comprehensive alternative
# version of tzdata than the one provided by the OS (Linux / mac os / iOS).
# E.g. On Linux OS tzdata content is located in /usr/share/zoneinfo.
# The install step of this cmake file will install this tzdata in 
# /usr/share/barco/tzdata (or in general under 
# ${CMAKE_INSTALL_PREFIX}/share/barco/tzdata). 
# 
# 2. Providing a CMake target for Howard Hinant's date lib (date-tz parser lib)
# This CMakeLists.txt file provides a CMake target "battzdata::battzdata"
# which provides as target property the compiler definition "INSTALL=..."
# The CMakeLists.txt of Howard Hinant's date lib was modified in that 
# the date-tz target depends on battzdata::battzdata target. The compile 
# definition property set on battzdata::battzdata target is derived 
# to date-tz target and overwrites the INSTALL compile definition set in the 
# date CMakeLists.txt file.
#

cmake_minimum_required( VERSION 3.14 )
project( BatTzdata VERSION 2021 )

add_library(battzdata INTERFACE)
add_library(battzdata::battzdata ALIAS battzdata )

include(GNUInstallDirs) # provides CMAKE_INSTALL_{INCLUDE|LIB|BIN|DATA}DIR variables

# Note: The install step will install tzdata folder under <install prefix>/share/barco/tzdata
target_compile_definitions(battzdata INTERFACE
    # Important for the standard use case where this package is installed on
    # target system and imported in a downstream CMake project via find_package
   INSTALL=$<INSTALL_PREFIX>/${CMAKE_INSTALL_DATADIR}/barco  
)

########################################################################
# Installation
########################################################################

#### Install tzdata folder ####
install(DIRECTORY tzdata
    DESTINATION ${CMAKE_INSTALL_DATADIR}/barco # <install prefix>/share/barco/tzdata
)

### Install CMake package config files ###

# prepare version file
include(CMakePackageConfigHelpers) # provides write_basic_package_version_file()
set( version_config "${CMAKE_CURRENT_BINARY_DIR}/BatTzdataConfigVersion.cmake" )
write_basic_package_version_file( "${version_config}"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion 
)

# Prepare BatTzdataTargets list
install( TARGETS battzdata
    EXPORT BatTzdataTargets
)
# Primary needed because CMakeLists.txt in date project also has an export step.
# Because it depends on battzdata it would complain if this step is missing here.
# Beside that we usually dont use / support the CMake export mechanism.
export( TARGETS battzdata NAMESPACE battzdata:: FILE BatTzdataTargets.cmake )

## Now Install all files

# Define config package target folder
if( WIN32 AND NOT CYGWIN)
    set( CONFIG_LOC CMake )
else( )
    set( CONFIG_LOC "${CMAKE_INSTALL_LIBDIR}/cmake/BatTzdata" )
endif( )


# Install cmake package target file
install(EXPORT BatTzdataTargets
    NAMESPACE battzdata::
    DESTINATION ${CONFIG_LOC}
)

# Install additional cmake package files (version file + 3rd dependencies file)
install(FILES 
    cmake/BatTzdataConfig.cmake
    ${version_config}
    DESTINATION ${CONFIG_LOC}
)
message(STATUS "|| Processing project BatTzdata - finished\n")
