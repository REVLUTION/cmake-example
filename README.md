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
