# Contract Upgrade Patterns

> **Status**: Phase 1 Research Findings  
> **Date**: 2025-12-17  
> **Author**: SwiftSec Research Team

## 1. Introduction
"Code is Law" implies immutability, but software has bugs. SwiftSec must support upgradeability without compromising trust.

## 2. Common Patterns

### 2.1 The Proxy Pattern (Standard)
- **Logic**: A "Proxy" contract holds state and delegates logic execution to an "Implementation" contract using `delegatecall`.
- **Risk**: Storage layout collisions. If the new implementation changes field order, state is corrupted.
- **SwiftSec Solution**: Since SwiftSec manages memory layout, the compiler can **statically verify** that `Implementation V2` has a backward-compatible storage layout with `Implementation V1`.

### 2.2 State Migration (The "Hard Fork")
- **Logic**: Deploy new contract, read old state, write to new state.
- **Risk**: Extremely expensive gas costs.
- **SwiftSec Solution**: Provide an `export` / `import` dump utility in the `swifsc-cli` for devnets, but not recommended for mainnet.

### 2.3 Immutable by Default
SwiftSec contracts are immutable unless explicitly marked `upgradeable` in the manifest `SwiftSec.toml`.
- If `upgradeable = true`, the compiler implicitly generates a Proxy wrapper and separates state from logic.

## 3. Safety Constraints
The SwiftSec Compiler will enforce **Storage Append-Only Rules** for upgradeable contracts:
1.  New fields must be added at the end of `storage`.
2.  Existing fields cannot change type (e.g., `u8` -> `u64` is okay, `u64` -> `u8` is not).
3.  Deleted fields must be marked `deprecated` but their slot preserved.

## 4. Conclusion
Upgradeability is a "First-Class Citizen" in SwiftSec. The compiler will handle the complex proxy logic and storage layout verification, removing the main source of upgrade-related bugs (like the Audius hack).
