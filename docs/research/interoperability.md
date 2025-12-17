# Blockchain Interoperability Requirements

> **Status**: Phase 1 Research Findings  
> **Date**: 2025-12-17  
> **Author**: SwiftSec Research Team

## 1. The Multi-Chain Challenge
SwiftSec targets **WASM** environments. However, every chain exposes a different "Host Interface" (ABI) for basic operations like:
- Getting user balance.
- Transferring funds.
- Reading/Writing storage.
- Calling other contracts.

## 2. Target Ecosystems

| Chain | Runtime | Contract Interface | Constraints |
|:---|:---|:---|:---|
| **Polkadot** (Contracts Pallet) | Wasmtime | `seal_*` functions (ink! compatible) | Strict gas, heavily sandboxed. |
| **NEAR** | Wasmer | `env_*` functions | Async/await needed for cross-contract calls. |
| **Cosmos** (CosmWasm) | Wasmer | JSON-based messages | Actor model, strict entry points. |
| **Stellar** (Soroban) | WASM | Host functions | Value transparency, strict bounds. |

## 3. The Adapter Architecture
SwiftSec will avoid code fragmentation by defining a **Standard Host Interface (SHI)**.

### 3.1 The "SwiftSec Standard Library" Abstraction
Developers write:
```swiftsec
// Generic code
let balance = std::chain::balance_of(user);
```

The compiler links against a target-specific backend:

**Target: `wasm32-unknown-unknown` + `--chain=polkadot`**:
```rust
// Lowers to:
seal_balance_of(...)
```

**Target: `wasm32-unknown-unknown` + `--chain=near`**:
```rust
// Lowers to:
env::account_balance(...)
```

## 4. Cross-Chain Messaging (XCM / IBC)
For v1, SwiftSec will treat cross-chain messages as byte arrays.
- **Polkadot**: Support XCM v3 types in `std::polkadot`.
- **Cosmos**: Support IBC packet formation in `std::ibc`.

## 5. Conclusion
SwiftSec core will be chain-agnostic. The `std` library will use conditional compilation (feature flags) to swap out the underlying host calls during part of the codegen phase.
