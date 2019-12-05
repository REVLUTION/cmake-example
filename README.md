This is an example C++ project using CMake that illustrates an issue with passing linker flags to be applied
to specific libraries. What we really want is to be able to specify that certain external libraries are to be
force-linked.

We have 4 fake imported static libraries that we want to link (that is pre-built 3rd party libraries). These are
declared in the main `CMakeLists.txt`. There are dependencies between them which are declared via
`set_target_properties` on each library. Since there are dependencies between them they should appear on the link line
in a specific order.

We have 2 libraries, `lib_a` and `lib_b`, that make use of some of these external libraries. They also require them to
be force linked. So they contain lines like:

```
target_link_libraries(lib_a PUBLIC -Wl,-force_load fake_4)
```

However, when we compile we see that some, but not all, libraries get the `-Wl,-force_load` applied. It seems CMake sees
that as a separate library it can place anywhere rather than a modifier specific libraries.

Other things we've tried:

1. Use `target_link_libraries(lib_a PUBLIC "-Wl,-force_load fake_4")`. This doesn't work because cmake doesn't expand
   `fake_4` to it's declared library path.
2. Use `target_link_libraries(lib_a PUBLIC "-Wl,-force_load $<TARGET_PROPERTY:fake_4,IMPORTED_LOCATION>")`. This doesn't
   work because cmake doesn't know that this refers to the `fake_4` we declared in the root `CMakeLists.txt` so it
   ignores the dependencies between our 4 fake imported libraries.
3. Use `target_link_options`: this doesn't apply the link options to a specific library but rather adds them in an
   arbitrary position on the link line.
4. We can't put the `-Wl,-force_load` properties on the libraries themselves as we have other executables that use them
   but do not require a force link.

To see what happens do the following from this directory:

```
$ mkdir build
$ cd build
$ cmake ..
$ VERBOSE=1 make
```

the link will fail as `libfake_X.a` aren't really static libraries but you can also see that the libraries are out of
order and not correctly force-linked though we've said they must be. Specifically, I get the following liker line:

```
Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++   -isysroot
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.15.sdk
-mmacosx-version-min=10.14 -Wl,-search_paths_first -Wl,-headerpad_max_install_names  CMakeFiles/exe.dir/main.cpp.o  -o
exe ../lib_b/liblib_b.a -Wl,-force_load ../../imported_libs/libfake_1.a -Wl,-force_load ../../imported_libs/libfake_2.a
../lib_a/liblib_a.a -Wl,-force_load ../../imported_libs/libfake_4.a ../../imported_libs/libfake_3.a
../../imported_libs/libfake_2.a ../../imported_libs/libfake_1.a
```

here we can see:

1. `libfake_3.a` is not preceeded by a `-Wl,-force_load` though `lib_a/CMakeLists.txt` said it was necessary.
2. The libraries are arranged in proper order on the link line however. But if you try experiments (1) or (2) above that
   is no longer the case.


