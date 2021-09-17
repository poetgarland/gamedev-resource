# How to structure your project

1.
    1. The following information is biased.
    2. But in a good way, I think.
    3. I'm going to tell you how to structure the directories in your project.
    4. This is based on convention, but will help you:

        1. Easily read other projects following the same patterns,
        2. Avoid a pattern that causes conflicts,
        3. Keep from muddling and complicating your build.
    
2.
    1. First, this is what your files should look like when you start if your project is creatively called `project`, with a library called `lib`, and a executable called `app`:

```
- project
    - .gitignore
    - README.md
    - LICENCE.md
    - CMakeLists.txt
    - cmake
        - FindSomeLib.cmake
        - something_else.cmake
    - include
        - project
            - lib.hpp
    - src
        - CMakeLists.txt
        - lib.cpp
    - apps
        - CMakeLists.txt
        - app.cpp
    - tests
        - CMakeLists.txt
        - testlib.cpp
    - docs
        - CMakeLists.txt
    - extern
        - googletest
    - scripts
        - helper.py
```
