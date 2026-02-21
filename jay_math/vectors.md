---
title: Vectors
order: 1
description: Parametric vector types with SIMD-accelerated operations
---

# Vectors

`Vector(N, T, AXES)` is a parametric struct that generates named axis fields from the `AXES` parameter, sub-vector overlays (e.g. `.xy`, `.xyz`), and all permutations of mixed scalar/vector constructors — all at compile time via `#insert`.

## Type Aliases

| float32 | float64 | s32 | s64 |
| ------- | ------- | --- | --- |
| `Vec2` `Vec3` `Vec4` | `Vec2d` `Vec3d` `Vec4d` | `Point2` `Point3` `Point4` | `Point2d` `Point3d` `Point4d` |

Default axes are `x`, `y`, `z`, `w`. Float vectors get SIMD-accelerated operators; integer vectors use scalar fallback.

## Construction

```jai
// Struct literal
a := Vec3.{1, 2, 3};

// From scalar (broadcast)
b := Vec3.new(5.0);  // {5, 5, 5}

// Mixed scalar/vector constructors (auto-generated)
c := Vec4.new(a.xy, 0.0, 1.0);   // from Vec2 + two scalars
d := Vec4.new(a, 1.0);            // from Vec3 + scalar
```

## Fields and Overlays

Named fields are generated from the `AXES` parameter. Sub-vector overlays let you access contiguous slices as smaller vectors:

```jai
v := Vec4.{1, 2, 3, 4};

v.x;    // 1
v.z;    // 3
v.xy;   // Vec2.{1, 2}
v.xyz;  // Vec3.{1, 2, 3}

v.values;  // [4]float32 raw array
```

## Operators

Element-wise arithmetic with SIMD acceleration for float types:

```jai
a := Vec3.{1, 2, 3};
b := Vec3.{4, 5, 6};

a + b;     // {5, 7, 9}
a - b;     // {-3, -3, -3}
a * b;     // {4, 10, 18}
a / b;     // {0.25, 0.4, 0.5}

// Scalar-vector (both directions)
a * 2.0;   // {2, 4, 6}
3.0 + a;   // {4, 5, 6}

// Comparison
a == b;    // false
a != b;    // true
```

## Functions

| Function | Signature | Description |
| -------- | --------- | ----------- |
| `dot` | `(a, b) -> T` | Dot product |
| `length` | `(v) -> T` | Euclidean length |
| `length_sqr` | `(v) -> T` | Squared length (avoids sqrt) |
| `normalize` | `(v, epsilon, fallback) -> T` | Normalize in-place, returns length |
| `lerp` | `(a, b, t) -> Vector` | Linear interpolation |

```jai
a := Vec3.{1, 2, 3};
b := Vec3.{4, 5, 6};

dot(a, b);          // 32.0
length(a);          // 3.7416...
length_sqr(a);      // 14.0
lerp(a, b, 0.5);    // {2.5, 3.5, 4.5}

normalize(*a);       // normalizes a in-place, returns original length
```
