# C#

1.
    1. CMake learned to support `CSharp` (C#) as a first-class language that can be enabled via the `project()` and `enable_language()` commands.
    2. It is currently supported by the Visual Studio Generators for VS 2010 and above.

2.
    1. C# assemblies and programs can be added just like common C++ targets using the `add_library()` and `add_executable()` commands.
    2. References between C# targets in the same source tree may be specified by `target_link_libraries()` like for C++.
    3. References to system or 3rd-party assemblies may be specified by the target properties `VS_DOTNET_REFERENCE_<refname>` and `VS_DOTNET_REFERENCES`.
    4. More fine tuning of C# targets may be done using target and source file properties.
    5. Specifically the target properties related to Visual Sudio (`VS_*`) are worth a look (for setting toolset versions, root namespaces, assembly icons, ...).