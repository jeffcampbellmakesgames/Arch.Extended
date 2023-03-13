# Arch.Extended
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg?style=for-the-badge)](https://GitHub.com/Naereen/StrapDown.js/graphs/commit-activity)
[![Nuget](https://img.shields.io/nuget/v/Arch?style=for-the-badge)](https://www.nuget.org/packages/Arch.System/)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg?style=for-the-badge)](https://opensource.org/licenses/Apache-2.0)
![C#](https://img.shields.io/badge/c%23-%23239120.svg?style=for-the-badge&logo=c-sharp&logoColor=white)

Extensions for [Arch](https://github.com/genaray/Arch) with some useful features like Systems, Source Generator and Utils.
 
- 🛠️ **_Productive_** >  Adds some useful tools and features to the main repository!
- ☕️ **_SIMPLE_** >  Works easily, reliably and understandably!
- 💪 _**MAINTAINED**_ > It's actively being worked on, maintained, and supported!
- 🚢 _**SUPPORT**_ > Supports .NetStandard 2.1, .Net Core 6 and 7 and therefore you may use it with Unity or Godot!

Download the packages and get started today! 
```console
dotnet add package Arch.System --version 1.0.2
dotnet add package Arch.System.SourceGenerator --version 1.0.8
dotnet add package Arch.EventBus --version 1.0.0
```

# Features & Tools
- ⚙️ **_[Systems](https://github.com/genaray/Arch.Extended/wiki/Systems-API)_** > By means of systems, it is now easy to organize, reuse and arrange queries. 
- ✍️ **_[Source Generator](https://github.com/genaray/Arch.Extended/wiki/Source-Generator)_** > Declarative syntax using attributes and source generator, let your queries write themselves! 
> Check the links and the [Wiki](https://github.com/genaray/Arch.Extended/wiki)! 

# Full code sample

With this package you are able to write and group queries and systems for Arch automatically.
And all this with the best possible performance.

The tools can be used independently of each other.

```cs
// Components ( ignore the formatting, this saves space )
public struct Position{ float X, Y };
public struct Velocity{ float Dx, Dy };

// BaseSystem provides several usefull methods for interacting and structuring systems
public class MovementSystem : BaseSystem<World, float>
{
    public MovementSystem(World world) : base(world) {}
    
    // Generates a query and calls that one automatically on BaseSystem.Update
    [Query]
    public void Move([Data] in float time, ref Position pos, ref Velocity vel)
    {
        pos.X += time * vel.X;
        pos.Y += time * vel.Y;
    }
    
    // Generates and filters a query and calls that one automatically on BaseSystem.Update in order
    [Query]
    [All<Player, Mob>, Any<Idle, Moving>, None<Alive>]  // Attributes also accept non generics :) 
    public void ResetVelocity(ref Velocity vel)
    {
        vel = new Velocity{ X = 0, Y = 0 };
    }
}

public class Game 
{
    public static void Main(string[] args) 
    {     
        var deltaTime = 0.05f; // This is mostly given by engines, frameworks
        
        // Create a world and a group of systems which will be controlled 
        var world = World.Create();
        var _systems = new Group<float>(
            new MovementSystem(world),   // Run in order
            new MyOtherSystem(...),
            ...
        );
      
        _systems.Initialize();                  // Inits all registered systems
        _systems.BeforeUpdate(in deltaTime);    // Calls .BeforeUpdate on all systems ( can be overriden )
        _systems.Update(in deltaTime);          // Calls .Update on all systems ( can be overriden )
        _systems.AfterUpdate(in deltaTime);     // Calls .AfterUpdate on all System ( can be overriden )
        _systems.Dispose();                     // Calls .Dispose on all systems ( can be overriden )
    }
}
```
