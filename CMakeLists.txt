cmake_minimum_required(VERSION 3.6)

project(lambda VERSION 0.1 LANGUAGES CXX)

add_library(ublib
  source/ublib/shared_string.cpp)
  
target_compile_features(ublib PUBLIC cxx_std_17)

target_include_directories(ublib
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
  PRIVATE
    source/ublib)

add_library(lambda
  source/lambda/parse_ast.cpp
  source/lambda/ast.cpp)

target_link_libraries(lambda ublib)

target_compile_features(lambda PUBLIC cxx_std_17)

target_include_directories(lambda
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
  PRIVATE
    source/lambda)

add_executable(lambdac
  source/main.cpp)

target_link_libraries(lambdac ublib lambda)

if(MSVC)
  # hack to deal with cmake automatically inserting /W3; taken from llvm
  string(REGEX REPLACE " /W[0-4]" "" CMAKE_C_FLAGS "${CMAKE_C_FLAGS}")
  string(REGEX REPLACE " /W[0-4]" "" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
endif()

function(add_options to)
  if(MSVC)
    target_compile_options(${to}
      PRIVATE
        /W4
      PUBLIC
        /permissive-)
  else()
    target_compile_options(${to}
      PRIVATE
        -Wall
        -Wextra
        -pedantic)
  endif()
endfunction()

add_options(ublib)
add_options(lambda)
add_options(lambdac)