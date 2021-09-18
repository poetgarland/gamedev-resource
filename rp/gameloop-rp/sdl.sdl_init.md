# `SDL_Init`

9/18/2021

1. Initialize the SDL library.

## 1. Syntax

```c
int SDL_Init(Uint32 flags);
```

## 2. Function Parameters

|--------|-------------------------------|
| flags  | subsytem initialization flags |
|--------|-------------------------------|

## 3. Return Value

1. Returns 0 on success or a negative error code on failure; call `SDL_GetError()` for more information.

## 4. Remarks

1.
    1. `SDL_Init()` simply forwards to calling `SDL_InitSubSystem()`.
    2. Therefore, the two may be used interchangeably.
    3. Though for readability of your code `SDL_InitSubSystem()` might be preferred.

2.
    1. The file I/O (for example `SDL_RWFromFile`) and threading (`SDL_CreateThread`) subsystems are initialized by default.
    2. Message boxes (`SDL_ShowSimpleMessageBox`) also attempt to work without initializing the video subsystem, in hopes of being useful in showing an error dialog when `SDL_Init` fails.
    3. You must specifically initialize other subsystems if you use them in your application.

3.
    1. Logging (such as `SDL_Log`) works without initialization, too.

4.
    1. `flags` may be any of the following OR'd together:

        1. `SDL_INIT_TIMER`: timer subsystem
        2. `SDL_INIT_AUDIO`: audio subsystem
        3. `SDL_INIT_VIDEO`: video subsystem; automatically initializes the events subsystem
        4. `SDL_INIT_JOYSTICK`: joystick subsystem; automatically initializes the events subsystem
        5. `SDL_INIT_HAPTIC`: haptic (force feedback) subsystem
        6. `SDL_INIT_GAMECONTROLLER`: controller subsystem; automatically initializes the joystick subsystem
        7. `SDL_INIT_EVENTS`: events subsystem
        8. `SDL_INIT_EVERYTHING`: all of the above subsystems
        9. `SDL_INIT_NOPARACHUTE`: compatibility; this flag is ignored

5.
    1. Subsystem initialization is ref-counted, you must call `SDL_QuitSubSystem()` for each `SDL_InitSubSystem()` to correctly shutdown a subsystem manually (or call `SDL_Quit()` to force shutdown).
    2. If a subsystem is already loaded then this call will increase the ref-count and return.

## 5. Code Examples

```cpp
#include "SDL.h"

int main(int argc, char* argv[])
{
    if (SDL_Init(SDL_INIT_VIDEO|SDL_INIT_AUDIO) != 0) {
        SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
        return 1;
    }

    /* ... */

    SDL_Quit();

    return 0;
}
```