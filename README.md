This is an example C++ project using CMake that issustrates an issue with passing linker flags to be applied
to specific libraries. What we really want is to be able to specify that certain external libraries are to be
force-linked.

Run the following to generate the make files:
```
cmake -H. -B/Users/derekseiple/Downloads/example/build -DCMAKE_BUILD_TYPE=Debug
```

This is not intended to actually compile. Instead we are concerned with the Make files that are generated. Specifically the linker command that is generated to [link.txt](build/exe/CMakeFiles/exe.dir/link.txt).

Note that this will create the following:
```
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++  -g -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.15.sdk -Wl,-search_paths_first -Wl,-headerpad_max_install_names  CMakeFiles/exe.dir/main.cpp.o  -o exe ../lib_b/liblib_b.a -Wl,-force_load /path/to/libfake_1.a -Wl,-force_load /path/to/libfake_2.a ../lib_a/liblib_a.a -Wl,-force_load /path/to/libfake_3.a /path/to/libfake_4.a 
```

We would expect `-Wl,-force_load` to show up in front of all 4 of the fake libraries. It does show up in front of  `/path/to/libfake_1.a` and `/path/to/libfake_2.a` which were specified in the [exe CMake](exe/CMakeLists.txt). But it only shows up once for the fake libraries specified in the [lib_a CMake](lib_a/CMakeLists.txt).