# 1. Base Code

## 1. General Structure

1.
    1. In the previous chapter you've created a Vulkan project with all of the proper configuration and tested it with the sample code.
    2. In this chapter we're starting from scratch with the following code:

```cpp
#include <vulkan/vulkan.h>

#include <iostream>
#include <stdexcept>
#include <cstdlib>

class HelloTriangleApplication {
    public:
        void run() {
            initVulkan();
            mainLoop();
            cleanup();
        }
    private:
        void initVulkan() {

        }

        void mainLoop() {

        }

        void cleanup() {

        }
};

int main() {
    HelloTriangleApplication app;

    try {
        app.run();
    } catch (const std::exception& e) {
        std::cerr << e.what() << std::endl;
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

2.
    1. We first include the Vulkan header from the LunarG SDK, which provides the functions, structures and enumerations.
    2. The `stdexcept` and `iostream` headers are included for reporting and propagating errors.
    3. The `cstdlib` header provides the `EXIT_SUCCESS` and `EXIT_FAILURE` macros.

3.
    1. The program itself is wrapped into a class where we'll store the Vulkan objects as private class members and add functions to initiate each of them, which will be called from the `initVulkan` function.
    2. Once everything has been prepared, we enter the main loop to start rendering frames.
    3. We'll fill in the `mainLoop` function to include a loop that iterates until the window is closed in a moment.
    4. Once the window is closed and `mainLoop` returns, we'll make sure to deallocate the resources we've used in the `cleanup` function.

4.
    1. If any kind of fatal error occurs during execution then we'll throw a `std::runtime_error` exception with a descriptive message, which will propagate back to the `main` function and be printed to the command prompt.
    2. To handle a variety of standard exception types as well, we catch the more general `std::exception`.
    3. One example of an error that we will deal with soon is finding out that a certain required extension is not supported.

5.
    1. Roughly every chapter that follows after this one will add one new function that will be called from `initVulkan` and one or more new Vulkan objects to the private class members that need to be freed at the end in `cleanup`.

## 2. Resource management

1.
    1. Just like each chunk of memory allocated with `malloc` requires a call to `free`, every Vulkan object that we create needs to be explicitly destroyed when we no longer need it.
    2. In C++ it is possible to perform automatic resource management using RAII or smart pointers provided in the `<memory>` header.
    3. However, I've chosen to be explicit about allocation and deallocation of Vulkan objects in this tutorial.
    4. After all, Vulkan's niche is to be explicit about every operation to avoid mistakes, so it's good to be explicit about the lifetime of objects to learn how the API works.

2.
    1. After following this tutorial, you could implement automatic resource management by writing C++ that acquire Vulkan objects in their constructor and release them in their destructor, or by providing a custom deleter to either `std::unique_ptr` or `std::shared_ptr`, depending on your ownership requirements.
    2. RAII is the recommended model for larger Vulkan programs, but for learning purposes it's always good to know what's going on behind the scenes.

3.
    1. Vulkan objects are either created directly with functions like `vkCreateXXX`, or allocated through another object with functions like `vkAllocateXXX`.
    2. After making sure that an object is no longer used anywhere, you need to destroy it with the counterparts `vkDestroyXXX` and `vkFreeXXX`.
    3. The parameters for these functions generally vary for different types of objects, but there is one parameter that they all share: `pAllocator`.
    4. This is an optional parameter that allows you to specify callbacks for a custom memory allocator.
    5. We will ignore this parameter in the tutorial and always pass `nullptr` as argument.

## 3. Integrating GLFW

1.
    1. Vulkan works perfectly fine without creating a window if you want to use it for off-screen rendering, but it's a lot more exciting to actually show something!
    2. First replace the `#include <vulkan/vulkan.h>` line with

```cpp
#define GLFW_INCLUDE_VULKAN
#include <GLFW/glfw3.h>
```

2.
    1. That way GLFW will include its own definitions and automaticaly load the Vulkan header with it.
    2. Add a `initWindow` function and add a call to it from the `run` function before the other calls.
    3. We'll use that function to initialize GLFW and create a window.

```cpp
void run() {
    initWindow();
    initVulkan();
    mainLoop();
    cleanup();
}

private:
    void initWindow() {

    }
```

3.
    1. The very first call in `initWindow` should be `glfwInit()`, which initializes the GLFW library.
    2. Because GLFW was originally designed to create an OpenGL context, we need to tell it to not create an OpenGL context with a subsequent call:

