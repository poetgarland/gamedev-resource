# Chapter 1: Game Programming Overview

**This chapter first discusses how to set up a development environment and access the source code for this book. Next, it covers the core concepts behind any real-time game: the game loop, how a game updates over time, and the basics of game input and output. Throughout the chapter, you will see how to implement code for a version of the classic game *Pong*.**

## 1.1 Setting Up a Development Environment

1.
    1. Although it's possible to write the source code for any program with a text editor, professional developers typically use an **integrated development environment** (**IDE**).
    2. The advantage of an IDE is that it provides code completion and debugging in addition to text editing capabilties.
    3. The code for this book works on both Microsoft Windows and Apple macOS, and the choice of IDE depends on the choice of platform.
    4. For Windows, this book uses Microsoft Visual Studio, and for macOS, it uses Apple Xcode.
    5. The remainder of this section contains brief instructions on setup of these environments on their respective platforms.

### 1.1.1 Microsoft Windows

1.
    1. For Windows development, the most popular IDE by far is Microsoft Visual Studio.
    2. Visual Studio also tends to be the most popular IDE for C++ game developers, with most PC and console developers gravitating toward the IDE.

2.
    1. This book uses Microsoft Visual Studio Community 2017, which is available as a free download at https://www.visualstudio.com/downloads/.
    2. Instllation of Visual Studio Community 2017 requires Microsoft Windows 7 or higher.

3.
    1. When you run the installer program for Visual Studio, it asks which "workloads" it should install.
    2. Make sure to minimally select the Game Development with C++ workload.
    3. Feel free to also select any other workloads or options desired.

[warning about visual studio versions ... . .....]

### 1.1.2 Apple macOS

.... . . . .. 

## 1.2 Getting This Book's Source Code

...  . . .

## 1.3 Beyond the C++ Standard Library

1.
    1. The C++ Standard Library only supports text console input and output and does not have any graphics libraries built in.
    2. To implement graphics in a C++ program, you must use one of the many available external libraries.

2.
    1. Unfortunately, many libraries are **platform specific**, meaning they work on only one operating system or type of computer.
    2. For example, the Microsoft Windows application programming interface (API) can create windows and other UI elements supported by the Windows operating system.
    3. However, the Windows API doesn't work on Apple macOS—for obvious reasons.
    4. Likewise, macOS has its own set of libraries for these same features that do not work on Windows.
    5. As a game programmer, you can't always avoid platform-specific libraries.
    6. For instance, game developers working with the Sony PlayStation 4 console must use libraries provided by Sony.

3.
    1. Luckily, this book sticks to cross-platform libraries, meaning that the libraries work on many different platforms.
    2. All the source code for this book works on recent versions for both Windows and macOS.
    3. Although Linux support is untested, the game projects generally should also work on Linux.

