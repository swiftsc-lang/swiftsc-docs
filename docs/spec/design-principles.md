# SwiftSec Design Principles

> **Status**: Permanent Core Values  
> **Date**: 2025-12-17

## 1. Safety > Flexibility
If a feature allows "clever" hacks but introduces 1% risk of a security bug, we reject it.
- **Example**: No pointer arithmetic. No `goto`.

## 2. Explicit > Implicit
Code is read more often than written. Implicit behavior (hidden control flow, magic conversions) causes bugs.
- **Example**: No implicit type casting. `u8` cannot become `u64` without `as u64`.

## 3. Predictability
A developer should know exactly what their code will compile to and roughly how much gas it will cost.
- **Example**: No Garbage Collection (GC). GC pauses are unpredictable.

## 4. Developer Experience (DX) matters
Security features should not make the language painful to use.
- **Example**: Helpful error messages with suggestions, not just "Segmentation fault".

## 5. Composition
Contracts should be composable. Interfaces should be standard.
