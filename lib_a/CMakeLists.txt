cmake_minimum_required (VERSION 3.7)
project (lib_a C CXX)
add_library(lib_a STATIC ./lib_a.cpp ./lib_a.h)
target_include_directories(lib_a PUBLIC .)

target_link_libraries(lib_a PUBLIC -Wl,-force_load fake_4)
target_link_libraries(lib_a PUBLIC -Wl,-force_load fake_3)
