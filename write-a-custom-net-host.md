# Write a custom .NET host to control the .NET runtime from our native code
(*9/17/2021*)

1.
    1. Like all managed code, .NET applications are executed by a host.
    2. The host is responsible for starting the runtime (including components like the JIT and garbage collector) and invoking managed entry points.

2.
    1. Hosting the .NET runtime is an advanced scenario and, in most cases, .NET developers don't need to worry about hosting because .NET build processes provide a default host to run .NET applications.
    2. In some specialized circumstances, though, it can be useful to explicitly host the .NET runtime, either as a means of invoking managed code in a native process or in order to gain more control over how the runtime works.

3.
    1. This article gives an overview of the steps necessary to start the .NET runtime from native code and execute managed code in it.

## 1. Prerequisites

1.
    1. Because hosts are native applications, this tutorial covers constructing a C++ application to host .NET.
    2. You will need a C++ development environment (such as that provided by Visual Studio).

2.
    1. You will also need to build a .NET component to test the host with, so you should install the .NET SDK.

## 2. Hosting APIs

1.
    1. Hosting the .NET runtime in .NET Core 3.0 and above is done with the `nethost` and `hostfxr` libraries' APIs.
    2. These entry points handle the complexity of finding and setting up the runtime for initialization and allow both launching a managed application and calling into a static managed method.

2.
    1. Prior to .NET Core 3.0, the only option for hosting the runtime was through the coreclrhost.h API.
    2. This hosting API is obsolete now and should not be used for hosting .NET Core 3.0 and higher runtimes.

## 3. Create a host using `nethost.h` and `hostfxr.h`

1.
    1. A sample host demonstrating the steps outlined in the tutorial below is available in the dotnet/samples GitHub repository.
    2. Comments in the sample clearly associate the numbered steps from this tutorial with where they're performed in the sample.
    3. For download instructions, see Samples and Tutorials.

2.
    1. Keep in mind that the sample host is meant to be used for learning purposes, so it is light on error checking and designed to emphasize readability over efficiency.

3.
    1. The following steps detail how to use the `nethost` and `hostfxr` libraries to start the .NET runtime in a native application and call into a managed static method.
    2. The sample uses the `nethost` header and library installed with the .NET SDK and copies of the coreclr_delegates.h and hostfxr.h files from the dotnet/runtime repository.
