1.
    1. AngelScript is structured around an engine where the application should register the functions, properties, and even types, that the scripts will be able to use.
    2. The scripts are then compiled into modules, where the application may have one or more modules, depending on the need of the application.
    3. The application can also expose a different interface to each module through the use of access profiles.
    4. This is especially useful when the application works with multiple types of scripts, e.g. GUI, AI control, etc.

2.
    1. As the scripts are compiled into bytecode AngelScript also provides a virtual machine, also known as a script context, for executing the bytecode.
    2. The application can have any number of scripts context at the same time, though most applications will probably only need one.
    3. The contexts support suspending the execution and then resuing it, so the application can easily implement features such as concurrent scripts and co-routines.
    4. The script context also provides an interface for extracting run-time information, useful for debugging scripts.

3.
    1. The script language is based on the well-known syntax of C++ and more modern languages such as Java, C# and D.
    2. Anyone with some knowledge of those languages, or other script lanuages with similar syntax, such as JavaScript and ActionScript, should feel right at home with AngelScript.
    3. Contrary to most scripting languages, AngelScript is a strongly typed language, which permits faster execution of the code and smoother interaction with the host application as there will be less need for runtime evaluation of the true type of values.

4.
    1. The memory management in AngelScript is based on reference counting with an incremental garbage collector for detecting and freeing objects with circular references.
    2. This provides for a controlled environment without application freezes as the garbage collector steps in to free up memory.