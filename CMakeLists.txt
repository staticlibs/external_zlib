# Copyright 2015, alex at staticlibs.net
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required ( VERSION 2.8.12 )

# project
project ( external_zlib CXX C )
set ( ${PROJECT_NAME}_STATICLIB_VERSION 1.2.7-4 )
set ( ${PROJECT_NAME}_DESCRIPTION "Zlib library build for Staticlibs" )
set ( ${PROJECT_NAME}_URL https://github.com/staticlibs/external_zlib.git )

# options, use set ( OPTNAME ON CACHE BOOL "") in parent to override
option ( ${PROJECT_NAME}_ENABLE_PRECONFIGURATION "Skip configure-step checks and use preconfigured config headers" OFF )

# standalone build
if ( NOT DEFINED CMAKE_LIBRARY_OUTPUT_DIRECTORY )
    set ( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR} )
endif ( )

# zlib subproject
add_subdirectory ( ${CMAKE_CURRENT_LIST_DIR}/resources/zlib_cmake ${CMAKE_CURRENT_BINARY_DIR}/zlib )
set_target_properties ( z PROPERTIES FOLDER "deps" )
add_custom_target ( ${PROJECT_NAME} DEPENDS z )

# pkg-config
set ( ${PROJECT_NAME}_MODULE zlib )
set ( ${PROJECT_NAME}_PC_CFLAGS "-I${CMAKE_CURRENT_BINARY_DIR}/zlib_install/include" )
set ( ${PROJECT_NAME}_PC_LIBS "-L${CMAKE_LIBRARY_OUTPUT_DIRECTORY} -lz" )
configure_file ( ${CMAKE_CURRENT_LIST_DIR}/resources/pkg-config.in 
        ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/pkgconfig/zlib.pc )

if ( DEFINED z_MSVC_ALT_CRUNTIME )
    set ( ${PROJECT_NAME}_MODULE zlib_altcr )
    set ( ${PROJECT_NAME}_PC_LIBS "-L${CMAKE_LIBRARY_OUTPUT_DIRECTORY} -lz_altcr" )
    configure_file ( ${CMAKE_CURRENT_LIST_DIR}/resources/pkg-config.in
        ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/pkgconfig/zlib_altcr.pc )
endif ( )