```cpp
glfwWindowHint(GLFW_CLIENT_API, GLFW_NO_API);
```

4. Because handling resized windows takes special care that we'll look into later, disable it for now with another window hint call:

```cpp
glfwWindowHint(GLFW_RESIZABLE, GLFW_FALSE);
```

5.
    1. All that's left now is creating the actual window.
    2. Add a `GLFWwindow* window;` private class member to store a reference to it and initialize the window with:

```cpp
window = glfwCreateWindow(800, 600, "Vulkan", nullptr, nullptr);
```

6.
    1. The first three parameters specify the width, height and title of the window.
    2. The fourth parameter allows you to optionally specify a monitor to open the window on and the last parameter is only relevant to OpenGL.

7.
    1. It's a good idea to use constants instead of hardcoded width and height numbers because we'll be referring to these values a couple of times in the future.
    2. I've added the following lines above the `HelloTriangleApplication` class definition:

```cpp
const uint32_t WIDTH = 800;
const uint32_t HEIGHT = 600;
```

    3. and replaced the window creation call with

```cpp
window = glfwCreateWindow(WIDTH, HEIGHT, "Vulkan", nullptr, nullptr);
```

8.
    1. You should now have a `initWindow` function that looks like this:

```cpp
void initWindow() {
    glfwInit();

    glfwWindowHint(GLFW_CLIENT_API, GLFW_NO_API);
    glfwWindowHint(GLFW_RESIZABLE, GLFW_FALSE);

    window = glfwCreateWindow(WIDTH, HEIGHT, "Vulkan", nullptr, nullptr);
}
```

9.
    1. To keep the application running until either an error occurs or the window is closed, we need to add an event loop to the `mainLoop` function as follows:

```cpp
void mainLoop() {
    while (!glfwWindowShouldClose(window)) {
        glfwPollEvents();
    }
}
```

10.
    1. This code should be fairly self-explanatory.
    2. It loops and checks for events like pressing the X button until the window has been closed by the user.
    3. This is also the loop where we'll later call a function to render a single frame.

11.
    1. Once the window is closed, we need to clean up resources by destroying it and terminating GLFW itself.
    2. This will be our first `cleanup` code:

```cpp
void cleanup() {
    glfwDestroyWindow(window);
    glfwTerminate();
}
```

12.
    1. When you run the program now you should see a window titled `Vulkan` show up until the application is terminated by closing the window.
    2. Now that we have the skeleton for the Vulkan application, let's create the first Vulkan object!

# 2. Instance

## 1. Creating an instance

1.
    1. The very first thing you need to do is initialize the Vulkan library by creating an *instance*.
    2. The instance is the connection between your application and the Vulkan library and creating it involves specifying some details about your application to the driver.

2.
    1. Start by adding a `createInstance` function and invoking it in the `initVulkan` function.

```cpp
void initVulkan() {
    createInstance();
}
```

3.
    1. Additionally add a data member to hold the handle to the instance:

```cpp
private:
    VkInstance instance;
```

4.
    1. Now, to create an instance we'll first have to fill in a struct with some information about our application.
    2. This data is technically optional, but it may provide some useful information to the driver in order to optimize our specific application (e.g. because it uses a well-known graphics engine with certain special behavior).
    3. This struct is called `VkApplicationInfo`:

```cpp
void createInstance() {
    VkApplicationInfo appInfo {};
    appInfo.sType = VK_STRUCTURE_TYPE_APPLICATION_INFO;
    appInfo.pApplicationName = "Hello Triangle";
    appInfo.applicationVersion = VK_MAKE_VERSION(1, 0, 0);
    appInfo.pEngineName = "No Engine";
    appInfo.engineVersion = VK_MAKE_VERSION(1, 0, 0);
    appInfo.apiVersion = VK_API_VERSION_1_0;
}
```

5.
    1. As mentioned before, many structs in Vulkan require you to explicitly specify the type in the `sType` member.
    2. This is also one of the many structs with a `pNext` member that can point to extension information in the future.
    3. We're using value initialization here to leave it as `nullptr`.

6.
    1. A lot of information in Vulkan is passed through structs instead of function parameters and we'll have to fill in one more struct to provide sufficient information for creating an instance.
    2. This next struct is not optional and tells the Vulkan driver which global extensions and validation layers we want to use.
    3. Global here means that they apply to the entire program and not a specific device, which will become clear in the next few chapters.

```cpp
VkInstanceCreateInfo createInfo {};
createInfo.sType = VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO;
createInfo.pApplicationInfo = &appInfo;
```

