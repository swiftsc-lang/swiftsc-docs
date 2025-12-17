# SwiftSec Language Requirements Specification

> **Status**: Phase 1 Specification  
> **Date**: 2025-12-17  
> **Author**: Language Design Team

## 1. Design Goals
SwiftSec aims to be the safest and most developer-friendly smart contract language for the WASM ecosystem.

### 1.1 Core Principles
1.  **Safety by Default**: No unsafe operations (e.g., unchecked math, raw pointers) without explicit `unsafe` blocks.
2.  **Predictable Cost**: Total predictability of gas usage before execution.
3.  **Zero-Cost Abstractions**: High-level features (generics, iterators) compile down to optimal manual WASM.
4.  **WASM Native**: The language semantics map cleanly to WASM instructions, minimizing runtime overhead.

---

## 2. Syntax & Grammar
**Inspiration**: TypeScript (for accessibility) + Rust (for semantics).

### 2.1 Code Structure
Contracts are defined as `contract` blocks (similar to `class` in TS or `mod` in Rust).
```swiftsec
contract Token {
    // State variables
    storage balance: Map<Address, u64>;
    
    // Constructor (runs once)
    init(supply: u64) {
        self.balance[msg.sender] = supply;
    }

    // Public entry point
    pub fn transfer(to: Address, amount: u64) -> Result<()> {
        let sender = msg.sender;
        let sender_bal = self.balance[sender];
        
        ensure!(sender_bal >= amount, "Insufficient funds");
        
        self.balance[sender] -= amount;
        self.balance[to] += amount;
        
        emit Transfer(sender, to, amount);
        Ok(())
    }
}
```

### 2.2 Keywords (Reserved)
`contract`, `fn`, `let`, `mut`, `pub`, `storage`, `struct`, `enum`, `if`, `else`, `match`, `for`, `while`, `return`, `init`, `type`, `trait`, `impl`, `unsafe`.

---

## 3. Type System
SwiftSec employs a **Strong, Static, Linear** type system.

### 3.1 Primitive Types
- **Integers**: `u8`, `u16`, `u32`, `u64`, `u128`, `U256` (bigint), `i8`...
- **Boolean**: `bool` (true/false)
- **Address**: `Address` (32-byte or chain-specific)
- **String**: `String` (UTF-8, distinct from bytes)

### 3.2 Composite Types
- **Structs**: `struct Point { x: u64, y: u64 }`
- **Enums**: Algebraic data types (Rust-style).
- **Arrays**: Fixed-size `[T; N]` and dynamic `Vec<T>`.
- **Maps**: `Map<K, V>` (only allowed in storage).

### 3.3 Linear Types (Resources)
Types annotated with `@resource` (or separate keyword `resource`) must be used exactly once. They cannot be ignored or overwritten.
```swiftsec
resource NFT {
    id: u64
}
// Compiler Error: NFT created but not moved/stored
```

---

## 4. Memory Safety Guarantees

### 4.1 Ownership & Borrowing
- **Single Owner**: Values have one owner.
- **Borrowing**: References (`&T`, `&mut T`) allowed but strictly checked.
- **No Null**: No null pointers; use `Option<T>`.
- **Bounds Checking**: All array accesses checked at runtime (panic on OOB).

### 4.2 Mutability
- Variables are immutable by default: `let x = 5;`
- Mutable variables must be explicit: `let mut x = 5;`

---

## 5. Error Handling
- **No Exceptions**: No `try/catch` unwinding.
- **Result Type**: Recoverable errors use `Result<T, E>`.
- **Panic**: Irrecoverable errors (OOB, overflow) cause immediate contract abort (revert).

---

## 6. Compatibility Requirements
- **Target**: WebAssembly (MVP + Sign-Extension + Multi-Value).
- **Host Interface**: Must support arbitrary host functions defined via import modules.
- **Serialization**: Standard layout (e.g., SCALE or Borsh) for cross-contract calls.

## 7. Versioning Strategy
- `0.x.x`: Rapid iteration, breaking changes allowed.
- `1.0.0`: Stability guarantee for language spec.
- **Edition System**: Like Rust (Edition 2026, 2029) to allow opt-in breaking syntax changes without splitting the ecosystem.
