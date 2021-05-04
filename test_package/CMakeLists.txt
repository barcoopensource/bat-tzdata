#
# Copyright Barco N.V. All Rights Reserved.
#

### Testproject to validate correct installation of battzdata package. ###

# To test build this project:
# cmake -DCMAKE_PREFIX_PATH:STRING=<prefix path to where tzdata is installed > -S <source folder> -B <build folder>
#
# Search for package under $HOME/prj/install_prefix
# cmake -DCMAKE_PREFIX_PATH:STRING=~/prj/install_prefix -S . -B ~/prj/build_removeme
#
# Just search for package in standard paths
# cmake -S . -B ~/prj/build_removeme

cmake_minimum_required( VERSION 3.14 )
project( TestBatTzdata VERSION 1.0 )

# Search for package config files
find_package(BatTzdata 2021 REQUIRED CONFIG NO_CMAKE_PACKAGE_REGISTRY NO_CMAKE_SYSTEM_PACKAGE_REGISTRY)

# Check if target exists and if it has the right property set
get_target_property(tzdataDir battzdata::battzdata INTERFACE_COMPILE_DEFINITIONS)
message(STATUS "|| INTERFACE_COMPILE_DEFINITIONS  ${tzdataDir} \n")

# Final test, check if the path given by property does really exists.
string(REPLACE "INSTALL=" "" tzdataDir ${tzdataDir})
set(vFile "${tzdataDir}/version")
if (EXISTS ${vFile})
    message(STATUS "|| Found version file \"${vFile}\" in battzdata.\n")
else()
    message(FATAL_ERROR "|| Version file \"${vFile}\" in battzdata not found!\n")
endif()
