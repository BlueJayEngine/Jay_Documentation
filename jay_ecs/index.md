---
title: Jay ECS
order: 0
description: Archetype-based Entity Component System for Jai
---

# Jay ECS

[Jay_ECS](https://github.com/BlueJayEngine/Jay_ECS) is an archetype-based Entity Component System built for Jai. Components are plain structs, systems are plain procedures, and the world wires them together at compile time — no macros, no registration boilerplate.

```jai
#import "Jay_ECS";
```

---

## Quick Start

```jai
#import "Jay_ECS";
#import "Basic";

// Components are plain structs
Health    :: struct { value: s32 = 100; }
Position  :: struct { x: float32; y: float32; }
Velocity  :: struct { x: float32; y: float32; }

// Game stages — controls which systems run when
Game_Stage :: enum_flags u64 {
    BEGIN;
    TICK;
    END;
}

// Systems are plain procedures
spawn :: () {
    new_entity(Position.{0, 0}, Velocity.{1, 0.5}, Health.{});
    new_entity(Position.{10, 5}, Velocity.{-1, 0}, Health.{50});
}

move :: (entity: Entity, pos: *Position, vel: Velocity) {
    dt := cast(float32) delta_time();
    pos.x += vel.x * dt;
    pos.y += vel.y * dt;
}

cleanup :: () {
    log("Done.");
}

main :: () {
    world: World(systems = .[
        System(spawn,   xx Game_Stage.BEGIN),
        System(move,    xx Game_Stage.TICK),
        System(cleanup, xx Game_Stage.END),
    ]);

    progress(*world, 0, Game_Stage.BEGIN);

    for 0..59 {
        progress(*world, 0.016, Game_Stage.TICK);
    }

    progress(*world, 0, Game_Stage.END);
    free(world);
}
```

---

## Core Concepts

### Component

Any Jai struct. No base class, no registration — if it's a struct, it's a component.

```jai
Health   :: struct { value: s32 = 100; }
Position :: struct { x: float32; y: float32; }
Velocity :: struct { x: float32; y: float32; }
```

Default initializers work. If a component has `value: s32 = 100`, new entities get that default unless you provide a value.

### Entity

A lightweight handle — just an index and a generation counter. Generations prevent stale handles from accessing recycled slots.

```jai
Entity :: struct {
    index: u32;
    generation: u32;
}
```

You never construct entities by hand. Use `new_entity()` to create them and the ECS manages the rest.

### World

The world holds all entities, archetypes, and systems. Systems are declared at compile time as part of the world's type:

```jai
world: World(systems = .[
    System(move,    xx Game_Stage.TICK),
    System(render,  xx Game_Stage.TICK),
]);
```

The world automatically builds a **component registry** by inspecting every system's procedure signature at compile time. Only components that appear in at least one system are tracked.

### System

A system is a plain procedure wrapped in `System(proc, condition)`. The condition is a 64-bit enum flag that controls when the system runs.

Systems come in two shapes:

- **Global systems** — no component arguments, run once per `progress()` call
- **Per-entity systems** — take component arguments, run once for every matching entity

```jai
// Global: runs once
spawn :: () { new_entity(Health.{}, Position.{}); }

// Per-entity: runs for each entity that has Position and Velocity
move :: (entity: Entity, pos: *Position, vel: Velocity) {
    pos.x += vel.x * cast(float32) delta_time();
}
```

Systems can also accept `Query` arguments for more advanced iteration patterns.

> Full reference: [Systems](/jay_ecs/systems)

### Archetype

Entities with the same set of components are stored together in an **archetype**. When you add or remove a component, the entity moves to a different archetype. This is handled automatically — you never interact with archetypes directly.

This layout means iteration is cache-friendly: systems walk contiguous arrays of components, not scattered pointers.

---

## Lifecycle

```
progress(*world, delta_time, condition)
```

Call `progress` to advance the world. It runs every system whose condition matches, iterates matching archetypes, and cleans up destroyed entities afterward.

A typical game loop:

```jai
Game_Stage :: enum_flags u64 {
    BEGIN;
    TICK;
    END;
}

progress(*world, 0, Game_Stage.BEGIN);

while running {
    progress(*world, dt, Game_Stage.TICK);
}

progress(*world, 0, Game_Stage.END);
free(world);
```

`delta_time()` is available inside any system — it returns the value you passed to `progress`.

---

## What's Next

- [Systems](/jay_ecs/systems) — conditions, delta time, system shapes
- [Entities](/jay_ecs/entities) — create, get, set, erase, destroy
- [Queries](/jay_ecs/queries) — for-each, iterators, random access, batched arrays
