cmake_minimum_required(VERSION 3.7)
project(cpp C CXX)

# 4 imported libraries. Note that there are dependencies between them.
add_library(fake_1 STATIC IMPORTED GLOBAL)
set_target_properties(fake_1
PROPERTIES
IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/imported_libs/libfake_1.a
)
add_library(fake_2 STATIC IMPORTED GLOBAL)
set_target_properties(fake_2
PROPERTIES
IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/imported_libs/libfake_2.a
INTERFACE_LINK_LIBRARIES "fake_1;"
)
add_library(fake_3 STATIC IMPORTED GLOBAL)
set_target_properties(fake_3
PROPERTIES
IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/imported_libs/libfake_3.a
INTERFACE_LINK_LIBRARIES "fake_1;fake_2"
)
add_library(fake_4 STATIC IMPORTED GLOBAL)
set_target_properties(fake_4
PROPERTIES
IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/imported_libs/libfake_4.a
INTERFACE_LINK_LIBRARIES "fake_3;fake_2"
)

add_subdirectory(lib_a)
add_subdirectory(lib_b)
add_subdirectory(exe)
