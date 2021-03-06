# @@@LICENSE
#
#      Copyright (c) 2007-2013 LG Electronics, Inc.
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
#
# LICENSE@@@

cmake_minimum_required(VERSION 2.8.7)

project(configurator CXX)

include(webOS/webOS)
webos_modules_init(1 0 0 QUALIFIER RC2)
webos_component(2 0 0)

webos_add_compiler_flags(ALL "-DMOJ_LINUX")
webos_add_compiler_flags(ALL "-std=c++11")

webos_machine_dep()

include_directories(src)

include(FindPkgConfig)

pkg_check_modules(GLIB2 REQUIRED glib-2.0)
include_directories(${GLIB2_INCLUDE_DIRS})
webos_add_compiler_flags(ALL ${GLIB2_CFLAGS})

pkg_check_modules(LUNASERVICE REQUIRED luna-service2)
include_directories(${LUNASERVICE_INCLUDE_DIRS})
webos_add_compiler_flags(ALL ${LUNASERVICE_CFLAGS})

pkg_check_modules(CJSON cjson)
include_directories(${CJSON_INCLUDE_DIRS})
webos_add_compiler_flags(ALL ${CJSON_CFLAGS})

pkg_check_modules(DB8 REQUIRED db8)
include_directories(${DB8_INCLUDE_DIRS})
webos_add_compiler_flags(ALL ${DB8_CFLAGS_OTHER})

find_library(PTHREAD pthread REQUIRED)

file(GLOB SOURCE_FILES src/*.cpp)
add_executable(configurator ${SOURCE_FILES} )

target_link_libraries(configurator
                   	-L. 
                    ${DB8_LDFLAGS}
                   	${GLIB2_LDFLAGS}
                   	${LUNASERVICE_LDFLAGS}
                   	${CJSON_LDFLAGS}
                        ${PTHREAD}
                   )

webos_configure_header_files(src)
webos_build_daemon(NAME configurator LAUNCH files/launch)
webos_build_system_bus_files()
