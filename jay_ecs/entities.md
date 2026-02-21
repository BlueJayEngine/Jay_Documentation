---
title: Entities
order: 2
description: Entity creation, component access, mutation, and destruction
---

# Entities

An entity is a lightweight handle — a `u32` index paired with a `u32` generation counter. The generation prevents stale handles from accessing recycled slots.

```jai
Entity :: struct {
    index: u32;
    generation: u32;
}
```

## Creating Entities

Pass component values to `new_entity`. Any struct is a valid component.

```jai
Health   :: struct { value: s32 = 100; }
Position :: struct { x: float32; y: float32; }

// Inside a system (world is in context):
entity := new_entity(Position.{0, 0}, Health.{});
```

You can also pass a bare `Type` instead of a value — it's treated as the default-initialized value of that type:

```jai
// These are equivalent:
new_entity(Health.{});
new_entity(Health);
```

## Getting Components

`get` returns a pointer to a component on an entity. Use this for random access when you have an entity handle.

```jai
hp := get(entity, Health);
log("Health: %", hp.value);
```

> **Note:** Random access through `get` is significantly slower than iterating via systems or queries. It walks the archetype storage to find the entity. Use it for entity-to-entity lookups, not bulk processing.

## Setting Components

`set` adds a component or replaces its value. If the entity doesn't have the component yet, it moves to a new archetype.

```jai
// Add or replace
set(entity, Health.{50});

// Add multiple at once
set(entity, Health.{50}, Position.{10, 0});
```

If the entity already has the component in the same archetype, the value is updated in place (no archetype move).

## Removing Components

`erase` removes components from an entity. The entity moves to a smaller archetype.

```jai
erase(entity, Health);
```

## Destroying Entities

`destroy` marks an entity for removal. It's cleaned up after the current system finishes.

```jai
destroy(entity);
```

The entity's index is recycled and the generation counter increments, so any stale handles pointing to the old entity will fail validation.

## Inspecting Entities

`dump` prints an entity's state and all its component values to the log:

```jai
dump(entity);
// Entity_0 (generation: 0, state: ...): [Health.{value = 100}, Position.{x = 0, y = 0}]
```

## Validity Check

Check if an entity handle is still valid (hasn't been destroyed and recycled):

```jai
if is_valid(world, entity) {
    // safe to use
}
```

## Inside vs Outside Systems

Inside a system, entity operations use the world from context — no need to pass it explicitly:

```jai
// Inside a system:
entity := new_entity(Health.{}, Position.{});
set(entity, Health.{50});
hp := get(entity, Health);
erase(entity, Position);
destroy(entity);
dump(entity);
```

Outside a system (or when you have multiple worlds), pass the world explicitly. Arguments are passed as array views:

```jai
// Outside a system:
entity := new_entity(*world, .[Health.{}, Position.{}]);
set(*world, entity, .[Health.{50}]);
hp := get(*world, entity, Health);
erase(*world, entity, .[Position]);
destroy(*world, entity);
dump(*world, entity);
```

> Using the context-based versions outside a system will log an error and do nothing.
