# set minimum cmake version
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

# project name and language
project(recipe-06 LANGUAGES CXX)

# require C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# in case Git is not available, we default to "unknown"
set(GIT_HASH "unknown")

# find Git and if available set GIT_HASH variable
find_package(Git QUIET)
if(GIT_FOUND)
  execute_process(
    COMMAND ${GIT_EXECUTABLE} --no-pager show -s --pretty=format:%h -n 1
    OUTPUT_VARIABLE GIT_HASH
    OUTPUT_STRIP_TRAILING_WHITESPACE
    ERROR_QUIET
    WORKING_DIRECTORY
      ${PROJECT_SOURCE_DIR}
    )
endif()

message(STATUS "Git hash is ${GIT_HASH}")

# generate file version.h based on version.h.in
configure_file(
  version.h.in
  generated/version.h
  @ONLY
  )

# example code
add_executable(example example.cpp)

# needs to find the generated header file
target_include_directories(
  example
  PRIVATE
    ${PROJECT_BINARY_DIR}/generated
  )
