# SwiftSC-Lang Type System Specification

> **Status**: Draft M2.2  
> **Date**: 2025-12-17  
> **Author**: Language Design Team

## 1. Overview
SwiftSC-Lang employs a **static, strong, affine-linear type system**.
- **Static**: All types known at compile time.
- **Strong**: No implicit type coercion (except safe widening).
- **Affine/Linear**: Resources must be used exactly once (linear) or at most once (affine).

## 2. Primitive Types

| Type | Description | Size (bytes) | Copyable? |
|:---|:---|:---|:---|
| `bool` | Boolean (`true`, `false`) | 1 | Yes |
| `u8`..`u128` | Unsigned Integers | 1..16 | Yes |
| `i8`..`i128` | Signed Integers | 1..16 | Yes |
| `U256` | 256-bit Unsigned (Solidity compat) | 32 | Yes |
| `Address` | Blockchain Address (32 bytes) | 32 | Yes |
| `String` | UTF-8 String (Heap allocated) | Fat Ptr | No (Cloneable) |

> **Note**: `char` is not supported. Use strings or bytes.

## 3. Composite Types

### 3.1 Structs
Standard data grouping.
```rust
struct Point { x: u64, y: u64 }
```
- **Copy Semantics**: By default, structs are moved. If all fields are `Copy`, the struct can derive `Copy`.

### 3.2 Enums (Algebraic Data Types)
Supports tagged unions.
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

### 3.3 Arrays & Vectors
- **Fixed Array**: `[u8; 32]` (Stack/Inline).
- **Dynamic Vector**: `Vec<u8>` (Heap).

---

## 4. Linear Types (Resources)
The core safety feature for assets (tokens, NFTs).

### 4.1 Definition
Defined with `resource` keyword.
```rust
resource Coin { value: u64 }
```

### 4.2 The Rules of Linearity
1.  **Destruction**: A resource cannot be dropped (go out of scope) without being "consumed".
    - *Consumption*: Moving into another function, moving into storage, or deconstructing.
2.  **Duplication**: A resource cannot be copied or cloned.
3.  **Borrowing**: A resource can be borrowed immutably (`&Coin`) or mutably (`&mut Coin`).

### 4.3 Example
```rust
fn process(c: Coin) {
    // Error: 'c' dropped here implicitly!
} // Compile Error: Resource 'c' must be used.

fn correct(c: Coin) {
    let Coin { value } = c; // Consumed via deconstruction
}
```

---

## 5. Ownership & Borrowing
Simplified Rust model suited for single-threaded WASM.

### 5.1 Rules
1.  Each value has a single owner.
2.  You can have **many** immutable borrows (`&T`) OR **one** mutable borrow (`&mut T`).
3.  References must always be valid (no null, no dangling).

### 5.2 Lifetime Elision
In SwiftSC-Lang, lifetime annotations (`'a`) are **hidden** in 99% of cases.
- **Contract Scope**: State lives forever (persists between calls).
- **Function Scope**: Arguments live as long as the function call.
- The compiler enforces that references do not escape their valid scope.

---

## 6. Type Inference
Local variable types are inferred.
```rust
let x = 5;          // Inferred as i32 or u64 based on context
let y: u64 = 5;     // Explicit
```
Function signatures MUST be explicit.

---

## 7. Generics
Supported on Functions, Structs, and Enums.
- Monomorphization strategy: Compiler generates specialized code for each concrete type used (zero runtime cost, larger binary).
```rust
fn id<T>(x: T) -> T { x }
```
