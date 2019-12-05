cmake_minimum_required (VERSION 3.7)
project (exe C CXX)
add_executable(exe ./main.cpp)
target_link_libraries(exe PRIVATE lib_b)

target_link_libraries(exe PUBLIC -Wl,-force_load fake_1)
target_link_libraries(exe PUBLIC -Wl,-force_load fake_2)
