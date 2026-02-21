---
title: Fundamentals
order: 0
description: Math module. Vectors, matrices, quaternions, transforms, and scalar math — we got them all
---

# Overview

[Jay_Math](https://github.com/BlueJayEngine/Jay_Math) is jai-native, SIMD-accelerated math module designed to be lightweight and high-performance at runtime while remaining flexible and convenient to use.
## Features

### [Vectors](/jay_math/vectors)
Parametric `Vector(N, T, AXES)` struct with named axis fields, SIMD-accelerated element-wise ops for floats, and scalar fallback for integers. Includes `Vec2/3/4`, `Point2/3/4`, and double-precision variants.

### [Matrices](/jay_math/matrices)
Parametric `Matrix(COL, ROW, T)` struct with FMA-accelerated multiply and closed-form inverse. Built-in transform operations: translate, rotate, scale, shear, face, and inverse.

### [Rotators](/jay_math/rotators)
Three interchangeable rotation representations — `Quat`, `Euler`/`Rotator`, and `Radians` — with full round-trip conversion between all of them. Also includes a `Transform` TRS struct.

### [Scalar Functions](/jay_math/scalar-math)
Drop-in replacements for standard math functions (`sin`, `cos`, `sqrt`, `pow`, etc.) using Cephes-derived implementations with hardware-acceleration.

### All SIMD-Accelerated
Compile-time code generation — SSE/AVX/FMA instructions are selected based on type and element count, not runtime dispatch.

