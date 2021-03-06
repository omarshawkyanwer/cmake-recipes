# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-03 LANGUAGES CXX)

# require C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# example library
add_library(sum_integers sum_integers.cpp)

# main code
add_executable(sum_up main.cpp)
target_link_libraries(sum_up sum_integers)

# we will use the network to fetch Google Test sources
# make it possible to disable unit tests when not on network
option(ENABLE_UNIT_TESTS "Enable unit tests" ON)
message(STATUS "Enable testing: ${ENABLE_UNIT_TESTS}")

if(ENABLE_UNIT_TESTS)
  include(ExternalProject)

  ExternalProject_Add(
    gtest
    PREFIX
      "${PROJECT_BINARY_DIR}/gtest"
    GIT_REPOSITORY
      https://github.com/google/googletest.git
    GIT_TAG
      release-1.8.0
    GIT_SHALLOW
      1
    CMAKE_ARGS
      -Dgtest_disable_pthreads=ON
    INSTALL_COMMAND
      "" # prevent gtest from installing itself system-wide
    BUILD_BYPRODUCTS
      # this is here otherwise Ninja will complain
      # that there is no rule to build libgtest.a
      ${PROJECT_BINARY_DIR}/gtest/src/gtest-build/googlemock/gtest/libgtest.a
    )

  add_executable(cpp_test test.cpp)

  target_include_directories(
    cpp_test
    PRIVATE
    ${PROJECT_BINARY_DIR}/gtest/src/gtest/googletest/include
    )

  target_link_libraries(
    cpp_test
    sum_integers
    ${PROJECT_BINARY_DIR}/gtest/src/gtest-build/googlemock/gtest/libgtest.a
    )

  # make sure that gtest is built before we build cpp_test
  add_dependencies(cpp_test gtest)

  enable_testing()
  add_test(google_test ${PROJECT_BINARY_DIR}/cpp_test)
endif()
