# How To Find Libraries

1.
    1. If your software uses external libraries (i.e. libraries not coming with your software), you don't know in advance where its headers and libraries will be located on the system where your software will be compiled.
    2. Depending on the location appropriate include directories and library search paths will have to be added to the compile commands.

2.
    1. CMake helps you with this by providing the find_package command.

3.
    1. This article briefly discusses how to use external libraries in a CMake project and then moves on to how to write your own find modules for libraries that don't already have one.

## 1. Using external libraries

1.
    1. CMake comes with numerous *modules* that aid in finding various well-known libraries and packages.
    2. You can get a listing of which modules your version of CMake supports by typing `cmake --help-module-list`, or by figuring out where your modules path is and looking inside on it.
    3. On Ubuntu Linux, for example, the module path is `/usr/share/cmake/Modules`.

2.
    1. Let's take the very popular *bzip2* library.
    2. There is a module named *FindBZip2.cmake*.
    3. Once you call this module with `find_package(BZip2)`, CMake will automatically fill in the values of various variables, which you can then use in your CMake script.
    4. For a list of the variables, look at the actual CMake module file, or you can type `cmake --help-module FindBZip2`.

```cmake
cmake_minimum_required(VERSION 2.8)
project(helloworld)
add_executable(helloworld hello.c)
find_package (BZip2)
if (BZIP2_FOUND)
    include_directories(${BZIP_INCLUDE_DIRS})
    target_link_libraries(helloworld, ${BZIP2_LIBRARIES})
endif (BZIP2_FOUND)
```

3.
    1. You can then use `cmake` and `make VERBOSE=1` to verify that the correct flags are being passed to the compiler and linker.
    2. You can also use a tool like 'ldd' or 'dependency walker' after compilation to verify what exact files `helloworld` has been linked with.

...