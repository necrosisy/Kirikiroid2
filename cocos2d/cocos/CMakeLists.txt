#/****************************************************************************
# Copyright (c) 2013 cocos2d-x.org
# Copyright (c) 2014 martell malone
# Copyright (c) 2015-2017 Chukong Technologies Inc.
#
# http://www.cocos2d-x.org
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:

# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.

# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.
# ****************************************************************************/

# this CMakeLists is to generate Cocos2d-x Engine Library
# build libcocos2d
# build luacocos2d if BUILD_LUA_LIBS=ON
# build jscocos2d if BUILD_JS_LIBS=ON

# The version number
set(COCOS2D_X_VERSION 3.17.1)

set(COCOS_CORE_LIB cocos2d)

include(2d/CMakeLists.txt)
include(3d/CMakeLists.txt)
include(platform/CMakeLists.txt)
include(physics/CMakeLists.txt)
include(physics3d/CMakeLists.txt)
include(math/CMakeLists.txt)
include(navmesh/CMakeLists.txt)
include(renderer/CMakeLists.txt)
include(vr/CMakeLists.txt)
include(base/CMakeLists.txt)
include(deprecated/CMakeLists.txt)
include(ui/CMakeLists.txt)
include(network/CMakeLists.txt)
include(audio/CMakeLists.txt)
include(storage/CMakeLists.txt)

# default value for cocos2dx extensions modules build
option(BUILD_EDITOR_COCOSBUILDER "Build editor support for cocosbuilder" ON)
option(BUILD_EDITOR_COCOSTUDIO "Build editor support for cocostudio" ON)
option(BUILD_EDITOR_SPINE "Build editor support for spine" ON)
option(BUILD_EXTENSIONS "Build extension library" ON)

if(BUILD_EDITOR_COCOSBUILDER)
    include(editor-support/cocosbuilder/CMakeLists.txt)
    set(COCOS_EDITOR_SUPPORT_SRC ${COCOS_EDITOR_SUPPORT_SRC} ${COCOS_CCB_SRC} ${COCOS_CCB_HEADER})
endif(BUILD_EDITOR_COCOSBUILDER)

if(BUILD_EDITOR_COCOSTUDIO)
    include(editor-support/cocostudio/CMakeLists.txt)
    set(COCOS_EDITOR_SUPPORT_SRC ${COCOS_EDITOR_SUPPORT_SRC} ${COCOS_CS_SRC} ${COCOS_CS_HEADER})
endif(BUILD_EDITOR_COCOSTUDIO)

if(BUILD_EDITOR_SPINE)
    include(editor-support/spine/CMakeLists.txt)
    set(COCOS_EDITOR_SUPPORT_SRC ${COCOS_EDITOR_SUPPORT_SRC} ${COCOS_SPINE_SRC} ${COCOS_SPINE_HEADER})
endif(BUILD_EDITOR_SPINE)

if(BUILD_EXTENSIONS)
    include(../extensions/CMakeLists.txt)
endif(BUILD_EXTENSIONS)

set(COCOS_HEADER cocos2d.h
    ${COCOS_2D_HEADER}
    ${COCOS_3D_HEADER}
    ${COCOS_PLATFORM_HEADER}
    ${COCOS_PHYSICS_HEADER}
    ${COCOS_PHYSICS3D_HEADER}
    ${COCOS_MATH_HEADER}
    ${COCOS_NAVMESH_HEADER}
    ${COCOS_RENDERER_HEADER}
    ${COCOS_VR_HEADER}
    ${COCOS_BASE_HEADER}
    ${COCOS_AUDIO_HEADER}
    ${COCOS_UI_HEADER}
    ${COCOS_DEPRECATED_HEADER}
    ${COCOS_NETWORK_HEADER}
    ${COCOS_EDITOR_SUPPORT_HEADER}
    ${COCOS_EXTENSIONS_HEADER}
    ${COCOS_STORAGE_HEADER}
    )

set(COCOS_SRC cocos2d.cpp
    ${COCOS_2D_SRC}
    ${COCOS_3D_SRC}
    ${COCOS_PLATFORM_SRC}
    ${COCOS_PHYSICS_SRC}
    ${COCOS_PHYSICS3D_SRC}
    ${COCOS_MATH_SRC}
    ${COCOS_NAVMESH_SRC}
    ${COCOS_RENDERER_SRC}
    ${COCOS_VR_SRC}
    ${COCOS_BASE_SRC}
    ${COCOS_AUDIO_SRC}
    ${COCOS_UI_SRC}
    ${COCOS_DEPRECATED_SRC}
    ${COCOS_NETWORK_SRC}
    ${COCOS_EDITOR_SUPPORT_SRC}
    ${COCOS_EXTENSIONS_SRC}
    ${COCOS_STORAGE_SRC}
    )

list(APPEND COCOS_SRC ${COCOS_HEADER})

add_library(cocos2d ${COCOS_SRC})

# use external libs
add_subdirectory(${COCOS2DX_ROOT_PATH}/external ${ENGINE_BINARY_PATH}/external)
target_link_libraries(cocos2d external turbojpep_static)

# add base macro define and compile options
use_cocos2dx_compile_define(cocos2d)
use_cocos2dx_compile_options(cocos2d)

# use all platform related system libs
use_cocos2dx_libs_depend(cocos2d)

target_include_directories(cocos2d
    PUBLIC ${COCOS2DX_ROOT_PATH}
    PUBLIC ${COCOS2DX_ROOT_PATH}/cocos
    PUBLIC ${COCOS2DX_ROOT_PATH}/extensions
    PUBLIC ${COCOS2DX_ROOT_PATH}/cocos/platform
    INTERFACE ${COCOS2DX_ROOT_PATH}/cocos/base
    INTERFACE ${COCOS2DX_ROOT_PATH}/cocos/editor-support
    INTERFACE ${COCOS2DX_ROOT_PATH}/cocos/audio/include
    INTERFACE ${COCOS2DX_ROOT_PATH}/cocos/platform/${PLATFORM_FOLDER}
)

set_target_properties(cocos2d
                      PROPERTIES
                      ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
                      LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
                      VERSION "${COCOS2D_X_VERSION}"
                      FOLDER "Internal"
                      )

## Lua bindings lib
if(BUILD_LUA_LIBS)
    add_subdirectory(${COCOS2DX_ROOT_PATH}/cocos/scripting/lua-bindings ${ENGINE_BINARY_PATH}/cocos/lua-bindings)
endif()
## JS bindings lib
if(BUILD_JS_LIBS)
    add_subdirectory(${COCOS2DX_ROOT_PATH}/cocos/scripting/js-bindings ${ENGINE_BINARY_PATH}/cocos/js-bindings)
endif()

if(XCODE OR VS)
    cocos_mark_code_files("cocos2d")
endif()

if(WINDOWS)
    # precompiled header. Compilation time speedup ~4x.
    target_sources(cocos2d PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/precheader.cpp")
    set_target_properties(cocos2d PROPERTIES COMPILE_FLAGS "/Yuprecheader.h /FIprecheader.h")
    set_source_files_properties("${CMAKE_CURRENT_SOURCE_DIR}/precheader.cpp" PROPERTIES COMPILE_FLAGS "/Ycprecheader.h")
    # compile c as c++. needed for precompiled header
    set_source_files_properties(${COCOS_SPINE_SRC} base/ccFPSImages.c PROPERTIES LANGUAGE CXX)
endif()
