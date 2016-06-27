# -*- coding: utf-8 -*-
# ----------------------------------------------------------------------
# Copyright © 2011-2013, RedJack, LLC.
#             2016, Cognize, LLC.
# All rights reserved.
#
# Please see the LICENSE.txt file in this distribution for license
# details.
# ----------------------------------------------------------------------

cmake_minimum_required(VERSION 2.6)
set(PROJECT_NAME ipset)
set(RELEASE_DATE 2013-12-11)
project(${PROJECT_NAME})
enable_testing()
find_package(PkgConfig)

#-----------------------------------------------------------------------
# Set the current version number. See http://semver.org for details.

set(MAJOR_VERSION 1)
set(MINOR_VERSION 1)
set(PATCH_VERSION 1)
set(VERSION ${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION})

#-----------------------------------------------------------------------
# Force a build type decision

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release CACHE STRING
        "Choose the build type: None Debug Release RelWithDebInfo MinSizeRel"
        FORCE)
endif(NOT CMAKE_BUILD_TYPE)

#-----------------------------------------------------------------------
# Check for prerequisite libraries

pkg_check_modules(CORK REQUIRED "libcork >= 0.12.0")
include_directories(${CORK_INCLUDE_DIRS})
link_directories(${CORK_LIBRARY_DIRS})

find_package(Threads)

#-----------------------------------------------------------------------
# Set some options

set(CMAKE_INSTALL_LIBDIR lib CACHE STRING
    "The base name of the installation directory for libraries")

if(CMAKE_C_COMPILER_ID STREQUAL "GNU")
    add_definitions(-Wall -Werror)
elseif(CMAKE_C_COMPILER_ID STREQUAL "Clang")
    add_definitions(-Wall -Werror)
endif(CMAKE_C_COMPILER_ID STREQUAL "GNU")

if(NOT ETCDIR)
    if(CMAKE_INSTALL_PREFIX STREQUAL /usr)
        set(ETCDIR /etc CACHE STRING "Location of runtime config files.")
    else(CMAKE_INSTALL_PREFIX STREQUAL /usr)
        set(ETCDIR ${CMAKE_INSTALL_PREFIX}/etc CACHE STRING "Location of runtime config files.")
    endif(CMAKE_INSTALL_PREFIX STREQUAL /usr)
endif(NOT ETCDIR)

#-----------------------------------------------------------------------
# Include our subdirectories

add_subdirectory(docs)
add_subdirectory(include)
add_subdirectory(share)
add_subdirectory(src)
add_subdirectory(examples)
add_subdirectory(tests)