7.
    1. The first two parameters are straightforward.
    2. The next two layers specify the desired global extensions.
    3. As mentioned in the overview chapter, Vulkan is a platform agnostic API, which means that you need an extension to interface with the window system.
    4. GLFW has a handy, built-in function that returns the extension(s) it needs to do that which we can pass to the struct:

```cpp
uint32_t glfwExtensionCount = 0;
const char** glfwExtensions;

glfwExtensions = glfwGetRequiredInstanceExtensions(&glfwExtensionCount);

createInfo.enabledExtensionCount = glfwExtensionCount;
createInfo.ppEnabledExtensionNames = glfwExtensions;
```

8.
    1. The last two members of the struct determine the global validation layers to enable.
    2. We'll talk about these more in-depth in the next chapter, so just leave these empty for now.

```cpp
createInfo.enabledLayerCount = 0;
```

9.
    1. We've now specified everything Vulkan needs to create an instance and we can finally issue the `vkCreateInstance` call:

```cpp
VkResult result = vkCreateInstance(&createInfo, nullptr, &instance);
```

10.
    1. As you'll see, the general pattern that object creation function parameters in Vulkan follow is:

        1. Pointer to struct with creation info
        2. Pointer to custom allocator callbacks, always `nullptr` in this tutorial.
        3. Pointer to the variable that stores the handle to the new object
    
11.
    1. If everything went well then the handle to the instance was stored in the `VkInstance` class member.
    2. Nearly all Vulkan functions return a value of type `VkResult` that is either `VK_SUCCESS` or an error code.
    3. To check if the instance was created successfully, we don't need to store the result and can just use a check for the success value instead:

```cpp
if (vkCreateInstance(&createInfo, nullptr, &instance) != VK_SUCCESS) {
    throw std::runtime_error("failed to create instance!");
}
```

12.
    1. Now run the program and make sure that the instance is created succesfully.

## 2. Checking for extension support

1.
    1. If you look at the `vkCreateInstance` documentation then you'll see that one of the possible error codes is `VK_ERROR_EXTENSION_NOT_PRESENT`.
    2. We could simplify specify the extensions we require and terminate if that error code comes back.
    3. That makes sense for essential extensions like the window system interface, but what if we want to check for optional functionality?

2.
    1. To retrieve a list of supported extensions before creating an instance, there's the `vkEnumerateInstanceExtensionProperties` function.
    2. It takes a pointer to a variable that stores the number of extensions and an array of `VkExtensionProperties` to store details of the extensions.
    3. It also takes an optional first parameter that allows us to filter extensions by a specific validation layer, which we'll ignore for now.

3.
    1. To allocate an array to hold the extension details we first need to know how many there are.
    2. You can request just the number of extensions by leaving the latter parameter empty:

```cpp
uint32_t extensionCount = 0;
vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, nullptr);
```

4. Now allocate an array to hold the extension details (`include <vector>`):

```cpp
std:vector<VkExtensionProperties> extensions(extensionCount);
```

5. Finally we can query the extension details:

```cpp
vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, extensions.data());
```

6.
    1. Each `VkExtensionProperties` struct contains the name and version of an extension.
    2. We can list them with a simple for loop (`\t` is a tab for indentation):

```cpp
std::cout < "available extensions:\n";

for (const auto& extension : extensions) {
    std::cout << '\t' << extension.extensionName << '\n';
}
```

7.
    1. You can add this code to the `createInstance` function if you'd like to provide some details about the Vulkan suport.
    2. As a challenge, try to create a function that checks if all of the extensions returned by `glfwGetRequiredInstanceExtensions` are included in the supported extensions list.

## 3. Cleaning Up

1.
    1. The `VkInstance` shoudl be only destroyed right before the program exits.
    2. It can be destroyed in `cleanup` with the `vkDestroyInstance` function:

```cpp
void cleanup() {
    vkDestroyInstance(instance, nullptr);
    glfwDestroyWindow(window);
    glfwTerminate();
}
```

2.
    1. The parameters for the `vkDestroyInstance` function are straightforward.
    2. As mentioned in the previous chapter, the allocation and deallocation functions in Vulkan have an optional allocator callback that we'll ignore by passing `nullptr` to it.
    3. All of the other Vulkan resources that we'll create in the following chapters should be cleaned up before the instance is destroyed.

3.
    1. Before continuing with the more complex steps after instance creation, it's time to evaluate our debugging options by checking out validation layers.

