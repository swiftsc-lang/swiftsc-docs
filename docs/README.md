# SwiftSec Documentation

Welcome to the internal documentation for **SwiftSec**, the next-generation smart contract language.

## 📚 Table of Contents

### Phase 1: Research & Background
Analysis of the current ecosystem to inform our design decisions.
- [Blockchain Landscape](research/blockchain-landscape.md): Comparative analysis of Solidity, Rust, Move, and Cairo.
- [WASM Execution Models](research/wasm-execution-models.md): Understanding JIT vs AOT and runtime architecture.
- [Vulnerability Analysis](research/smart-contract-vulnerabilities.md): Top threats (reentrancy, overflows) and how we prevent them.
- [Gas Accounting](research/gas-accounting-models.md): Strategy for instruction injection and metering.
- [Formal Verification](research/formal-verification.md): Plans for SMT solver integration.

### Phase 2: Specification
The core defining documents for the language.
- [Language Requirements](spec/language-requirements.md): Syntax, type system, and memory safety rules.
- [Threat Model](spec/threat-model.md): Trust boundaries and attacker capabilities.
- [Design Principles](spec/design-principles.md): Core philosophy (Safety > Flexibility).

### Phase 3: Architecture
How we are building the compiler.
- [Compiler Overview](architecture/compiler-overview.md): Pipeline from source to WASM.

---

> This documentation is a living resource. Please update it as design decisions evolve.
