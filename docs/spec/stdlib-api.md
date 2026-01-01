# SwiftSC-Lang Standard Library API

> **Status**: Draft M2.3  
> **Date**: 2025-12-17  
> **Author**: Library Design Team

## 1. Overview
The standard library (`std`) is implicitly linked to every contract. It provides safe wrappers around WASM host functions and common utilities.

## 2. Module: `std::blockchain`
Core interaction with the chain state and context.

| Function/Macro | Signature | Description |
|:---|:---|:---|
| `msg.sender()` | `fn() -> Address` | Get caller address. |
| `msg.value()` | `fn() -> u128` | Get attached native currency. |
| `block.height()` | `fn() -> u64` | Get current block number. |
| `block.timestamp()` | `fn() -> u64` | Get block timestamp (unix micros). |
| `emit(event)` | `macro` | Emit a log event. |
| `transfer(to, amt)` | `fn(Address, u128) -> Result<()>` | Send native currency. |
| `call(addr, ...)` | `fn(...) -> Result<...>` | Low-level cross-contract call. |

## 3. Module: `std::collections`

### 3.1 `Vec<T>` (Dynamic Array)
Heap-allocated, growable array.
- `new() -> Vec<T>`
- `push(item: T)`
- `pop() -> Option<T>`
- `len() -> u64`
- `[index]` -> `&T` / `&mut T`

### 3.2 `Map<K, V>` (Storage Map)
**Only available in `storage` blocks.** Maps to blockchain state trie/kv-store.
- `insert(key: K, value: V) -> Option<V>`
- `get(key: K) -> Option<&V>`
- `remove(key: K) -> Option<V>`
- `contains(key: K) -> bool`

### 3.3 `Set<T>` (Storage Set)
Abstraciton over `Map<T, ()>`.
- `insert(val: T) -> bool`
- `contains(val: T) -> bool`

## 4. Module: `std::crypto`
Cryptographic primitives. Costly operations (high gas).

- `sha256(data: &[u8]) -> [u8; 32]`
- `keccak256(data: &[u8]) -> [u8; 32]`
- `blake2b(data: &[u8]) -> [u8; 32]`
- `verify_signature(pubkey, msg, sig) -> bool` (Ed25519 or ECDSA depending on chain).

## 5. Module: `std::math`
Safe arithmetic wrappers.

- `sqrt(x: u128) -> u128`
- `pow(base: u128, exp: u32) -> u128`
- `min(a, b)` / `max(a, b)`

### 5.1 `FixedPoint`
Decimal math support since floats are banned.
- `Fixed64` (18 decimals precision).
- `Fixed128` (18 decimals precision).
- Ops: `mul_div(x, y, denominator)` for precision-preserving math.

## 6. Module: `std::convert`
Type conversions.
- `From<T>`, `Into<T>` traits.
- `as_bytes() -> &[u8]`
- `to_string() -> String`

## 7. Error Handling (`std::result`)
- `Result<T, E>` enum.
- `Option<T>` enum.
- `assert!(cond, msg)`: Panics if false.
- `ensure!(cond, error)`: Returns `Err(error)` if false.
