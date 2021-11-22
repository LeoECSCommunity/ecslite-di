# Dependency injection for LeoEcsLite C# Entity Component System framework
Dependency injection for [LeoECS Lite](https://github.com/Leopotam/ecslite).

> Tested on unity 2020.3 (not dependent on it) and contains assembly definition for compiling to separate assembly file for performance reason.

> **Important!** Don't forget to use `DEBUG` builds for development and `RELEASE` builds in production: all internal error checks / exception throwing works only in `DEBUG` builds and eleminated for performance reasons in `RELEASE`.

# Table of content
* [Socials](#socials)
* [Installation](#installation)
    * [As unity module](#as-unity-module)
    * [As source](#as-source)
* [Integration to startup](#integration-to-startup)
* [Attributes](#attributes)
    * [EcsWorldAttribute](#ecsworldattribute)
    * [EcsPoolAttribute](#ecspoolattribute)
    * [EcsFilterAttribute](#ecsfilterattribute)
    * [EcsSharedAttribute](#ecssharedattribute)
    * [EcsInjectAttribute](#ecsinjectattribute)
* [License](#license)

# Socials
[![discord](https://img.shields.io/discord/404358247621853185.svg?label=enter%20to%20discord%20server&style=for-the-badge&logo=discord)](https://discord.gg/5GZVde6)

# Installation

## As unity module
This repository can be installed as unity module directly from git url. In this way new line should be added to `Packages/manifest.json`:
```
"com.leopotam.ecslite.di": "https://github.com/Leopotam/ecslite-di.git",
```
By default last released version will be used. If you need trunk / developing version then `develop` name of branch should be added after hash:
```
"com.leopotam.ecslite.di": "https://github.com/Leopotam/ecslite-di.git#develop",
```

## As source
If you can't / don't want to use unity modules, code can be downloaded as sources archive from `Releases` page.

# Integration to startup
```csharp
var systems = new EcsSystems (new EcsWorld ());
systems
    .Add (new System1 ())
    .AddWorld (new EcsWorld (), "events")
    // ...
    // Inject() method should be placed after
    // all systems/worlds registration and
    // before Init().
    .Inject ()
    .Init ();
```

# Attributes

## EcsWorldAttribute
```csharp
class TestSystem : IEcsRunSystem {
    [EcsWorld]
    // field will be injected with default world instance.
    readonly EcsWorld _defaultWorld = default;
    
    [EcsWorld ("events")]
    // field will be injected with "events" world instance.
    readonly EcsWorld _eventsWorld = default;

    public void Run (EcsSystems systems) {
        // all injected fields can be used here.
    }
}
```

## EcsPoolAttribute
```csharp
class TestSystem : IEcsRunSystem {
    [EcsPool]
    // field will be injected with pool from default world instance.
    readonly EcsPool<C1> _c1Pool = default;
    
    [EcsPool ("events")]
    // field will be injected with pool from "events" world instance.
    readonly EcsPool<C1> _c1EventsPool = default;

    public void Run (EcsSystems systems) {
        // all injected fields can be used here.
    }
}
```

## EcsFilterAttribute
```csharp
class TestSystem : IEcsRunSystem {
    [EcsFilter (typeof (C1))]
    // field will be injected with filter (C1 included)
    // from default world instance.
    readonly EcsFilter _filter1 = default;
    
    [EcsFilter (typeof (C1), typeof (C2))]
    // field will be injected with filter (C1,C2 included)
    // from default world instance.
    readonly EcsFilter _filter2 = default;
    
    [EcsFilter (typeof (C1))]
    [EcsFilterExclude (typeof (C2))]
    // field will be injected with filter (C1 included, C2 excluded)
    // from default world instance.
    readonly EcsFilter _filter11 = default;
    
    [EcsFilter ("events", typeof (C1))]
    [EcsFilterExclude (typeof (C2))]
    // field will be injected with filter (C1 included, C2 excluded)
    // from "events" world instance.
    readonly EcsFilter _eventsFilter11 = default;

    public void Run (EcsSystems systems) {
        // all injected fields can be used here.
    }
}
```

## EcsSharedAttribute
```csharp
class TestSystem : IEcsRunSystem {
    [EcsShared]
    // field will be injected with GetShared() instance from EcsSystems.
    readonly Shared _shared = default;

    public void Run (EcsSystems systems) {
        // all injected fields can be used here.
    }
}
```

## EcsInjectAttribute
```csharp
systems
    .Add (new TestSystem ())
    .Inject (new CustomData1 (), new CustomData2 ())
    .Init ();
// ...
class TestSystem : IEcsRunSystem {
    [EcsInject]
    // field will be injected with instance from EcsSystems.Inject() call.
    readonly CustomData1 _custom1 = default;
    
    [EcsInject]
    // field will be injected with instance from EcsSystems.Inject() call.
    readonly CustomData2 _custom2 = default;

    public void Run (EcsSystems systems) {
        // all injected fields can be used here.
    }
}
```

# License
The software is released under the terms of the [MIT license](./LICENSE.md).

No personal support or any guarantees.
