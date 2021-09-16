# Chapter 2
## Game Objects and 2D Graphics

1.
    1. **Most games have many different characters and other objects, and an important decision is how to represent these objects.**
    2. **This chapter first covers different methods of object representation.**
    3. **Next, it continues the discussion of 2D graphics techniques by introducing sprites, sprite animations, and scrolling backgrounds.**
    4. **This chapter culminates with a side-scrolling demo that applies the covered techniques.**

1.
    1. The *Pong* game created in Chapter 1 does not use separate classes to represent the wall, paddles, and ball.
    2. Instead, the `Game` class uses member variables to track the position and velocity of the different elements of the game.
    3. While this can work for a very simple game, it's not a scalable solution.
    4. The term **game object** refers to anything in the game world that updates, draws, or both updates and draws.
    5. There are several methods to represent game objects.
    6. Some games employ hierarchies, others employ composition, and still others utilise more complex methods.
    7. Regardless of the implementation, a game needs some way to track and update these game objects.

## 1. Types of Game Objects

1.
    1. A common type of game object is one that's both updated every frame during the "update game world" phase of the loop and drawn every frame during the "generate outputs" phase.
    2. Any character, creature, or otherwise movable object falls under this umbrella.
    3. For example, in *Super Mario Bros.*, Mario, any enemies, and all the dynamic blocks are game objects that the game both updates and draws.

2.
    1. Developers sometimes use the term **static object** for game objects that draw but don't update.
    2. These objects are visible to the player but never need to update.
    3. An example of this is a building in the background of a level.
    4. In most games, a building doesn't move or attack the player but is visible onscreen.

3.
    1. A camera is an example of a game object that updates but doesn't draw to the screen.
    2. Another example is a **trigger**, which causes something to occur based on another object's intersection.
    3. For instance, a horror game might want to have zombies appear when the player approaches a door.
    4. In this case, the level designer would place a trigger object that can detect when the player is near and *trigger* the action to spawn the zombie.
    5. One way to implement a trigger is as an invisible box that updates every frame to check for intersection with the player.

## 2. Game Object Models

1.
    1. There are numerous **game object models**, or ways to represent game objects.
    2. This section discusses some types of game object models and the trade-offs between these approaches.

## 3. Game Objects as a Class Hierarchy

1.
    1. One game object model approach is to declare game objects in a standard object-oriented class hierarchy, which is sometimes called a **monolithic class hierarchy** because all game objects inherit from one base class.

2.
    1. To use this object model, you first need a base class:

```cpp
class Actor
{
    public:
        // Called every frame to update the Actor
        virtual void Update(float deltaTime);

        // Called every frame to draw the Actor
        virtual void Draw();
};
```

3.
    1. Then, different characters have different subclasses:

```cpp
class PacMan : public Actor
{
public:
    void Update(float deltaTime) override;
    void Draw() override;
}
```

4.
    1. Similarly, you could declare other subclasses of `Actor`.
    2. For example, there may be a `Ghost` class that inherits from `Actor`, and then each individual ghost could have its own class that inherits from `Ghost`.
    3. Figure 2.1 illustrates this style of game object class hierarchy.

[Figure 2.1]
Partial class hierarchy for *Pac-Man*

5.
    1. A disadvantage of this approach is that it means that every game object must have all the properties and functions of the base game object (in this case, Actor).
    2. For example, this assumes that every `Actor` can update and draw.
    3. But as discussed, there may be objects that aren't visible, and thus calling Draw on these objects is a waste of time.

6.
    1. The problem becomes more apparent as the functionality of the game increases.
    2. Suppose many of the acors in the game—but not all of them—need to move.
    3. In the case of *Pac-Man*, the ghosts and Pac-Man need to move, but the pellets do not.
    4. One approach is to place the movement code inside `Actor`, but not every subclass will need this code.
    5. Alternatively, you could extend the hierarchy with a new `MovingActor` that exists between `Actor` and any subclasses that need movement.
    6. However, this adds more complexity to the class hierarchy.

7.
    1. Furthermore, having one big class hierarchy can cause difficulties when two sibling classes later need to have features shared between them.
    2. For instance, a game in the vein of *Grand Theft Auto* might have a base `Vehicle` class.
    3. From this class, it might make sense to create two subclasses: `LandVehicle` (for vehicles that traverse land) and `WaterVehicle` (for water-based vehicles like boats).

8.
    1. But what happens if one day a designer decides to add an amphibious vehicle?
    2. It may be tempting to create a new subclass caled `AmphibiousVehicle` that inherits from both `LandVehicle` and `WaterVehicle`.
    3. However, this requires use of multiple inheritance and, furthermore, means that `AmphibiousVehicle` inherits from `Vehicle` along two different paths.
    4. This type of hierarchy, called **diamond inheritance**, can cause issues because the subclass might inherit multiple versions of a virtual function.
    5. For this reason, it's recommended that we avoid diamond hierarchies.

## 4. Game Objects with Components

1.
    1. Instead of using a monolithic hierarchy, many games instead use a **component-based** game object model.
    2. This model has become increasingly popular, especially because the Unity game engine uses it.
    3. In this approach, there is a game object class, but there are no subclasses of the game object.
    4. Instead, the game object class *has-a* collection of component objects that implement needed functionality.

2.
    1. For example, in the monolithic hierarchy we looked at earlier, `Pinky` is a subclass of `Ghost`, which is a subclass of `Actor`.
    2. However, in a component-based model, Pinky is a `GameObject` instance containing four components: `PinkyBehaviour`, `CollisionComponent`, `TransformComponent`, and `DrawComponent`.
    3. Figure 2.2 shows this relationship.

3.
    1. Each of these components has the specific properties and functionality needed for that component.
    2. For example, `DrawComponent` handles drawing the object to the screen, and `TransformComponent` stores the position and transformation of an object in the game world.

4.
    1. One way to implement a component object model is with a class hierarchy for components.
    2. This class hierarchy generally has a very shallow depth.
    3. Given a base `Component` class, `GameObject` then simply has a collection of components:

```cpp
class GameObject
{
    public:
        void AddComponent(Component* comp);
        void RemoveComponent(Component* comp);
    private:
        std::unordered_set<Component*> mComponents;
}
```

5.
    1. Notice that `GameObject` contains only functions for adding and removing components.
    2. This makes systems that track different types of components necessary.
    3. For example, every `DrawComponent` might register with a `Renderer` object so that when it is time to draw the frame, the `Renderer` is aware of all the active `DrawComponent`s.

6.
    1. One advantage of the component-based game object model is that it's easier to add functionality only to the specific game objects that require it.
    2. Any object that needs to draw can have a `DrawComponent`, but objects that don't simply don't have one.

7.
    1. However, a disadvantage of pure component systems is that dependencies between components in the same game object are not clear.
    2. For instance, it's likely that the `DrawComponent` needs to know about the `TransformComponent` in order to know where the object should draw.
    3. This means that the `DrawComponent` likely needs to ask the owning `GameObject` about its `TransformComponent`.
    4. Depending on the implementation, the querying can become a noticeable performance bottleneck.

## 5. Game Objects as a Hierarchy with Components

1.
    1. The game object model used in this book is a hybrid of the monolithic hierarchy and the component object models.
    2. This is, in part, inspired by the game object model used in Unreal Engine 4.
    3. There is a base `Actor` class with a handful of virtual functions, but each actor also has a vector of components.
    4. Listing 2.1 shows the declaration of the `Actor` class, with some getter and setter functions omitted.