# SDL Graphics with Vulkan

1.
    1. Vulkan is a generation API for high-efficiency access to graphics and computing on modern GPUs.
    2. It is an open-standard, cross-platform API designed from the ground up by industry experts collaborating under the Khronos consortium.
    3. It aims to address the inefficiencies of existing 3D APIs, such as OpenGL, which are designed for single-core processors and lag to map modern hardware.
    4. It provides a much lower-level fine-grained control over the GPU to maximize performance and achieve a consistent user experience across different operating environments.
    5. For general information on Vulkan, and the comparative merits of Vulkan and OpenGL, see the official Khronos Vulkan Web site, and Vulkan vs OpenGL.

2.
    1. The Tizen platform supports the Vulkan API in order to provide the most cutting-edge 3D programming tools for you to create high-quality games and real-time graphics in applications.
    2. Vulkan is especially recommended for performance- or latency-sensitive applications.
    3. With Vulkan, you can achieve a much smoother user experience by parallelizing the rendering job across multiple threads which can feed the GPU in an efficient manner.
    4. Applications demanding explicit control on work submission, synchronization, and less power consumption can seriously consider migrating to Vulkan as well.
    5. Tizen allows the use of the Vulkan API through SDL.

[Figure]

3.
    1. The main Vulkan API features include rendering 3D objects in an SDL application.

4.
    1. Before using the Vulkan API in Tizen, make sure that you are familiar with the following key Vulkan features, which help you to align your application design around Vulkan concepts and exploit the hardware to the fullest:

        1. **Cross-platform**
            1. Vulkan is designed to run on a wide range of platforms and hardware with very different form factors and power envelopes.
            2. While OpenGL has the OpenGL ES variant for mobile and embedded systems and OpenGL for desktops, Vulkan provides a unified API that is completely identical across all these platforms.
            3. You simply link to the same library, with the same header and the same code on all targeted platforms, and the application runs everywhere.
            4. By not limiting itself to a subset of platforms, Vulkan enables you to "write once and run everywhere".
            5. This means that by targeting this API, you can achieve a large market share across both mobile and desktop devices.

        2. **Explicit control**
            1. Vulkan is an explicit API, and wants you to define "exactly what you want", rather than relying on hidden driver heuristics to do the right thing.
            2. Vulkan, by design, is a very low-level API that provides applications direct control over the GPU.
            3. Older higher-level APIs hide most of the costly operations, such as memory management and resource synchronization, denying you any control over them.
            
            1. Vulkan attempts to turn as much of the problematic "implicitness" of older APIs as possible into "explicit" application choices.
            2. It provides a number of explicit mechanisms for many operations, such as memory allocation, synchronization, and work submission, allowing applications to be more expressive.
        
        3. **Multi-core friendly**
            1. Older APIs written in the single-core processor age do not do enough to take advantage of today's multi-core processors and thereby max out the single core by over-burdening the render thread with all heavy tasks, such as error checking, implicit resource tracking, synchronization, and state validation.

            1. Vulkan provides tools and design choices to spread its workload across multiple threads.
            2. It achieves this by removing from the driver the thread-specific features, such as global data, access synchronization, thread safety, and order guarantee, and handling the responsibility to you.
            3. By carefully handling the threads and scaling across multiple threads on a device with more cores, you can distribute your workload across threads much better.
            4. This leads to better efficiency and better performance in applications that otherwise find themselves maxing out a single core.
        
        4. **High efficiency**
            1. With a better design, Vulkan offers the potential to reduce the workload of the render thread, thereby allowing the application to draw less power and generate less heat.
            2. It achieves better throughput by removing lots of (mostly) unnecessary background work, such as runtime error checking, state validation, and shader compilation, from the main loop.
            3. It delegates that background work to the tooling and layers, which are used during development and get removed before the application makes it to a consumer device.
        
        5. **Portable**
            1. With the introduction of an intermediate shader language, called SPIR-V, Vulkan improves the shader program portability and allows you to write shaders in languages other than GLSL (such as C/C++).
            2. This avoids the need for the computer to execute during runtime to translate the shader code and allows offline shader precompilation.
            3. It also retrieves the vendors from the shader/kernel source shipping and IP leakage.

## 1. Prerequisites

1.
    1. To enable your application to use the Vulkan functionality:

        1. To use Vulkan for 3D rendering, you must create an SDL application, and understand both Vulkan and SDL.
        2.
            1. Check whether the device supports Vulkan.

            1. As not all Tizen devices available on the market support Vulkan yet, check for device support with the `system_info_get_platform_bool()` function bnefore using the Vulkan APIs.
            2. If the device can support Vulkan, the function returns `true` in the second parameter.

```cpp
bool vulkan_support;
system_info_get_platform_bool("http://tizen.org/feature/vulkan.version.1_0", &vulkan_support);
```

        3.
            1. To use the functions and data types of the Vulkan (in mobile and wearable applications) and SDL (in mobile and wearable applications) APIs, include the `<SDL.h>` and `<vulkan/vulkan.h>` header files in your application.

```cpp
#include <SDL.h>
#include <vulkan/vulkan.h>
```

## 2. Rendering a Triangle with Vulkan

1.
    1. To render a triangle using Vulkan in an SDL application:

        1.
            1.
                1. Initialize the SDL library and create an SDL window.
            
            2.
                1. Before using any other SDL functions, call the `SDL_Init()` function to properly initialize the SDL library and start each of its subsystems.
                2. The function accepts as a parameter a set of allowed flags OR'd together.
            
            3.
                1. After SDL is initialized successfully, create the `SDL_Window` instance using the `SDL_CreateWindow()` function.
                2. The parameters define the title of the window, the X and Y position coordinates, width, height, and a set of `SDL_WindowFlags` OR'd together.

> **Note**: To use the Vulkan context, use the `SDL_WINDOW_VULKAN` flag when you create a window. Do not use both `SDL_WINDOW_VULKAN` and `SDL_WINDOW_OPENGL` simultaneously.

            4.
                1. The `SDL_main()` function is mandatory for the Tizen framework to initialize the SDL application.
                2. You must use the `SDL_main()` function instead of the usual `main()` function in your SDL application.

```cpp
int SDL_main(int argc, char *argv[])
{
    SDL_Init(SDL_INIT_VIDEO | SDL_INIT_EVENTS);
    demo.sdl_window = SDL_CreateWindow("SDL Vulkan Sample", 0, 0, demo.sdl_mode.w, demo.sdl_mode.h, SDL_WINDOW_SHOWN | SDL_WINDOW_FULLSCREEN | SDL_WINDOW_VULKAN);
}
```

        2.
            1.
                1. Initialize the Vulkan library.