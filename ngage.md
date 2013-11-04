# ngage
> A new wave declarative game engine built with JavaScript.

## Table of Contents
1. [Philosophy](#philosophy)
* [Modules](#modules)
* [Entities](#entities)
* [Systems](#systems)
* [Layers](#layers)
* [Scenes](#scenes)
* [Config blocks](#config-blocks)
* [Events](#events)

## Philosophy
`ngage` is inspired by this ideas:

* Clarity is better than cleverness
* Where repair is implausible, fail fast and noisily 


## Modules
A module is a self contained unit of features. It can include all and any of the other declarable elements within the engine.

It's main purpose is to code in the smallest set of chunks possible, to make it both easily testable and remarkably reusable.

Modules can depend on other modules, and each and every module is registered in a central registry at the moment of declaration.

### API

This will create a new module:

```
ngage.createModule('MarioBros', deps);
```
The name of the module has to be a string. It's dependencies (`deps`) can be both a single string or an array of string for module names.

Retrieving a module should be an inexpensive operation and it's done with the `module` function:

```
var marioModule = ngage.module('MarioBros');
```

With this simple set of functions, any module can be declared, injected and expanded.

## Entities
Entities are the common currency in any game. They will be enemies, bullets, platforms, almost anything you can think of. Entities belong in a memory pool. That pool is handled on a Scene basis (because only one scene at the time can be active) and it will try to be as efficient as possible to minimize the footprint of the game.

And entity is plain old JavaScript object, with some special features we'll discuss later on. It's defined by this syntax:

```
ngage.module('MarioBros').entity('Player', function (dep1, dep2, ...) {
    // Anything in here will be part of the entity itself
});
```

An entity will become available for injection right after being declared. Each of the dependencies being injected into an entity are `Systems`. This encourages to build up entities with a composition pattern rather than a hierarchical one, althou Parasitical inheritance can be easily done since other `Entities` can be injected.

An entity by itself will have a just a handful of methods:

```
Player.$getScene();
```
`$getScene` will return a reference to the scene at which the entity is.

```
Player.$getLayers();
```
`$getLayers` will return the layers in which the entity is working.

```
Player.$getSystems();
```
`$getSystems` will return the systems actually working on the entity.

## Systems
A `system` is basically a policy. It can or not delegate it's mechanism to other `systems`, but it always defines an API to which an `entity` can hook to. A `system` is declared as follows:

```
ngage.module('MarioBros').system('SystemName', function (dep1, dep2, ...) {

    var aPrivateMember;
    
    return {
        $get: function (deps1, deps2, ...) {
            // the public system API
            // what this function returns is what will be injected
            // everywhere, except in config blocks            
        },
        
        foo: function () {
            // a function that is accessible only from a config blog
        }
    }
});
```

And it is a regular JavaScript object. The main difference with a `system` and an `entity` is that `system` can act in `entities`, other `systems`, `layers`, and that they are configurable.

Let's take as an example a `$render` system:

```
$render.clear(color);
$render.fill(color, rect);
$render.draw(source, rect, dest);
```

We really do not know what is going on behind `clear`, `fill` and `draw`. At configure-time what those functions do could be changed from a Canvas to a WebGL rendered for instance. What we do know is how to use them. Now let's take a look at how we could use this `$render` system in a `Sprite` system:

```
ngage.module('Core').system('Sprite', function ($render, $pool, $uuid) {
    // let's create a memory pool with the default size;
    var sprites = $pool.create();
    
    return {
       create: function () {
           // lets request memory and assign an uuid to it
           var sprite = sprites.request();
           sprite.id = $uuid.create();
           
           // some functions
           sprite.render = function () {
               // iterate thru all the frames
               // in this case a frame is a rect
               this.frames.forEach(function (f) {
                   $render.draw(this.source, f, this.position);
               }.bind(this));
           }
           
           return sprite;
       }
    }
});
```

As you can see, this `system` does not define a `$get` method, so it's not configurable. `ngage` will complain if you try to inject it into a `config` block.

Other possible uses for systems are data storage common to multiple entities, common functionality, or third-party integrations.

## Layers
Layers are sets of objects and systems that work together in an isolated manner. A layer in your game could be NPC's with whom the user has no interaction whatsoever, or it could be used to separate multiple set of enemies so that they do not interact with other sets but they all interact with the player.

To create a layer, we use the function `layer` as follows:

```
ngage.module('MarioBros').layer('Level', function (deps1, deps2, ...) {
    // Layer code
});
```

Layers provide a public API to attach both systems and entities:

```
Level.attach(Entity, Priority);
```
`.attach` will include an entity or system in it's lifecyle with the given priority.

```
Level.dettach(Entity);
```
`.dettach` will remove an entity or system from it's lifecycle.

The priority during attachment will make sure certain entity or system gets executed before or after others.

## Scenes
Scenes are a handful of layers thrown together in a particular order. They can represent anything that you might need, from a credit's screen to a boss level, or even a menu.

### API
A scene can be defined by using the `scene` method:

```
ngage.module('MarioBros').scene('FirstLevel', function (layer1, layer2, ...) {
    // Level logic
    
    this.configure = function () {
        // spawn layers in a certain order
    };
});
```

There has to be either a `configure` function that spawns the layers in the appropriate order or returns an ordered array of the layer objects to be spawned, or a `configure` array of layer objects in the order to be spawned.

## Config
A config block is simply a place in where to configure a given set of systems.

Config blocks get executed before the digest loop kicks in.

## Events
Any `entity` can `$emit` an event to all other `entities` in it's containing layer, or it can `$broadcast` something to every single `entity` across `layers` in the current `scene`.

Suppose the Player dies. This could either cause every other `entity` in the `layer` to slow down until the player respawns, or it could actually cause all the `scene` to become gray and kick in a respawn effect that happens on a separate `layer`.

