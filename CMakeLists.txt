cmake_minimum_required(VERSION 3.10.2)
project(Cleaver)

set(CMAKE_CXX_STANDARD 17)

include(FetchContent)
set(_itk_build_testing ${BUILD_TESTING})
#set(BUILD_TESTING OFF)
if (CMAKE_CXX_COMPILER_IS_GNUCXX)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-maybe-uninitialized")
endif()
set(_itk_build_shared ${BUILD_SHARED_LIBS})
set(BUILD_SHARED_LIBS OFF)
set(cleaver_GIT_REPOSITORY "https://github.com/SCIInstitute/Cleaver2")
set(cleaver_GIT_TAG "02ba7b5fda5578b348a1dd54fb1660ef6cc7faf2")
FetchContent_Declare(
  cleaver_lib
  GIT_REPOSITORY ${cleaver_GIT_REPOSITORY}
  GIT_TAG ${cleaver_GIT_TAG})
FetchContent_GetProperties(cleaver_lib)
if(NOT cleaver_lib_POPULATED)
  FetchContent_Populate(cleaver_lib)
  add_subdirectory(${cleaver_lib_SOURCE_DIR}/src ${cleaver_lib_BINARY_DIR})
endif()
# Debugging / development, symlink Cleaver2 into the source tree
#add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/Cleaver2/src)
set(BUILD_TESTING ${_itk_build_testing})
set(BUILD_SHARED_LIBS ${_itk_build_shared})

set(Cleaver_LIBRARIES cleaver)
set(Cleaver_INCLUDE_DIRS
  ${Cleaver_SOURCE_DIR}/include
  ${cleaver_lib_SOURCE_DIR}/src/lib
  )

if(NOT ITK_SOURCE_DIR)
  find_package(ITK REQUIRED)
  list(APPEND CMAKE_MODULE_PATH ${ITK_CMAKE_DIR})
  include(ITKModuleExternal)
else()
  set(ITK_DIR ${CMAKE_BINARY_DIR})
  itk_module_impl()
endif()

itk_module_target(cleaver NO_INSTALL)