4.
    1. One of the foundational libraries used in this book is Simple DirectMedia Layer (SDL; see https://www.libsdl.org).
    2. The SDL library is a cross-platform game development library written in C.
    3. It provides support for creating windows, creating basic 2D graphics, processing input, and outputting audio, among other features.
    4. SDL is a very lightweight library that works on many platforms, including Microsoft Windows, Apple macOS, Linux, iOS, and Android.

5.
    1. In this first chapter, the only external library needed is SDL.
    2. Subsequent chapters use other libraries and introduce them when needed.

## 1.4 The Game Loop and Game Class

1.
    1. One of the big differences between a game and any other program is that a game must update many times per second for as long as the program runs.
    2. A **game loop** is a loop that controls the overall flow for the entire game program.
    3. Like any other loop, a game loop has code it executes on every iteration, and it has a loop condition.
    4. For a game loop, you want to continue looping as long as the player hasn't quit the game program.

2.
    1. Each iteration of a game loop is a **frame**.
    2. If a game runs at 60 **frames per second** (**FPS**), this means the game loop completes 60 iterations every second.
    3. Many real-time games run at 30 or 60 FPS.
    4. By running this many iterations per second, the game gives the illusion of continuous motion even though it's only updating at periodic intervals.
    5. The term **frame rate** is interchangeable with FPS; a frame rate of 60 means the same thing as 60 FPS.

### 1.4.1 Anatomy of a Frame

1.
    1. At a high level, a game performs the following steps on each frame:
        1. It processes any inputs.
        2. It updates the game world.
        3. It generates any outputs.

2.
    1. Each of these three steps has more depth than may be apparent at first glance.
    2. For instance, processing inputs (step 1) clearly implies detecting any inputs from devices such as a keyboard, mouse, or controller.
    3. But these might not be the only inputs for a game.
    4. Consider a game that supports an online multiplayer mode.
    5. In this case, the game receives data over the Internet as an input.
    6. In certain types of mobile games, another input be what's visible to the camera, or perhaps GPS information.
    7. Ultimately, the inputs to a game depend on both the type of game and the platform it runs on.

3.
    1. Updating a game world (step 2) means going through every object in the game world and updating it as needed.
    2. This could be hundreds or even thousands of objects, including characters in the game world, parts of the user interface, and other objects that affect the game—even if they are not visible.

4.
    1. For step 3, generating any outputs, the most apparent output is the graphics.
    2. But there are other outputs, such as audio (including sound effects, music, and dialogue).
    3. As another example, most console games have **force feedback** effects, such as the controller shaking when something exciting happens in the game.
    4. And for an online multiplayer game, an additional output would be data sent to the other players over the internet.

5.
    1. Consider how this style of game loop might apply to a simplified version of the classic Namco arcade game *Pac-Man*.
    2. For this simplified version of the game, assume that the game immediately begins with *Pac-Man* in a maze.
    3. The game program continues running until Pac-Man either completes the maze or dies.
    4. In this case, the "process inputs" phase of the game loop need only read in the joystick input.

6.
    1. The "update game world" phase of the loop updates Pac-Man based on this joystick input and then also updates the four ghosts, pellets, and the user interface.
    2. Part of this update code must determine whether Pac-Man runs into any ghosts.
    3. Pac-Man can also eat any pellets or fruits he moves over, so the update portion of the loop also needs to check for this.
    4. Because the ghosts are fully AI controlled, they also must update their logic.
    5. Finally, based on what Pac-Man is doing, the UI may need to update what data it displays.

> **note**
> This style of game loop is **single-threaded**, meaning it does not take advantage of modern CPUs that can execute multiple threads simultaneously. Making a game loop that supports multiple threads is very complex, and not necessarily for games that are smaller in scope. A good book to learn more about multi-threaded game loops is Jason Gregory's, listed in the "Additional Reading" section at the end of this chapter.

7.
    1. The only outputs in the "generate outputs" phase of the classic *Pac-Man* game are the audio and video.
    2. Listing 1.1 provides pseudocode showing what the game loop for this simplified version of *Pac-Man* might look like.

**Listing 1.1** *Pac-Man* Game Loop Pseudocode

```cpp
void Game::RunLoop()
{
    while (!mShouldQuit)
    {
        // Process Inputs
        JoystickData j = GetJoystickData();

        // Update Game World
        UpdatePlayerPosition(j);

        for (Ghost& g : mGhost)
        {
            if (g.Collides(player))
            {
                // Handle Pac-Man colliding with a ghost
            }
            else
            {
                g.Update();
            }
        }

        // Handle Pac-Man eating pellets
        // ...

        // Generate Outputs
        RenderGraphics();
        RenderAudio();
    }
}
```

### 1.4.2 Implementing a Skeleton Game Class

1.
    1. You are now ready to use your basic knowledge of the game loop to create a `Game` class that contains code to initialize and shut down the game as well as run the game loop.
    2. If you are rusty in C++, you might want to first review the content in Appendix A, "Intermediate C++ Review," as the remainder of this book assumes familiarity with C++.
    3. In addition, it may be helpful to keep this chapter's completed source code handy while reading along, as doing so will help you understand how all the pieces fit together.

2.
    1. __Listing 1.2__ shows the declaration of the `Game` class in the `Game.h` header file.
    2. Because this declaration references an `SDL_Window` pointer, you need to also include the main SDL header file `SDL/SDL.h`.
    3. (If you wanted to avoid including this here, you could use a forward declaration.)
    4. Many of the member function names are self-explanatory; for example, the `Initialize` function initializes the `Game` class, the `Shutdown` function shuts down the game, and the `RunLoop` function runs the game loop.
    5. Finally, `ProcessInput`, `UpdateGame`, and `GenerateOutput` correspond to the three steps of the game loop.

3.
    1. Currently, the only member variables are a pointer to the window (which you'll create in the `Initialize` function) and a bool that signifies whether the game should continue running the game loop.

**Listing 1.2** `Game` Declaration

```cpp
class Game
{
public:
    Game();
    // Initialize the game
    bool Initialize();
    // Runs the game loop until the game is over
    void RunLoop();
    // Shutdown the game
    void Shutdown();
private:
    // Helper functions for the game loop
    void ProcessInput();
    void UpdateGame();
    void GenerateOutput();

    // Window created by SDL
    SDL_Window* mWindow;
    // Game should continue to run
    bool mIsRunning;
}
```

4.
    1. With this declaration in place, you can start implementing the member functions in `Game.cpp`.
    2. The constructor simply initializes `mWindow` to `nullptr` and `mIsRunning` to `true`.

#### Game::Initialize

5.
    1. The `Initialize` function returns `true` if initialization succeeds and `false` otherwise.
    2. You need to initialize the SDL library with the `SDL_Init` function.
    3. This function takes in a single parameter, a bitwise-OR of all subsystems to initialize.
    4. For now, you only need to initialize the video subsystem, which you do as follows:

```cpp
int sdlResult = SDL_Init(SDL_INIT_VIDEO);
```

6.
    1. Note that `SDL_Init` returns an integer.
    2. If this integer is nonzero, it means the initialization failed.
    3. In this case, `Game::Initialize` should return `false` because without SDL, the game cannot continue.

```cpp
if (sdlResult != 0)
{
    SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
    return false;
}
```

7.
    1. Using the `SDL_Log` function is a simple way to output messages to the console in SDL.
    2. It uses the same syntax as the C `printf` function, so it supports outputting variables to `printf` specifiers such as `%s` for a C-style string and `%d` for an integer.
    3. The `SDL_GetError` function returns an error message as a C-style string, which is why it's passed in as the `%s` parameter in this code.

8.
    1. SDL contains several different subsystems that you can initialize with `SDL_Init`.
    2. Table 1.1 shows the most commonly used subsystems; for the full list, consult the SDL API reference at https://wiki.libsdl.org.

**Table 1.1** SDL Subsystem Flags of Note

| Flag | Subsystem |
|------|-----------|
| `SDL_INIT_AUDIO` | Audio device management, playback, and recording. |
| `SDL_INIT_VIDEO` | Video subsystem for creating a window, interfacing with OpenGL, and 2D graphics. |
| `SDL_INIT_HAPTIC` | Force feedback subsystem. |
| `SDL_INIT_GAMECONTROLLER` | Subsystem for supporting controller input devices. |

9.
    1. If SDL initializes successfully, the next step is to create a window with the `SDL_CreateWindow` function.
    2. This is just like the window that any other Windows or macOS program uses.
    3. The `SDL_CreateWindow` function takes in several parameters: the title of the window, the x/y coordinates of the top-left corner, the width/height of the window, and optionally any window creation flags:

```cpp
mWindow = SDL_CreateWindow(
    "Game Programming in C++ (Chapter 1)", // Window title
    100, // Top left x-coordinate of window
    100, // Top left y-coordinate of window
    1024, // Width of window
    768,  // Height of window
    0     // Flags (0 for no flags set)
)
```

10.
    1. As with the `SDL_Init` call, you should verify that `SDL_CreateWindow` succeeded.
    2. In the event of failure, `mWindow` will be `nullptr`, so add this check:

```cpp
if (!mWindow)
{
    SDL_Log("Failed to create window: %s", SDL_GetError());
    return false;
}
```