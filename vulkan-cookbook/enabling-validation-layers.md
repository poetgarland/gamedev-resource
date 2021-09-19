# Enabling validation layers

1.
    1. The Vulkan API was designed with performance in mind.
    2. One way to increase its performance is to lower state and error checking performed by the driver.
    2. This is one of the reasons Vulkan is called a "thin API" or "thin driver," it is a minimal abstraction of the hardware, which is required for the API to be portable across multiple hardware vendors and device types (high-performance desktop computers, mobile phones, and integrated and low-power embedded systems).

2.
    1. However, this approach makes creating applications with the Vulkan API much more difficult, compared to the traditional high-level APIs such as OpenGL.
    2. It's because very little feedback is given to developers by the driver, as it expects that programmers will correctly use the API and abide by rules defined in the Vulkan specification.

3.
    1. To mitigate this problem, Vulkan was also designed to be a layered API.
    2. The lowest layer, the core, is the **Vulkan API** itself, which communicates with the **Driver**, allowing us to program the **Hardware** (as seen in the preceding diagram).
    3. On top of it (between the **Application** and the **Vulkan API**), developers can enable additional layers, to ease the debugging process.

### 1. How to do it...

1.
    1. On the Windows operating system family:

        1. Go to the folder in which the SDK was installed and then open the `Config` subdirectory.
        2. Copy the `vk_layer_settings.txt` file into the directory of the executable you want to debug (into a folder of an application you want to execute).
        3. Create an environment variable named `VK_INSTANCE_LAYERS`:
            1. Open the command-line console (Command Prompt/`cmd.exe`).
            2. Type the following:

```bat
setx VK_INSTANCE_LAYERS VK_LAYER_LUNARG_standard_validation
```

            3. Close the console.
        4. Re-open the command prompt once again.
        5. Change the current directory to the folder of the application you want to execute.
        6. Run the application; potential warnings or errors will be displayed in the standard output of the command prompt.
    
2.
    1. On the Linux operating system family:

        1. Go to the folder in which the SDK was installed and then open the `Config` subdirectory.
        2. Copy the `vk_layer_settings.txt` file into the directory of the executable you want to debug (into a folder of an application you want to execute).
        3. Create an environment variable named `VK_INSTANCE_LAYERS`:
            1. Open the Terminal window.
            2. Type the following:

```sh
export VK_INSTANCE_LAYERS=VK_LAYER_LUNARG_standard_validation
```

        4. Run the application; potential warnings or errors will be displayed in the standard output of the Terminal window.

### 2. How it works...

1.
    1. Vulkan validation layers contain a set of libraries which help find potential problems in created applications.
    2. Their debugging capabilties include, but are not limited to, validating parameters passed to Vulkan functions, validating texture and render target formats, tracking Vulkan objects and their lifetime and usage, and checking for potential memory leaks or dumping (displaying/printing) Vulkan API function calls.
    3. These functionalities are enablked by different validation layers, but most of them are gathered into a single layer called `VK_LAYER_LUNARG_standard_validation` which is enabled in this recipe.
    4. Examples of names of other layers include `VK_LAYER_LUNARG_swapchain`, `VK_LAYER_LUNARG_object_tracker`, `VK_LAYER_GOOGLE_threading`, or `VK_LAYER_LUNARG_api_dump`, among others.
    5. Multiple layers can be enabled at the same time, in a similar was as presented here in the recipe.
    6. Just assign the names of the layers you want to activate to the `VK_INSTANCE_LAYERS` environment variable.
    7. If you are a Windows OS user, remember to separate them with a semicolon, as in the example:

```bat
setx VK_INSTANCE_LAYERS VK_LAYER_LUNARG_api:dump;VK_LAYER_LUNARG_core_validation
```

2.
    1. If you are a Linux OS user, separate them with a colon.
    2. Here is an example:

```sh
export VK_INSTANCE_LAYERS=VK_LAYER_LUNARG_api_dump:VK_LAYER_LUNARG_core_validation
```

3.
    1. The environment variable named `VK_INSTANCE_LAYERS` can be also set with other OS specific ways such as, advanced operating system settings on Windows or `/etc/environment` on Linux.

4.
    1. The preceding examples enable validation layers globally, for all applications, but they can also be enabled only for our own application, in its source code during Instance creation.
    2. However, this approach requires us to recompile the whole program every time we want to enable or disable different layers.
    3. So, it is easier to enable them using the preceding recipe.
    4. This way, we also won't forget to disable them when we want to ship the final version of our application.
    5. To disable validation layers, we just have to delete `VK_INSTANCE_LAYERS` environment variable.

> Validation layers should not be enabled in the released (shipped) version of the applications as they may drastically decrease performance.

5.
    1. For a full list of available validation layers, please refer to the documentation, which can be found in the `Documentation` sub-folder of the directory in which the Vulkan SDK was installed.

## 3. See also

1. The following recipes in this chapter:

    1. *Downloading Vulkan's SDK*
    2. *Connecting with a Vulkan Loader library*
    3. *Releasing a Vulkan Loader library*