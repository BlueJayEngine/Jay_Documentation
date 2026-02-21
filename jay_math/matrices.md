---
title: Matrices
order: 2
description: Parametric matrix types with SIMD multiply, cofactor inverse, and transform operations
---

# Matrices

`Matrix(COL, ROW, T)` is a parametric struct with named fields (`_11`, `_12`, ...), a flat `values[]` array, and `cells[][]` row-major access. Multiply is FMA-accelerated (broadcast-shuffle-FMA), add/sub are SIMD element-wise.

## Type Aliases

| float32 | float64 |
| ------- | ------- |
| `Mat2` `Mat3` `Mat4` `Mat4x3` | `Mat2d` `Mat3d` `Mat4d` `Mat4x3d` |

## Construction

```jai
m := Mat4.identity();

// Named field access
m._11;              // row 1, col 1
m.cells[0][2];      // row 0, col 2
m.values;           // flat [N]float32 array
```

## Operators

```jai
a := Mat3.identity();
b := Mat3.identity();

a + b;    // element-wise add (SIMD)
a - b;    // element-wise sub (SIMD)
a * b;    // matrix multiply (FMA-accelerated)
a / b;    // a * inverse(b) — square matrices only
a == b;   // element-wise comparison
```

## Inverse

Closed-form cofactor expansion for 2x2, 3x3, and 4x4 — branchless, no loops, single division. Gauss-Jordan fallback for larger matrices.

```jai
m := Mat4.identity();
inv := inverse(m);
```

## Transform Operations

Every transform follows a **make / set / apply** pattern:

- **`make_*`** — returns a new matrix from parameters
- **`set_*`** — overwrites the relevant part of an existing matrix
- **`apply`** (verb form) — multiplies the transform into an existing matrix

All have both pointer (`*mat`) and value (`mat`) overloads.

### Translate

4-column affine translation.

```jai
m := make_translation(Vec3.{10, 0, 2});

world := Mat4.identity();
set_translation(*world, Vec3.{5, 0, 0});
translate(*world, Vec3.{1, 0, 0});        // adds to existing translation
```

### Rotate

2D angle rotation for `Mat2`, 3D axis-angle (Rodrigues' formula) for `Mat3`/`Mat4`.

```jai
// 2D
r := make_rotation(PI / 4);

// 3D — axis must be unit length
r3 := make_rotation(PI / 4, Vec3.{0, 1, 0});

world := Mat4.identity();
rotate(*world, PI / 4, Vec3.{0, 1, 0});
```

### Scale

Diagonal scale from scalar pair (2D) or vector (3D).

```jai
s2 := make_scale(2.0, 3.0);             // Mat2
s3 := make_scale(Vec3.{2, 2, 2});       // Mat3

world := Mat4.identity();
scale(*world, Vec3.{1, 2, 1});
```

### Shear

2D (2 params) or 3D (6 params).

```jai
sh := make_shear(0.5, 0.0);                          // 2D
sh3 := make_shear(0.1, 0.0, 0.0, 0.2, 0.0, 0.0);    // 3D
```

### Face

Orient a matrix to look along a direction with an up vector. Rows become right / up / -forward.

```jai
orientation := make_face(direction, up);

world := Mat4.identity();
face(*world, direction, up);
```
