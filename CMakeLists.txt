# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
# 
#   http://www.apache.org/licenses/LICENSE-2.0
# 
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

cmake_minimum_required (VERSION 3.2)
cmake_policy(SET CMP0012 NEW)
cmake_policy(SET CMP0042 NEW)

project (Celix C CXX)

include(GNUInstallDirs)                                                                                                                                                                             

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")

SET(CMAKE_BUILD_TYPE "Debug")

# see https://public.kitware.com/Bug/view.php?id=15696
IF (${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION} EQUAL 3.3 AND ${CMAKE_GENERATOR} STREQUAL "Unix Makefiles")
    message( FATAL_ERROR "Building Celix using CMake 3.3 and makefiles is not supported due to a bug in the Makefile Generator (see Bug 15696). Please change the used CMake version - both, CMake 3.2 and CMake 3.4 are working fine. Or use a different generator (e.g. Ninja)." )
ENDIF()

IF (ANDROID)
    SET(CMAKE_C_FLAGS "-D_GNU_SOURCE -std=gnu99 -Wall ${CMAKE_C_FLAGS}")
ENDIF()
IF(UNIX AND NOT ANDROID)
    SET(CMAKE_C_FLAGS "-D_GNU_SOURCE -std=gnu99 -Wall -Werror -fPIC ${CMAKE_C_FLAGS}")
    SET(CMAKE_CXX_FLAGS "-std=c++11 ${CMAKE_CXX_FLAGS}") #TODO enable -Wall -Werror -> warning in cpputest
    SET(CMAKE_C_FLAGS_DEBUG "-g -DDEBUG")
    SET(CMAKE_CXX_FLAGS_DEBUG "-g -DDEBUG")
ENDIF()
IF(UNIX AND NOT APPLE) 
	SET(CMAKE_C_FLAGS "-pthread ${CMAKE_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "-pthread ${CMAKE_CXX_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "-pthread ${CMAKE_EXE_LINKER_FLAGS}")
ENDIF()
IF(WIN32)
	SET(CMAKE_C_FLAGS "-D_CRT_SECURE_NO_WARNINGS ${CMAKE_C_FLAGS}")
ENDIF()
IF(APPLE)
    set(CMAKE_MACOSX_RPATH 1)
ENDIF()

# Set version for the framework package/release
SET(CELIX_MAJOR "1")
SET(CELIX_MINOR "0")
SET(CELIX_MICRO "0")
SET(CELIX_QUALIFIER "")

option(ENABLE_TESTING "Enables unit/bundle testing" FALSE)

if (ENABLE_TESTING)
	enable_testing()
endif()


include(CMakeCelix)
include(UseDoxygen)  

# Default bundle version
set(DEFAULT_VERSION 1.0.0)

## New sub project must use a buildoption to be able to enable/disable the project using the CMake Editor
## Sub projects depending on another sub project automatically enable these dependencies
## For this to work, the order off "add_subdirectory" has to be correct (ie backwards)
add_subdirectory(config_admin)
add_subdirectory(device_access)
add_subdirectory(deployment_admin)
add_subdirectory(remote_services)
add_subdirectory(remote_shell)
add_subdirectory(shell_bonjour)
add_subdirectory(shell_tui)
add_subdirectory(shell)
add_subdirectory(log_writer)
add_subdirectory(log_service)

#add_subdirectory(event_admin)# event_admin is unstable
add_subdirectory(dependency_manager)
add_subdirectory(dependency_manager_cxx)
#add_subdirectory(scr) scr still needs APR

add_subdirectory(launcher)
add_subdirectory(framework)
add_subdirectory(utils)
add_subdirectory(dfi)

#Example as last, because some example will check if underlining options are enabled
add_subdirectory(examples)
