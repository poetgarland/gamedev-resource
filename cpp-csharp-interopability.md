# C++/C# Interopability

1.
    1. This is a blog entry on C++/C# interopability.

2.
    1. Recently I was working on a project where I needed to use C++ code from within a C# application.
    2.  Apart from calling and executing C++ functions from within C#, data needed to be passed back and forth across this divide.
    3. This is not a trivial task, considering the different paradigms used by the two languages,. mainly the *unmanaged* nature of C++ versus the *managed* nature of C#.

3.
    1. As the project dealt with image processing functionality, some of the data that needed ot be passed from C# to C++ and back consisted of raw image buffers.

4.
    1. In this blog I will briefly describe the mechanism used for performing C++/C# interopability.
    2. Some code is also provided for handling the 'transfer' of data between the two languages.

## 1. Managed code

1.
    1. C# is one of the programming languages forming part of Microsoft's *.NET framework*.
    2. C# programs get compiled into an intermediate type of langauge, called *IL*, that run on a virtual execution system, called *Common Language Runtime (CLR)*.

2.
    1. Using Java as an analogy, think of the CLR as being the *Java VM (JVM)*.
    2. One difference is that, apart from C#, there are other languages in the .NET framework.
    3. For example, F# and Visual Basic.
    4. Programs written in these languages all get compiled to the same intermediate code that executes on CLR.

3.
    1. One characteristic of the .NET languages is that they make use of *managed* code.
    2. In managed code, the CLRt takes responsibility of managing the memory and other resources of the programs.
    3. This 'management' can include garbage collection, control of the lifetime of objects, enhanced debugging functionality, etc.

4.
    1. In contrast, unmanaged code like that of C++, the runtime system knows little about the memory and resources used by the program and can provide minimal services.
    2. It's the program's responsibility to manage such objects and resources.

5.
    1. One of the issues faced in making C++ code interopable with C# is how to go about moving data across the boundary between managed and unmanaged code.
    2. This process is known as **marshaling**.

## 2. Interopability mechanisms

1.
    1. The .NET framework provides different ways of achieving interopability.
    2. These are:

        1. Platform invocation (P/Invoke)
        2. C++ Interop
        3. COM Interop
        4. Embedded Interop types

2.
    1. **Platform Invocation** (**PInvoke** for short) allows for managed code to call native unmanaged functions implemented as DLLs.
    2. This method is ideal for when we have API-like functions written in C or C++ that need to be accessed from within a C# program.
    3. For further info on PInvoke follow this link.

3.
    1. **C++ Interop** is also known as *implicit PInvoke* and informally referred to as *It Just Works*.
    2. This mechanism consists of wrapping a native C++ class so that it can be consumed by C# code.
    3. More details on this method can be found here.

4.
    1. **COM Interop** is a mechanism specifically for exposing COM components to a .NET language.
    2. In other words, the unmanaged code must be encapsulated as a COM object for this mechanism to be applicable.

5.
    1. A recent mechanism that was introduced with .NET version 4.0 is **Embedded Interop Types**.
    2. This is based on defining the equivalence of types.

6.
    1. For my image processing-based project, I opted for the PInvoke method mainly because it fits quite well with the API-style of usage of the C++ code.

## 3. A PInvoke example

1.
    1. The diagram below summarises the PInvoke mechanism.

[diagram]

2.
    1. The native C++ code is compiled as a DLL with C-type linkage used for the exported functions ('DLL function 1' and 'DLL function 2' in the diagram above).
    2. Sample C++ code is given below:

```cpp
#pragma once

#ifdef IMAGEHANDLER_EXPORTS
#    define IMAGEHANDLER_API __declspec(dllexport)
#else
#    define IMAGEHANDLER_API __declspec(dllimport)
#endif

extern "C" IMAGEHANDLER_API float ValidateImageColourInterop(
    char**& inputParams,
    int* numInputParams,
    char**& outputParams,
    int* numOutputParams
);
extern "C" IMAGEHANDLER_API float ValidateImageResolutionInterop(
    char**& intputParams
    int* numInputParams,
    char**& outputParams,
    int* numOutputParams
);
```

3.
    1. The C++ functions to be invoked must also be declared in the C# code.
    2. These declarations are called the *managed signatures* of the functions, and must specify the name of the DLL library in which they reside, obviously the function names, the return types of the functions, and the input parameters of the functions.

```cs
using System;
using System.Runtime.InteropServices;

public class ColourValidator
{
    [DllImport("ImageHandler", CallingConvention = CallingConvention.Cdecl, CharSet = CharSet.Ansi)]
    static extern int ValidateImageColourInterop(ref IntPtr inParams, ref int inParamsSize, ref IntPtr outParams, ref int outParamsSize);

    public bool Validate()
    {
        // ...

        int rc = ValidateImageColourInterop(ref inputParamsRoot, ref inputParamsSize, ref outParamsRoot, ref outParamsSize);

        // ...
    }
}
```

4.
    1. The managed signatures provide the information needed by the CLR to locate the C++ functions in the respective DLLs.

## 4. Marshalling of data types

1.
    1. The diagram above also depicts the marshalling mechanism, the process of converting unmanaged types and vice-versa.
    2. The black line indicates the boundary between the managed and unmanaged code.
    3. An example is given whereby a C++ `DWORD` is marshalled into a `System.UInt32` on .NET.

2.
    1. For some types, marshalling can be done in different ways.
    2. For example, an `LPCTSTR` in C++ (a long pointer to a constant wide-character string) can be marshalled to a `System.String`, or a `System.Text.StringBuilder`, or a `System.Char[]` type.

3.
    1. The .NET framework provides a marshaler object, unsurprisingly called `System.Runtime.InteropServices.Marshaller`, that provides common marshalling functionality.