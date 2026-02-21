---
title: Rotators
order: 3
description: Quaternions, euler angles, radians, and Transform — with full round-trip conversion
---

# Rotators

Three interchangeable rotation representations with full conversion between all of them. Any representation can round-trip through any other.

## Types

### Quat

Unit quaternion. Internally a `Vector(4, float32, .["w", "x", "y", "z"])`.

```jai
q := Quat.{1, 0, 0, 0};   // identity (w=1)
q.w;  q.x;  q.y;  q.z;
```

### Rotator / Euler

Rotation vector in **degrees** — Roll, Pitch, Yaw. `Euler` is an alias for `Rotator`.

```jai
r := Rotator.{roll = 0, pitch = 45, yaw = 90};
```

### Radians

Same layout as `Rotator` but values are in **radians**.

```jai
rad := Radians.{roll = 0, pitch = PI/4, yaw = PI/2};
```

### Transform

TRS struct — translation, rotation, scale. Converts to/from `Mat4`.

```jai
t := Transform.{
    translation = .{1, 2, 3},
    rotation    = Quat.{1, 0, 0, 0},  // identity
    scale       = .{1, 1, 1},
};
```

## Conversions

All conversions are free functions. Matrix extraction uses Shepperd's method for numerical stability.

| From | To | Function |
| ---- | -- | -------- |
| `Quat` | `Mat3` | `to_matrix(quat)` |
| `Quat` | `Mat4` | `to_matrix4(quat)` |
| `Quat` | `Rotator` | `to_rotator(quat)` |
| `Quat` | `Radians` | `to_radians(quat)` |
| `Radians` | `Quat` | `to_quat(rad)` |
| `Radians` | `Mat3` | `to_matrix(rad)` |
| `Radians` | `Mat4` | `to_matrix4(rad)` |
| `Radians` | `Rotator` | `to_rotator(rad)` |
| `Rotator` | `Radians` | `to_radians(rot)` |
| `Mat3` | `Quat` | `to_quat(mat3)` |
| `Mat4` | `Quat` | `to_quat(mat4)` |
| `Mat3` | `Rotator` | `to_rotator(mat3)` |
| `Mat3` | `Radians` | `to_radians(mat3)` |
| `Mat4` | `Rotator` | `to_rotator(mat4)` |
| `Mat4` | `Radians` | `to_radians(mat4)` |
| `Transform` | `Mat4` | `to_matrix(transform)` |
| `Mat4` | `Transform` | `to_transform(mat4)` |

> **Note:** `Rotator` stores degrees. To convert `Rotator` -> `Quat`, go through radians first: `to_quat(to_radians(my_rotator))`.

## Example

```jai
// Quaternion -> matrix -> back to quaternion
q := to_quat(Radians.{0, PI/4, 0});
m := to_matrix(q);           // Mat3
q2 := to_quat(m);            // round-trips cleanly

// Transform -> Mat4
t := Transform.{translation = .{10, 0, 5}};
world := to_matrix(t);       // Mat4 with TRS composition

// Mat4 -> Transform (extracts translation, scale, rotation)
t2 := to_transform(world);
```
