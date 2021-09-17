# Using SDL2 with CMake

1.
    1. SDL2 is the newest version of the Simple Directmedia Layer API.
    2. It can be used together with CMake to build a cross platform multimedia application.
    3. In this blog post I will describe the necessary steps to use SDL2 with CMake on both Linux (Ubuntu 17.04) and Windows.

2.
    1. You need to create a CMakeLists.txt file for your project that includes `SDL2` and compiles a simple program.

3.
    1. You can use the following code:

```cmake
cmake_minimum_required(VERSION 3.7)

project(SDL2Test)

find_package(SDL2 REQUIRED)
include_directories(${SDL2_INCLUDE_DIRS})

add_executable(SDL2Test Main.cpp)
target_link_libraries(SDL2Test ${SDL2_LIBRARIES})
```

## 1. Linux

1.
    1. Setting up SDL2 with CMake under Ubuntu Linux is pretty easy.
    2. All you need to do is install the required dependencies first.

```sh
$ sudo apt install cmake libsdl2-dev g++
```

2.
    1. Now you can use CMake to generate your Makefiles and build your project.
    2. To include SDL2 headers you just use `#include "SDL.h"`.
    3. The correct include paths have been set up by cmake.
    4. For Linux nothing else is required.

## 2. Windows

1.
    1. For Windows you have to download the development package `SDL2-devel-2.0.5-VC.zip` and extract it to some location on your hard disk.

2.
    1. You can create Visual Studio project files with the CMake GUI under windows but when you hit configure it will fail because it will not find the SDL2 Library.

3.
    1. In the configuration window you will see a `SDL2_DIR` variable.
    2. You will have to point that to the location where you extracted the SDL2 development package.

4.
    1. Before you can reconfigure you also have to create a file `sdl2-config.cmake` where you extracted the development libraries and put the following content:

```cmake
set(SDL2_INCLUDE_DIRS "${CMAKE_CURRENT_LIST_DIR}/include")

# Support both 32 and 64 bit builds
if (${CMAKE_SIZEOF_VOID_P} MATCHES 8)
    set(SDL2_LIBRARIES "${CMAKE_CURRENT_LIST_DIR}/lib/x64/SDL2.lib;${CMAKE_CURRENT_LIST_DIR}/lib/x64/SDL2main.lib")
else ()
    set(SDL2_LIBRARIES "${CMAKE_CURRENT_LIST_DIR}/lib/x86/SDL2.lib;${CMAKE_CURRENT_LIST_DIR}/lib/x86/SDL2main.lib")
endif ()

string(STRIP "${SDL2_LIBRARIES}" SDL2_LIBRARIES)
```

5.
    1. After that you now should be able to reconfigure successfully and generate the Visual Studio project files.

...

## 4. Test App

1.
    1. You can create a test application to verify that everything works.
    2. You could for instance use the following code for that which renders a black window.

```cpp
#include "SDL.h"

int main(int argc, char *argv[])
{
    SDL_Init(SDL_INIT_VIDEO);

    SDL_Window* window = SDL_CreateWindow(
        "SDL2Test",
        SDL_WINDOWPOS_UNDEFINED,
        SDL_WINDOWPOS_UNDEFINED,
        640,
        480,
        0
    );

    SDL_Renderer* renderer = SDLCreateRenderer(window, -1, SDL_RENDERER_SOFTWARE);
    SDL_SetRenderDrawColor(renderer, 0, 0, 0, SDL_ALPHA_OPAQUE);
    SDL_RenderClear(renderer);
    SDL_RenderPresent(renderer);

    SDL_Delay(3000);

    SDL_DestroyWindow(window);
    SDL_Quit();

    return 0;
}
```