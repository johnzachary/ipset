# -*- coding: utf-8 -*-
# ----------------------------------------------------------------------
# Copyright © 2011, RedJack, LLC.
#             2016, Cognize, LLC.
# All rights reserved.
#
# Please see the LICENSE.txt file in this distribution for license
# details.
# ----------------------------------------------------------------------

include_directories(../include)
link_directories(${CMAKE_CURRENT_BINARY_DIR}/../src)

#-----------------------------------------------------------------------
# Check for prerequisite libraries

find_package(PkgConfig)

pkg_check_modules(CHECK REQUIRED check)
include_directories(${CHECK_INCLUDE_DIRS})
link_directories(${CHECK_LIBRARY_DIRS})

#-----------------------------------------------------------------------
# Build the test cases

macro(make_test test_name)
    add_executable(${test_name} ${test_name}.c)
    target_link_libraries(${test_name} ${CHECK_LIBRARIES} libipset ${CMAKE_THREAD_LIBS_INIT})
    add_test(${test_name} ${test_name})
endmacro(make_test)

# Add calls to make_test for each .c test case in the tests/ directory.
make_test(test-assignment)
make_test(test-bdd)
make_test(test-ipmap)
make_test(test-ipset)
make_test(test-iterator)

#-----------------------------------------------------------------------
# Configure command line tests that use cram

find_package(PythonInterp)
if(PYTHON_EXECUTABLE)
    file(GLOB_RECURSE TESTS "${CMAKE_CURRENT_SOURCE_DIR}/*.t")
    foreach(TEST ${TESTS})
        get_filename_component(TEST_NAME "${TEST}" NAME_WE)
        add_test(${TEST_NAME}
                 ${CMAKE_COMMAND} -E chdir ${CMAKE_BINARY_DIR}
                 ${CMAKE_SOURCE_DIR}/tools/cram/ccram
                    --python ${PYTHON_EXECUTABLE}
                    --root ${CMAKE_SOURCE_DIR}
                    --tests ${TEST}
        )
    endforeach(TEST)
else(PYTHON_EXECUTABLE)
    message(WARNING "Unable to find python. Skipping cram tests.")
endif(PYTHON_EXECUTABLE)
