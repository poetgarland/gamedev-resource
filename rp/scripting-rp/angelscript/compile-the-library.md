1.
    1. In the `sdk/angelscript/projects` directory you'll find project files for many of the popular compilers.
    2. However, these project files are not always up to date with the latest version of the library.
    3. If you get any compiler or linker errors please make sure the project file includes all the files in the `sdk/angelscript/source` directory, and that the project settings are set according to this article.

2.
    1. If you don't find a project file for your compiler, you can easily create your own project by adding all the files in the `sdk/angelscript/source` directory, and configuring the project appropriately.
    2. If you have a new compiler/target that hasn't been used with AngelScript before, you may need to edit the `as_config.h` file to make sure the library is compiled properly.

## 1. Set compile time options

1.
    1. The code tries to contain compiler differences in as few places as possible.
    2. The header `as_config.h` was created for that purpose.
    3. There you will find some `#define`s that allow different compilers to work.
    4. You'll probably not have to change this file, but if you're using a compiler not previously used and you're getting compiler errors it might be worth it to take a look at this file.

2.
    1. There are also a couple of other `#define`s used in the code to alter the compilation.
    2. When compiling the library you might want to define `ANGELSCRIPT_EXPORT` so that library functions are exported.
    3. If you include the library source code directory in your application project you shouldn't have to define this flag.

3.
    1. If `AS_DEPRECATED` is define then some backward compatibility is maintained, this can help you do the upgrade to the latest version a little more smoothly.
    2. There is no guarantee that the backward compatibility will be maintained though so try to remove use of deprecated functions as soon as possible.

## 2. Linking with the library

1.
    1. There are four ways of compiling and linking with AngelScript in order to use it.
    2. I recommend linking with a static library.
    3. Note that all four ways are interchangeable with only a small change in your code, i.e. a defined flag before including the header file, and possibly a routine for manually loading the DLL.
    4. The rest of your code should look exactly the same for each of the alternatives.

### 1. Include library source files in project

1.
    1. You can take the source files for AngelScript and include them directly in your own project.
    2. The advantage of this is that you can be sure that the same compiler options are used for the library and the host applications, e.g. multi-threaded or single-threaded CRT.
    3. The disadvantage is that your project will be polluted with the library files.

2.
    1. The files that need to use the library should include the angelscript.h header with no need for any special settings.

```cpp
// Include the library interface
#include "angelscript.h"

// ... Start using the library
```

### 2. Compile a static library and link into project

