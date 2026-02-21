---
title: Queries
order: 3
description: Query patterns — for-each, iterators, random access, and batched arrays
---

# Queries

Queries give systems flexible access to component data. A `Query(...)` matches all archetypes that contain the listed components and provides multiple ways to iterate them.

## For-Each Loop

The simplest pattern. Iterate the query directly — `components()` returns pointers to each component for the current entity:

```jai
physics :: (query: Query(Position, Velocity, Health)) {
    for query {
        pos, vel, hp := components();
        pos.x += vel.x * cast(float32) delta_time();
        pos.y += vel.y * cast(float32) delta_time();
    }
}
```

`it` is the current `Entity`, `it_index` is the flat index across all matching archetypes.

You can `remove` inside the loop to destroy the current entity:

```jai
kill_dead :: (query: Query(Health)) {
    for query {
        hp := components();
        if hp.value <= 0  remove it;
    }
}
```

## Iterator Access

Use `get_iter` to get a typed iterator for a single component. Useful when you want to process each component stream independently:

```jai
update :: (query: Query(Position, Velocity)) {
    positions  := get_iter(query, Position);
    velocities := get_iter(query, Velocity);

    for *pos, index: positions {
        pos.x += velocities[index].x * cast(float32) delta_time();
        pos.y += velocities[index].y * cast(float32) delta_time();
    }
}
```

Iterators support:
- `for` loop (by value or `*` for pointer)
- `[]` random access by flat index
- `[]=` write by flat index
- `.count` for total element count

## Random Access

Iterators support `[]` indexing for random access across all matching archetypes:

```jai
swap_example :: (query: Query(Position)) {
    positions := get_iter(query, Position);

    if positions.count >= 2 {
        // Read
        first := positions[0];

        // Write
        positions[0] = positions[positions.count - 1];
    }
}
```

This uses binary search over archetype offsets internally, so it's slower than sequential iteration but faster than entity-level `get()`.

## Batched Arrays

Systems can take array views (`[]Component`) to process slices of components at a time. The ECS feeds fixed-size batches from each archetype:

```jai
update_batched :: (
    entities: []Entity,
    positions: []Position,
    velocities: []Velocity,
) {
    for 0..entities.count-1 {
        positions[it].x += velocities[it].x;
        positions[it].y += velocities[it].y;
    }
}
```

> **Note:** You cannot mix batched (`[]Component`) and non-batched (`*Component` / `Component`) arguments in the same system.

## Choosing a Pattern

| Pattern | Use when |
| ------- | -------- |
| **Per-entity system** | Simple per-entity logic, one component set |
| **For-each query** | Need multiple component sets or `remove` |
| **Iterator** | Processing component streams independently |
| **Random access** | Need to index into components by position |
| **Batched** | SIMD-friendly bulk processing |

Per-entity systems and for-each queries are the most common. Iterators and batched access are there when you need tighter control over the access pattern.
