---
title: Systems
order: 1
description: System declaration, conditions, delta time, and execution shapes
---

# Systems

A system is a plain Jai procedure wrapped in `System(proc, condition)`. The world inspects every system's signature at compile time to build the component registry and generate the dispatch code.

## Declaration

Systems are declared as part of the world type:

```jai
world: World(systems = .[
    System(spawn,   xx Game_Stage.BEGIN),
    System(move,    xx Game_Stage.TICK),
    System(render,  xx Game_Stage.TICK),
    System(cleanup, xx Game_Stage.END),
]);
```

Systems can't be added or removed at runtime — they're baked into the world's type. All systems are conditional: they only run when `progress` is called with a matching condition.

## Conditions

The second parameter to `System` is a 64-bit enum flag value. Define your own stages:

```jai
Game_Stage :: enum_flags u64 {
    BEGIN;
    TICK;
    END;
}
```

When you call `progress`, only systems whose condition matches will execute:

```jai
progress(*world, 0, Game_Stage.BEGIN);    // runs spawn
progress(*world, dt, Game_Stage.TICK);    // runs move, render
progress(*world, 0, Game_Stage.END);      // runs cleanup
```

A system with condition `0` never runs unless you pass `0` as the condition.

## Delta Time

The delta time you pass to `progress` is available inside any system via `delta_time()`:

```jai
move :: (entity: Entity, pos: *Position, vel: Velocity) {
    dt := cast(float32) delta_time();
    pos.x += vel.x * dt;
    pos.y += vel.y * dt;
}
```

`delta_time()` returns `Delta_Time` (which is `float64`).

## System Shapes

### Global Systems

No component arguments — runs once per `progress` call. Use these for setup, teardown, or anything that doesn't iterate entities.

```jai
spawn :: () {
    new_entity(Health.{100}, Position.{0, 0});
    new_entity(Health.{50}, Position.{10, 5});
}

cleanup :: () {
    log("Frame done.");
}
```

### Per-Entity Systems

Take component arguments — runs once for every entity that has **all** the listed components. This is the most common shape.

```jai
// Runs for every entity with Position and Velocity
move :: (pos: *Position, vel: Velocity) {
    dt := cast(float32) delta_time();
    pos.x += vel.x * dt;
    pos.y += vel.y * dt;
}
```

Add `entity: Entity` if you need the owning entity:

```jai
move :: (entity: Entity, pos: *Position, vel: Velocity) {
    // entity is the handle for this specific entity
    pos.x += vel.x * cast(float32) delta_time();
}
```

Components can be passed by value or by pointer. Use pointers when you need to write back.

### Query Systems

Take a `Query(...)` argument for more flexible iteration. See [Queries](/jay_ecs/queries) for the full breakdown.

```jai
physics :: (query: Query(Position, Velocity)) {
    for query {
        pos, vel := components();
        pos.x += vel.x * cast(float32) delta_time();
    }
}
```

### Batched Systems

Take array views (`[]Component`) to process slices of components at once:

```jai
update :: (entities: []Entity, positions: []Position, velocities: []Velocity) {
    for 0..entities.count-1 {
        positions[it].x += velocities[it].x;
    }
}
```

> **Note:** You cannot mix batched (`[]Component`) and non-batched (`*Component`) arguments in the same system.

## Execution Order

Systems run in the order they're declared in the world. There's no automatic dependency analysis or parallelization (yet — it's on the roadmap).

```jai
world: World(systems = .[
    System(first,  xx Game_Stage.TICK),   // runs first
    System(second, xx Game_Stage.TICK),   // runs second
    System(third,  xx Game_Stage.TICK),   // runs third
]);
```

After each per-entity system finishes, destroyed entities are cleaned up before the next system runs.
