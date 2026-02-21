---
title: Scalar Functions
order: 4
description: Cephes-derived math functions with hardware-accelerated sqrt, floor, and ceil
---

# Scalar Functions

Drop-in replacements for standard math functions. Implementations are derived from the [Cephes](https://www.netlib.org/cephes/) library. All functions have `float32`, `float64`, and integer overloads where applicable.

## Hardware-Accelerated

These use SSE instructions directly:

| Function | Instruction | Description |
| -------- | ----------- | ----------- |
| `sqrt` | `sqrtss` / `sqrtsd` | Square root |
| `floor` | `roundss` / `roundsd` | Round toward negative infinity |
| `ceil` | `roundss` / `roundsd` | Round toward positive infinity |

## Trigonometry

| Function | Description |
| -------- | ----------- |
| `sin(x)` | Sine |
| `cos(x)` | Cosine |
| `tan(x)` | Tangent (computed as `sin/cos`) |
| `asin(x)` | Arc sine |
| `acos(x)` | Arc cosine |
| `atan(x)` | Arc tangent |
| `atan2(y, x)` | Two-argument arc tangent |
| `sincos(x, do_sin, do_cos)` | Simultaneous sin and cos from a single range reduction |

`sin` and `cos` also have a scaled integer LUT variant: `sin(x, scale)` generates a compile-time lookup table for fixed-point use.

## Exponential and Logarithmic

| Function | Description |
| -------- | ----------- |
| `exp(x)` | Natural exponential (Pade approximation) |
| `log(x)` | Natural logarithm |
| `log2(x)` | Base-2 logarithm |
| `pow(x, y)` | Power function |

## Rounding and Remainder

| Function | Description |
| -------- | ----------- |
| `floor(x)` | Round down (SSE-accelerated) |
| `ceil(x)` | Round up (SSE-accelerated) |
| `trunc(x)` | Round toward zero |
| `mod(x)` | Fractional part (`x - floor(x)`) |
| `mod(x, e)` | Modulo with divisor |
| `frac(x)` | Fractional part (same as single-arg `mod`) |

## Utility

| Function | Description |
| -------- | ----------- |
| `abs(x)` | Absolute value |
| `sqrt(x)` | Square root (SSE-accelerated) |
| `inv_sqrt(x)` | `1/sqrt(x)` |
| `finv_sqrt(x)` | Quake III fast inverse square root |
| `lerp(a, b, t)` | Linear interpolation |
| `grid_snap(loc, grid)` | Snap value to nearest grid point |
| `signbit(x)` | Sign bit extraction |
| `is_nan(x)` | NaN check |
| `is_inf(x)` | Infinity check |
| `is_finite(x)` | Finite check |

## Type Helpers

| Function | Description |
| -------- | ----------- |
| `epsilon(T)` | Machine epsilon for float type |
| `inf(T)` | Infinity for float type |
| `nan(T)` | NaN for float type |
| `negative_zero(T)` | Negative zero for float type |

## Constants

```jai
PI          // 3.14159...  (float32)
PI64        // 3.14159...  (float64)
TAU         // 6.28318...  (float32)
TAU64       // 6.28318...  (float64)
DEG_TO_RAD  // PI/180
RAD_TO_DEG  // 180/PI
EPSILON     // float32 machine epsilon
EPSILON_64  // float64 machine epsilon
```

Numeric limits for all sizes:

```jai
FLOAT32_MIN  FLOAT32_MAX  FLOAT32_INFINITY  FLOAT32_NAN
FLOAT64_MIN  FLOAT64_MAX  FLOAT64_INFINITY  FLOAT64_NAN
S8_MIN  S8_MAX  U8_MAX
S16_MIN S16_MAX U16_MAX
S32_MIN S32_MAX U32_MAX
S64_MIN S64_MAX U64_MAX
FLOAT16_MAX
```

## Bit Manipulation

| Function | Description |
| -------- | ----------- |
| `set_bit(x, nth)` | Set the nth bit |
| `clear_bit(x, nth)` | Clear the nth bit |
| `toggle_bit(x, nth)` | Toggle the nth bit |
