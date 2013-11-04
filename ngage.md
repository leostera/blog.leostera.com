# ngage
> A new wave declarative game engine built with JavaScript.

## Table of Contents
1. Philosophy
* Modules
* Entities
* Systems
* Layers
* Scenes
* Config blocks

## Philosophy
`ngage` is inspired by this rules:

* Clarity is better than cleverness
* Where repair is implausible, fail fast and noisily 


## Module
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
var marioModule = ngage.module('MarioBros);
```

With this simple set of functions, any module can be declared, injected and expanded.

## Entities
Entities are the common currency in any game. They will be enemies, bullets, platforms, 