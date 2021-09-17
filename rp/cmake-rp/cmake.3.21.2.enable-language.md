# `enable_language`

1.
    1. Enable a language (CXX/C/OBJC/OBJCXX/Fortran/etc)

    ```cmake
    enable_language(<lang> [OPTIONAL])
    ```

2.
    1. Enables support for the named language in CMake.
    2. This is the same as the `project()` command but does not create any of the extra variables that are created by the project command.
    3. Example languages are `CXX`, `C`, `CUDA`, `OBJC`, `OBJCXX`, `Fortran`, `HIP`, `ISPC`, and `ASM`.

*New in version 3.8*: Added `CUDA` support.

*New in version 3.16*: Added `OBJC` and `OBJCXX` support.

*New in version 3.18*: Added `ISPC` support.

*New in version 3.21*: Added `HIP` support.

3.
    1. If enabling `ASM`, enable it last so that CMake can check whether compilers for other languages like `C` work for assembly too.

4.
    1. This command must be called in file scope, not in a function call.
    2. Furthermore, it must be called in the highest directory common to all targets using the named language directly for compiling sources or indirectly through link dependencies.
    3. It is simplest to enable all needed language in the top-level directory of a project.

5.
    1. The `OPTIONAL` keyword is a placeholder for future implementation and does not currently work.
    2. Instead you can use the `CheckLanguage` module to verify support before enabling.