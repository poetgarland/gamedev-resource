# C++/CLI

1.
    1. **C++/CLI** (C++ modified for Common Language Infrastructure) is a language specification created by Microsoft which supersedes Managed Extensions for C++.
    2. It is a complete revision that simplifies the now-deprecated Managed C++ syntax and provides interopability with Microsoft .NET languages such as C#.
    3. C++/CLI was standardized by Ecma as **ECMA-372**.
    4. It is currently available in Visual Studio 2005, 2008, 2010, 2012, 2013, 2015, 2017 and 2019 including the Express editions.

## 1. Syntax changes

1.
    1. C++/CLI should be thought of as a language of its own (with a new set of keywords, for example), instead of the C++ superset-oriented Managed C++ (MC++) (whose non-standard keywords were styled like `__gc` or `__value`).
    2. Because of this, there are some major syntactic changes, especially related to the elimination of ambiguous identifiers and the addition of .NET-specific features.

2.
    1. Many conflicting syntaxes, such as the multiple versions of operator `new()` in MC++, have beeb split: in C++/CLI, .NET reference types are created with the new keyword `gcnew` (i.e. garbage collected `new()`).
    2. Also, C++/CLI has introduced the concept of generics from .NET (similar, for the most common purposes, to standard C++ templates, but quite different in their implementation).

### 1.1 Handles

1.
    1. 