---
title: Jay Math
order: 0
description: Jai-native, SIMD-accelerated math for games and real-time applications
---

# Jay Math

[Jay_Math](https://github.com/BlueJayEngine/Jay_Math) is a Jai-native math module built for games and real-time applications. It's lightweight, SIMD-accelerated at runtime, and designed to feel natural in Jai — no wrappers, no friction.

```jai
#import "Jay_Math";
```

That's it. Everything below is now available.

---

## Quick Start

```jai
#import "Jay_Math";
#import "Basic";

main :: () {
    // Create some vectors
    position := Vec3.{0, 1, 0};
    velocity := Vec3.{5, 0, 3};

    // Move
    dt : float32 = 0.016;
    position = position + velocity * dt;

    // Build a transform matrix
    world := Mat4.identity();
    translate(*world, position);
    rotate(*world, PI / 4, Vec3.{0, 1, 0});
    scale(*world, Vec3.{1, 1, 1});

    print("Position: %, %, %\n", position.x, position.y, position.z);
}
```

---

## Vectors

Vectors are parametric: `Vector(N, T, AXES)` generates named fields, sub-vector overlays, and mixed constructors — all at compile time.

```jai
a := Vec3.{1, 2, 3};
b := Vec3.{4, 5, 6};

a + b;           // {5, 7, 9}
a * 2.0;         // {2, 4, 6}
dot(a, b);       // 32.0
length(a);       // 3.7416...

// Sub-vector overlays
v := Vec4.{1, 2, 3, 4};
v.xy;            // Vec2.{1, 2}
v.xyz;           // Vec3.{1, 2, 3}

// Mixed constructors — auto-generated for every permutation
c := Vec4.new(a.xy, 0.0, 1.0);
d := Vec4.new(a, 1.0);
```

Available types:

| float32 | float64 | s32 | s64 |
| ------- | ------- | --- | --- |
| `Vec2` `Vec3` `Vec4` | `Vec2d` `Vec3d` `Vec4d` | `Point2` `Point3` `Point4` | `Point2d` `Point3d` `Point4d` |

Float vectors use SIMD-accelerated operators. Integer vectors fall back to scalar.

> Full reference: [Vectors](/jay_math/vectors)

---

## Matrices

`Matrix(COL, ROW, T)` with named fields (`_11`, `_12`, ...), flat `values[]` array, and `cells[][]` row-major access. Multiply is FMA-accelerated.

```jai
a := Mat4.identity();
b := Mat4.identity();

a * b;           // matrix multiply (FMA)
a + b;           // element-wise add (SIMD)
inv := inverse(a);
```

Available types:

| float32 | float64 |
| ------- | ------- |
| `Mat2` `Mat3` `Mat4` `Mat4x3` | `Mat2d` `Mat3d` `Mat4d` `Mat4x3d` |

### Transforms: make / set / apply

Every transform operation follows the same three-verb pattern:

- **`make_*`** — create a new matrix from parameters
- **`set_*`** — overwrite the relevant part of an existing matrix
- **verb** (`translate`, `rotate`, `scale`, ...) — multiply the transform into an existing matrix

All have both pointer and value overloads.

```jai
// Build a world matrix step by step
world := Mat4.identity();
translate(*world, Vec3.{10, 0, 5});
rotate(*world, PI / 4, Vec3.{0, 1, 0});
scale(*world, Vec3.{2, 2, 2});

// Or create standalone matrices
t := make_translation(Vec3.{10, 0, 5});
r := make_rotation(PI / 4, Vec3.{0, 1, 0});
s := make_scale(Vec3.{2, 2, 2});

// Orient toward a target
face(*world, direction, up);
```

Available transforms: **translate**, **rotate** (2D angle / 3D axis-angle), **scale**, **shear**, **face**.

> Full reference: [Matrices](/jay_math/matrices)

---

## Rotations

Three interchangeable representations with full round-trip conversion between all of them:

| Type | Unit | Layout |
| ---- | ---- | ------ |
| `Quat` | — | `w, x, y, z` |
| `Rotator` / `Euler` | degrees | `roll, pitch, yaw` |
| `Radians` | radians | `roll, pitch, yaw` |

```jai
// Create a rotation
q := to_quat(Radians.{0, PI/4, 0});

// Convert freely
m   := to_matrix(q);        // -> Mat3
m4  := to_matrix4(q);       // -> Mat4
rot := to_rotator(q);       // -> degrees
rad := to_radians(q);       // -> radians
q2  := to_quat(m);          // -> back to Quat
```

### Transform

`Transform` is a TRS struct — translation, rotation (as `Quat`), scale. Converts to and from `Mat4`:

```jai
t := Transform.{
    translation = .{10, 0, 5},
    rotation    = to_quat(Radians.{0, PI/4, 0}),
    scale       = .{1, 1, 1},
};

world := to_matrix(t);       // -> Mat4
t2    := to_transform(world); // -> back to Transform
```

> Full reference: [Rotators](/jay_math/rotators)

---

## Scalar Functions

Drop-in replacements for standard math functions. Derived from [Cephes](https://www.netlib.org/cephes/), with SSE-accelerated `sqrt`, `floor`, and `ceil`.

```jai
sin(x);  cos(x);  tan(x);
asin(x); acos(x); atan(x); atan2(y, x);
sqrt(x); exp(x);  log(x);  pow(x, y);
floor(x); ceil(x); abs(x);

// Simultaneous sin/cos from a single range reduction
s, c := sincos(angle, do_sin = true, do_cos = true);

// Quake III fast inverse square root
finv_sqrt(x);

// Scaled integer sin via compile-time LUT
sin(x, scale);
```

All functions have `float32`, `float64`, and integer overloads.

> Full reference: [Scalar Functions](/jay_math/scalar-math)

---

## SIMD Under the Hood

Jay_Math selects SSE/AVX/FMA instructions at compile time based on type and element count — no runtime dispatch. Float vectors and matrices are automatically aligned and overlaid with SIMD-friendly layouts. You don't need to think about it; the right instructions are picked for you.

Integer types use scalar fallback — SIMD integer math is on the roadmap.

---

## Constants

```jai
PI   PI64              // 3.14159...
TAU  TAU64             // 6.28318...
DEG_TO_RAD  RAD_TO_DEG // conversion factors
EPSILON     EPSILON_64 // machine epsilon
```

Numeric limits for every size: `FLOAT32_MIN`, `FLOAT32_MAX`, `S32_MIN`, `U64_MAX`, etc.
