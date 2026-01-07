# SwiftSC Security Analysis (V1.0.3)

## Overview

The `swiftsc-analyzer` is a static analysis tool integrated into the CLI and LSP. In V1.0.3, it has been hardened with three critical security passes designed to catch common smart contract vulnerabilities before deployment.

## New Detection Rules

### 1. Reentrancy Detection

**ID**: `SEC-002`

Detects state changes (writes to storage) that occur *after* an external call. This pattern is the root cause of reentrancy attacks (e.g., The DAO).

**Vulnerable Pattern:**
```swift
func withdraw(amount: u64) {
    call_external(user_addr, amount); // External call
    balance = balance - amount;       // State change AFTER call (VULNERABLE!)
}
```

**Fix:** Follow the Checks-Effects-Interactions pattern.
```swift
func withdraw(amount: u64) {
    balance = balance - amount;       // State change FIRST
    call_external(user_addr, amount); // Interaction LAST
}
```

### 2. Integer Overflow

**ID**: `SEC-003`

Detects arithmetic operations (`+`, `-`, `*`) that are not wrapped in checked contexts. In SwiftSC V1.0.3, standard operators do not trap on overflow by default for performance, so critical logic must use `SafeMath`.

**Recommendation**: Use `math::add`, `math::sub`, etc., for sensitive value transfers.

### 3. Uninitialized Storage

**ID**: `SEC-004`

Ensures that all declared storage variables are assigned a value in the contract's `init()` constructor. Accessing uninitialized storage can lead to undefined behavior or logical errors.

## Running the Analyzer

The analyzer runs automatically in your editor (via generic LSP diagnostics). You can also run it manually:

```bash
swiftsc check --path ./my_contract
```