1.
    1. The most recommended way is to compile a static library that your project will link with.
    2. When compiling the static library you have to make sure that the correct compiler settings are used so that you don't get conflicts in linkage with the CRT functions.
    3. This happens if you for example compile the library with dynamically linked multi-threaded CRT and your application with statically linked single-threaded CRT.
    4. (For Visual C++ you'll find these settings under Project → Settings → C/C++ → Category: Code Generation).

2.
    1. To use the library you only need to include the `angelscript.h` header file.

```cpp
// Include the library interface
#include "angelscript.h"

// ... Start using the library
```

### 3. Compile a dynamically loaded library with an import library

1.
    1. With Microsoft Visual C++ it is possible to compile a dynamically loaded library with an import library.
    2. The import library will then take care of the work needed to load the dll and bind the functions.
    3. A possible disadvantage of this method is that you are not able to give any user-friendly error messages in case loading the library fails.

2.
    1. To use the library you'll have to define `ANGELSCRIPT_DLL_LIBRARY_IMPORT` before including the `angelscript.h` header file.

```cpp
// Include the library interface
#define ANGELSCRIPT_DLL_LIBRARY_IMPORT
#include "angelscript.h"

// ...Start using the library
```

### 4. Load the dynamically loaded library manually

1.
    1. If you want to use a dll, e.g. to share code between applications, I recommend loading the library manually as you can treat any failures to load or bind functions graciously.

2.
    1. To use manually loaded DLLs, you should define `ANGELSCRIPT_DLL_MANUAL_IMPORT` before including the `angelscript.h` header file.
    2. This will insure that the header file doesn't declare the function prototypes, as you will most likely want to use these names for the function pointers.

```cpp
// Include the library interface
#define ANGELSCRIPT_DLL_MANUAL_IMPORT
#include "angelscript.h"

// Declare the function pointers
typedef asIScriptEngine* AS_CALL t_asCreateScriptEngine(int);
t_asCreateScriptEngine* asCreateScriptEngine = 0;

// ... Declare the rest of the functions

// Load the dll and bind the functions (error handling left out for clarity)
HMODULE dll = LoadLibrary("angelscript.dll");
asCreateScriptEngine = (t_asCreateScriptEngine*)GetProcAddress(dll, "_asCreateScriptEngine");

// ... Bind the other functions

// ... Start using the library
```

## 3. Considerations for specific platforms

1.
    1. As mentioned before, for most platforms the compilation of the library is as easy as including all source files and compiling them.
    2. However, on some platforms specific actions need to be performed to compile the library correctly.

### 1. Windows 64bit

1.
    1. The MSVC compiler doesn't support inline assmelber for the x86 64 bit CPU family.
    2. To support this platform a separate assembler file has been created: `as_callfunc_x64_msvc_asm.asm`.

2.
    1. To compile this file it is necessary to configure a custom build command with the following:

```cpp
ml64.exe /c /nologo /Fo$(OutDir)\as_callfunc_x64_msvc_asm.obj /W3 /Zi /Ta $(InputDir)\$(InputFileName)
```

### 2. Microsoft Visual C++

1.
    1. While AngelScript doesn't use Microsoft's language extensions you may still face trouble compiling the library if you disable the language extensions.
    2. This is because Microsoft's own SDK may have code that relies on the language extensions, e.g. in version 6.0a you might get compiler errors due to the existance of $ in the macro definitions in the `specstrings.h` header from the Platform SDK.
    3. This particular problem was fixed by Microsoft in version 6.1 of their SDK, but there may be others so it might just be easier to leave the language extensions turned on.

### 3. GNUC based compilers

1.
    1. In order to properly integrate with C++ without the need for wrappers AngelScript uses a lot of pointer casts.
    2. Unfortunately it is not possible to always guarantee strict aliasing because of this, so on GNUC based compielrs it is necessary to disable compiler optimizations that assume strict aliasing.

2.
    1. Use the following compiler argument to disable this: `-fno-strict-aliasing`

### 4. Pocket PC with ARM CPU

1.
    1. The MSVC compiler doesn't support linline assembler for the ARM CPU, so a separate assembler file has been written with this code: `as_callfunc_arm_msvc.asm`.

2.
    1. In order to compile this file properly it is necessary to configure a custom build command with the following:

```sh
$ armasm -g $(InputPath)
```

### 5. Marmalade

1.
    1. Marmalade is a cross platform SDK created with mobile devices in mind.
    2. It functions by abstracting the underlying OS with its own C runtime library even though it uses the common C++ compilers, e.g. MSVC on Windows, and GNUC on Linux and Mac.

2.
    1. When compiling AngelScript with Marmalade for iOS and Android scons must be used in order to properly compile the native ARM assembler routines.
    2. For Windows Phone you should be able to use MSVC normally.

## 4. Size of the library

1.
    1. The size of the library depends on many different factors, such as compiler brand, compiler flags, and also what features of AngelScript are included.
    2. However, to give an idea of how much space the library will take up on the disk and memory I've compiled the asrun sample in a few different ways and noted down the size.

| Options | Size of binary on disk |
|---------|------------------------|
| 32 bit / multithreaded dll / optimize for speed without including AngelScript | 14KB |
| 32 bit / multithreaded dll / optimize for speed / using angelscript and add-ons | 796KB |
| 32 bit / multithreaded dll / optimize for speed / angelscript without compiler (`AS_NO_COMPILER`) and add-ons | 453KB |
| 32 bit / multithreaded static / optimize for speed / using AngelScript but without add-ons | 867KB |
| 32 bit / multithreaded static / optimize for speed / Using AngelScript and add-ons | 1015KB |
| 64 bit / multithreaded static / optimize for speed / using AngelScript and add-ons | 1336KB |
| 32 bit / multithreaded static / optimize for size / Using AngelScript and add-ons | 797KB |
| 32 bit / multithreaded dll / optimize for size using AngelScript and add-ons | 582KB |

2.
    1. Based on this we can draw the conclusion that the engine and VM takes up about 300KB when optimized for speed, the compiler adds another 350KB, and the add-ons yet another 150KB.

>**Note**: these tests were made with MSVC 2012 and version 2.30.2 of the library.