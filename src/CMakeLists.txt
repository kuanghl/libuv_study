cmake_minimum_required(VERSION 3.18.4)

project(main)

add_definitions(-std=c++17)

set(EXECUTABLE_OUTPUT_PATH ${libuv_study_BINARY_DIR}/bin)

set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -o0 -Wall -g -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGES} -o3 -Wall")

add_executable(main main.c)
target_link_libraries(main ${libuv_study_BINARY_DIR}/libuv/libuv.so)