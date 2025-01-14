# Quasar(Veil) Cheatsheet

# **I do not take any guarantee for the Informations provided here since they can change any day depending on Veil.**

This document is supposed to give you a quick overview over the various files you need to compose Particles with Veil and Quasar, it‘s Particle system.

## Terminology:

[Veil](https://github.com/foundryMC/veil) is a Minecraft mod developed by [FoundryMC](https://github.com/foundryMC).

Quasar is their Particle system built into the mod

## Codecs:

Quasar uses so-called Codecs to read it‘s particles from Resource Packs. If you are not familiar with what a codec is, read it up [here](https://gist.github.com/Drullkus/1bca3f2d7f048b1fe03be97c28f87910) or [here](https://docs.fabricmc.net/develop/codecs).

When you don‘t know what a entry should look like, search it up on Veil‘s Github or, if it is a Minecraft Codec, using [linkie](https://linkie.shedaniel.dev/mappings?namespace=mojang_raw&version=1.20.1&search=&translateMode=none)


## Overview

| File | Relative Path | Function | Link to Specification 
| ---                | ---                           | ---                                                               | ---                                                                                                                                  |
| Particle Emitter   | emitters                      | Define  informations for spawning this particle                   | [Github](https://github.com/FoundryMC/Veil/blob/1.20/common/src/main/java/foundry/veil/api/quasar/data/ParticleEmitterData.java#L27) |
| EmitterSettings    | Part of Particle Emitter file | Links emission shape and particle settings                        | [Github](https://github.com/FoundryMC/Veil/blob/1.20/common/src/main/java/foundry/veil/api/quasar/data/EmitterSettings.java#L11)     |
| Particle Shape     | modules/emitter/particle/shape| Defines where relative to the emitter to spawn particles          | [Github](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/java/foundry/veil/api/quasar/emitters/shape)                    |
| Particle settings  | modules/emitter/particle      | Defines a few properties like size and lifetime for each particle | [Github](https://github.com/FoundryMC/Veil/blob/1.20/common/src/main/java/foundry/veil/api/quasar/data/ParticleSettings.java#L26)    |
| Particle Data      | modules/particle_data         | Defines the modules for this particle                             | [Github](https://github.com/FoundryMC/Veil/blob/1.20/common/src/main/java/foundry/veil/api/quasar/data/QuasarParticleData.java#L52)  | 
| Modules            | modules/                      | Control the particles behavior while existing                     | [Github](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/java/foundry/veil/api/quasar/emitters/module)                   |

For examples see the [example Resource pack for Particles.](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/resources/resourcepacks/test_particles/assets/veil/quasar)

For installation instructions see [the Veil wiki](<https://github.com/FoundryMC/Veil/wiki>).

All relative paths go off of `assets/<mod_id>/quasar/`.

### Particle spawning process
```mermaid
flowchart
emitter[Particle Emitter]
data[Particle Module data
=> List of Modules]
pmodules[Particle Modules
Run every frame/tick]
particle[Individual Particle]
shape[Shape]
modules[Modules]
moduledata[Module Data
=> Parameters for the Module]
settings[Particle Settings
=> Lifetime and size]
emitter--spawns using-->shape-->particle
emitter--supplies-->settings-->particle
moduledata-->modules
emitter--uses-->data--to apply-->modules--to -->particle
particle-->pmodules-->particle

```

### Particle Emitter

A particle emitter is, as the name implies, a point in Space that spawns Particles. It defines some additional data about itself and the data the spawned Particle(s) will consist of.

### Particle Emitter Shape

When multiple particles are spawned from the Emitter, it defines the shape in which to spawn them. [See the Github for a list of available ones](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/java/foundry/veil/api/quasar/emitters/shape). Every Emitter has a Vec3f (aka: a list of three decimal numbers) for its size and rotation.

### Particle Data

The Particle Data defines a list of Particle Modules that should be attached to this Particle when spawned. 

### Particle Modules

A module can do various things when applied to a particle, for example make it have Physics or render a trail behind it.

[List of all particle modules](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/java/foundry/veil/api/quasar/data/module)

There are the following base types of Modules on which all other modules are based on:

- [Update modules](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/java/foundry/veil/api/quasar/data/module/update)
- [Render Modules](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/java/foundry/veil/api/quasar/data/module/render)
- [Init Modules](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/java/foundry/veil/api/quasar/data/module/init)

Additionally to these base modules, there are also [Force](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/java/foundry/veil/api/quasar/data/module/force) and [Collision](https://github.com/FoundryMC/Veil/tree/1.20/common/src/main/java/foundry/veil/api/quasar/data/module/collision) Modules which are shipped with Veil.

These modules are ran, depending on the module, while the particle exists or when it is spawned. A rendering module will be ran every frame, whereas init modules will, as the name implies, only run when the Particle is initialized, Update modules when it is ticked

When using them, you create a new instance of this module with the arguments you give it. Please keep in mind each Module can have it‘s own, unique parameters, for example the Trail Module allows you to define color, width and texture.
