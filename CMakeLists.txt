cmake_minimum_required(VERSION 3.16)

if(MSVC AND DEFINED ENV{VCPKG_ROOT} AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
  set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake" CACHE STRING "")
endif()

project(LMAGI
  VERSION 0.0.1
  DESCRIPTION "LMAGI Game"
  LANGUAGES CXX)

if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
  set(LMAGI_STANDALONE ON)
endif()

if(LMAGI_STANDALONE)
  set_property(GLOBAL PROPERTY PREDEFINED_TARGETS_FOLDER "CMakeTargets")
  set_property(GLOBAL PROPERTY USE_FOLDERS ON)
  set(CMAKE_CXX_STANDARD 17)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
  set(CMAKE_CXX_EXTENSIONS OFF)
  set(CMAKE_DISABLE_IN_SOURCE_BUILD ON)
  set(CMAKE_DISABLE_SOURCE_CHANGES ON)
  set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
  set(CMAKE_DEBUG_POSTFIX "d")

  if(NOT WIN32)
    set(CMAKE_LINK_WHAT_YOU_USE ON)
  endif()

  if(NOT MSVC)
    find_program(LMAGI_CCACHE_PROGRAM ccache)
    if(LMAGI_CCACHE_PROGRAM)
      set(CMAKE_C_COMPILER_LAUNCHER "${LMAGI_CCACHE_PROGRAM}")
      set(CMAKE_CXX_COMPILER_LAUNCHER "${LMAGI_CCACHE_PROGRAM}")
      message(STATUS "Using ccache: ${LMAGI_CCACHE_PROGRAM}")
    endif()
  endif()
endif()

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
include(BuildType)
include(CompilerType)
include(GetRepoInfo)

#this does not seem to work ;<
#list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../engine/install/cmake/LMAGIE")

set(LMAGIE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../engine/install/cmake/LMAGIE")

find_package(LMAGIE REQUIRED)

#this should be handled by find_packed, but for whatever reason it isn't so here you go...
set(LMAGIE_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../engine/install/include")

set(CMAKEINSTINCLUDROOT ${CMAKE_CURRENT_SOURCE_DIR}/install)

add_subdirectory(Source)

set(LMAGI_ENABLE_TESTS ${LMAGI_STANDALONE} CACHE STRING "Enable unit tests")

if(LMAGI_ENABLE_TESTS)
  message(STATUS "Tests are not integrated yet")
# include(CTest)
# enable_testing()
# add_subdirectory( tests )
endif()