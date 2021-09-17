9/17/2021

1.
    1. As of CMake 3.8.2, CSharp project generation is officially supported by CMake.

2.
    1. To build the default Visual Studio 2017 generated C#/WPF project using CMake, create a `CMakeLists.txt` file as follows:

        1. Project Declaration

```cmake
project(Example VERSION 0.1.0 LANGUAGES CSharp)
```

        2. Include CMake `CSharpUtilities` if you are planning on using WPF or other designer properties.

```cmake
include(CSharpUtilities)
```

        3. Add all `cs`, `xaml`, `settings`, `properties`.

```cmake
add_executable(
    Example
    App.config
    App.xaml
    App.xaml.cs
    MainWindow.xaml
    MainWindow.xaml.cs

    Properties/AssemblyInfo.cs
    Properties/Resources.Designer.cs
    Properties/Resources.resx
    Properties/Settings.Designer.cs
    Properties/Settings.settings
)
```

    4. Link designer files, `xaml` files, and other properties files with their corresponding cs files.

```cmake
csharp_set_designer_cs_properties(
    Properties/AssemblyInfo.cs
    Properties/Resources.Designer.cs
    Properties/Resources.resx
    Properties/Settings.Designer.cs
    Properties/Settings.settings
)

csharp_set_xaml_cs_properties(
    App.xaml
    App.xaml.cs
    MainWindow.xaml
    MainWindow.xaml.cs
)
```

    5. Set app `App.xaml` properties file as program entry point (if project is a WPF project)

```cmake
set_property(SOURCE App.xaml PROPERTY VS_XAML_TYPE "ApplicationDefinition")
```

    6. Set other `csproj` file flags

```cmake
set_property(TARGET Example PROPERTY VS_DOTNET_TARGET_FRAMEWORK_VERSION "v4.6.1")
set_property(TARGET Example PROPERTY WIN32_EXECUTABLE TRUE)
```

    7. Add libraries

```cmake
set_property(
    TARGET EXAMPLE
    PROPERTY VS_DOTNET_REFERENCES
    "Microsoft.CSharp"
    "PresentationCore"
    "System"
    "System.Core"
    "System.Data"
    "System.Data.DataSetExtensions"
    "System.Net.Http"
    "System.Xaml"
    "System.Xml"
    "System.Xml.Linq"
    "WindowsBase"
)
```

3.
    1. For a working WPF example, see https://github.com/bemehiser/cmake_csharp_example

4.
    1. For a WinForms example, see this answer.