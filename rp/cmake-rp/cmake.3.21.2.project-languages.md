```
LANGUAGES <language-name>...
```

1.
    1. Optional.
    2. Can also be specified without `LANGUAGES` keyword per the first, short signature.

2.
    1. Selects which programming languages are needed to build the project.
    2. Supported languages include `C`, `CXX` (i.e. C++), `CUDA`, `OBJC` (i.e. Objective-C), `OBJCXX`, `Fortran`, `HIP`, `ISPC`, and `ASM`.
    3. By default C and CXX are enabled if no language options are given.
    4. Specify language `NONE`, or use the `LANGUAGES` keyword and list no languages, to skip enabling any languages.

*New in version 3.8*: Added `CUDA` support.

*New in version 3.16*: Added `OBJC` and `OBJCXX` support.

*New in version 3.18*: Added `ISPC` support.

3. If enabling `ASM`, list it last so that CMake can check whether compilers for other languages like C work for assembly too.