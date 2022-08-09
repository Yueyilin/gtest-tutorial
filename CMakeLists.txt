cmake_minimum_required(VERSION 2.8.12)

project(gflags_demo)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)
## set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)
set(EXECUTABLE_OUTPUT_PATH  ${PROJECT_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

add_subdirectory(include)

add_executable(gtestexe1 src/gtest_demo1.cc)
target_link_libraries(gtestexe1 gtest)

add_executable(gtestexe2 src/gtest_demo2.cc)
target_link_libraries(gtestexe2 gtest)