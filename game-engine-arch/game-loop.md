# The Game Loop and Real-Time Simulation

1.
    1. Games are real-time, dynamic, interactive computer simulations.
    2. As such, *time* plays an incredibly important role in any electronic game.
    3. There are many different kinds of time to deal with in a game engine—real time, game time, the local timeline of an animation, the actual CPU cycles spent within a particular function, and the list goes on.
    4. Every engine system might define and manipulate time differently.
    5. We must have a solid understanding of all the ways time can be used in a game.
    6. In this chapter, we'll take a look at how real-time, dynamic simulation software works and explore the common ways in which time plays a role in such a simulation.

## 8.1 The Rendering Loop

1.
    1. In a graphical user interface (GUI), of the sort found on a Windows PC or a Macintosh, the majority of the screen's contents are static.
    2. Only a small part of any one window is actively changing appearance at any given moment.
    3. Because of this, graphical user interfaces have traditionally been drawn on-screen via a technique known as *rectangle invalidation*, in which only the small portions of the screen whose contents have actually changed are redrawn.
    4. Older 2D video games used similar techniques to minimize the number of pixels that needed to be drawn.

2.
    1. Real-time 3D computer graphics are implemented in an entirely different way.
    2. As the camera moves about in a 3D scene, the *entire contents* of the screen or window change continually, so the concept of invalid rectangles no longer applies.
    3. Instead, an illusion of motion and interactively is produced in much the same way that a movie produces it—by presenting the viewer with a series of still images in rapid succession.

3.
    1. Obviously, producing a rapid succession of still images on-screen requires a loop.
    2. In a real-time rendering application, this is sometimes known as the *render loop*.
    3. At its simplest, a rendering loop is structured as follows:

```cpp
while (!quit)
{
    // Update the camera transform based on interactive
    // inputs or by following a predefined path.
    updateCamera();

    // Update positions, orientations and any other relevant visual state of any dynamic elements in the scene.
    updateSceneElements();

    // Render a still frame into an off-screen frame
    // buffer known as the "back buffer".
    renderScene();

    // Swap the back buffer with the front buffer, making
    // the most recently rendered image visible
    // on-screen. (Or, in windowed mode, copy (blit) the
    // back buffer's contents to the front buffer.)
    swapBuffers();
}
```

## 8.2 The Game Loop

1.
    1. A game is composed of many interacting subsystems, including device I/O, rendering, animation, collision detection and resolution, optional rigid body dynamics simulation, multiplayer networking, audio, and the list goes on.
    2. Most game engine subsystems require periodic *servicing* while the game is running.
    3. However, the *rate* at which these subsystems need to be serviced varies from subsystem to subsystem.
    4. Animation typically needs to be updated at a rate of 30 or 60 Hz, in synchronization with the rendering system.
    5. However, a dynamics (phyics) simulation may actually require more frequent updates (e.g. 120 Hz).
    6. Higher-level systems, like AI, might only need to be serviced once or twice per second, and they needn't necessarily be synchronized with the rendering loop at all.

2.
    1. There are a number of ways to implement the periodic updating of our game engine subsystems.
    2. We'll explore some of the possible architectures in a moment.
    3. But for the time being, let's stick with the simplest way to update our engine's subsystems—using a single loop to update everything.
    4. Such a loop is often called the *game loop*, because it is the master loop that services every subsystem in the engine.

### 8.2.1 A Simple Example: Pong

1.
    1. Pong is a well-known genre of table tennis video games that got its start in 1958, in the form of an analog computer game called *Tennis for Two*, created by William A. Higinbotham at the Brookhaven National Laboratory and displayed on an oscilloscope.
    2. The genre is best known by its later incarnations on digital computers—the Magnavox Oddysey game *Table Tennis* and the Atari arcade game *Pong*.

2.
    1. In pong, a ball bounces back and forth between two movable vertical paddles and two fixed horizontal walls.
    2. The human players control the positions of the paddles via control wheels.
    3. (Modern re-implementations allow control via a joystick, the keyboard or some other human interface device.)
    4. If the ball passes by a paddle without striking it, the other team wins the point and the ball is reset for a new round of play.

3.
    1. The following pseudocode demonstrates what the game loop of a pong game might look like at its core:

```cpp
void main() // Pong
{
    initGame();

    while (true) // game loop
    {
        readHumanInterfaceDevices();

        if (quitButtonPressed())
        {
            break; // exit the game loop
        }

        movePaddles();

        moveBall();

        collideAndBounceBall();

        if (ballImpactedSide(LEFT_PLAYER))
        {
            incrementScore(RIGHT_PLAYER);
            resetBall();
        }
        else if (ballImpactedSide(RIGHT_PLAYER))
        {
            incrementScore(LEFT_PLAYER);
            resetBall();
        }

        renderPlayfield();
    }
}
```

4.
    1. Clearly this example is somewhat contrived.
    2. The original pong games were certainly not implemented by redrawing the entire screen at a rate of 30 frames per second.
    3. Back then, CPUs were so slow that they could barely muster the power to draw two lines for the paddles and a box for the ball in real time.
    4. Specialized 2D sprite hardware was often used to draw moving objects on-screen.
    5. However, we're only interested in the concepts here, not the implementation details of the original Pong.

5.
    1.