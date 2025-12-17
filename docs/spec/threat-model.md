# SwiftSec Threat Model

> **Status**: Phase 1 Specification  
> **Date**: 2025-12-17  
> **Author**: Security Engineering Team

## 1. Introduction
This document defines the security boundaries, potential adversaries, and guarantees provided by the SwiftSec language and runtime environment.

## 2. Trust Boundaries

### 2.1 Trusted Components (TCB)
- **The SwiftSec Compiler**: We assume the compiler correctly translates valid source to WASM.
- **The WASM Runtime**: The blockchain's VM (Wasmtime/Wasmi) allows correct execution.
- **The Consensus Engine**: The underlying blockchain correctly orders and validates transactions.

### 2.2 Untrusted Components
- **Input Data**: All arguments (`msg.data`, `msg.sender`) are potentially malicious.
- **External Contracts**: Any contract called via CPI (Cross-Program Invocation) is untrusted.
- **Off-Chain Oracles**: Data provided by oracles may be false/manipulated.

---

## 3. Assets & Protective Goals

| Asset | Description | Goal |
|:---|:---|:---|
| **Contract State** | Persistent storage (balances, configs). | **Integrity**: Only authorized logic modifies state. |
| **User Funds** | Native tokens or minted assets. | **Theft Prevention**: No unauthorized transfers. |
| **Availability** | Ability to execute contract logic. | **DoS Prevention**: Functions must eventually complete. |
| **Privacy** | (Optional) Sensitive data visibility. | **Confidentiality**: *Out of Scope for v1 (Public Chain)*. |

---

## 4. Attacker Capabilities

### 4.1 The Malicious User
- Can call any public function with arbitrary arguments.
- Can call functions in any order (state machine violation attempts).
- Can deploy their own malicious contracts to interact with ours.

### 4.2 The "Rich" Attacker (Flash Loans)
- Has "infinite" capital within a single transaction.
- Can manipulate AMM prices or governance votes atomically.

### 4.3 The Miner/Validator (MEV)
- Can reorder transactions within a block.
- Can censor transactions (temporarily).
- **Cannot** forge signatures.
- **Cannot** revert finalized blocks (assuming secure consensus).

---

## 5. Security Guarantees

### 5.1 Deterministic Execution
SwiftSec guarantees that for a given State $S$ and Input $I$, the Output $O$ and New State $S'$ are always identical across all nodes.
- **Mitigation**: No floating point, no nondeterministic iteration (e.g., iterating hash maps keys).

### 5.2 Isolation
A crash or panic in a SwiftSec contract will **revert** the transaction but **never** corrupt the memory of the host or other contracts.
- **Mitigation**: WASM Sandbox + Stack depth limits.

### 5.3 Type Safety
It is impossible to confuse an Integer for a Pointer or an Address.
- **Mitigation**: Strong static typing + Runtime casting checks.

---

## 6. Out of Scope
The following are NOT handled by the language:
- **Phishing Attacks**: Users signing bad transactions.
- **Private Key Theft**: Wallet security.
- **Consensus Failures**: 51% attacks on the underlying chain.
- **Economic Logic Bugs**: A developer writing `price = 0` is a logic error, not a language flaw.

---

## 7. Residual Risks
- **Compiler Bugs**: The compiler itself may have codegen errors. *Mitigation: Fuzzing, Audits.*
- **Supply Chain Attacks**: Malicious dependencies in `std` or third-party packages. *Mitigation: Lockfiles, Registry scanning.*
