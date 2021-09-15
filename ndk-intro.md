# Get Started with the NDK
(9/15/2021)

1.
    1. The Native Development Kit (NDK) is a set of tools that allows youn to use C and C++ code with Android, and provides platform libraries you can use to manage native activities and access physical device components, such as sensors and touch input.
    2. The NDK may not be appropriate for most novice Android programmers who need to use only Java code and framework APIs to develop their apps.
    3. However, the NDK can be useful for cases in which you need to do one or more of the following:

        1. Squeeze extra performance out of a device to achieve low latency or run computationally intensive applications, such as games or physics simulations.
        2. Reuse your own or other developers' C or C++ libraries.

2.
    1. Using Android Studio 2.2 and higher, you can use the NDK to compile C and C++ code into a native library and package it into your APK using Gradle, the IDE's integrated build system.
    2. Your Java code can then call functions in your native library through the Java Native Interface (JNI) framework.
    3. To learn more about Gradle and the Android build system, read Configure Your Build.

3.
    1. Android Studio's default build tool to compile native libraries is CMake.
    2. Android Studio also supports ndk-build due to the large number of existing projects that use the build toolkit.
    3. However, if you are creating a new native library, you should use CMake.

4.
    1. This guide gives you the information you need to get up and running with the NDK on Android Studio.
    2. If you don't have the latest version of Android Studio, download and install it now.

## 1. Download the NDK and tools
(9/9/15)

1.
    1. To compile and debug native code for your app, you need the following components:

        1. The Android Native Development Kit (NDK): a set of tools that allows you to use C and C++ code with Android.
        2. CMake: an external build tool that works alongside Gradle to build your native library. You do not need this component if you only plan to use ndk-build.
        3. LLDB: the debugger Android Studio uses to debug native code.

2.
    1. For information on installing these components, see install and configure the NDK and CMake.