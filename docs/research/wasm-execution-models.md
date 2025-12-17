# WASM Execution Models for Smart Contracts

> **Status**: Phase 1 Research Findings  
> **Date**: 2025-12-17  
> **Author**: Systems Engineering Team

## 1. Why WebAssembly (WASM)?
WASM is the de-facto standard for next-generation portable computation. For blockchains, it offers:
- **Near-native performance**: JIT/AOT compilation approaches C++ speed.
- **Sandboxing**: Memory isolation is built-in.
- **Determinism**: Can be made deterministic (removing floats, threads).
- **Tooling**: LLVM backend support means C++, Rust, Zig, and Go can define the toolchain.

## 2. Execution Environments

### 2.1 JIT vs. AOT vs. Interpreted

| Method | Description | Pros | Cons | Recommendation |
|:---|:---|:---|:---|:---|
| **Interpreted** | Executing instructions one by one (e.g., Wasmi) | Fast startup, low memory overhead, easy metering | Slow execution speed (10-100x slower) | **Use for Dev/Debug** |
| **JIT (Just-In-Time)** | Compilation at runtime (e.g., V8, SpiderMonkey) | Fast execution | Compilation latency, non-deterministic risks, large memory footprint | **Avoid for Consensus** |
| **AOT (Ahead-Of-Time)** | Compile to machine code before deployment (e.g., Wasmtime, PVF) | Fastest execution, deterministic | Slower deployment (compilation step), large artifacts | **Ideal for Mainnet** |

**SwiftSec Decision**: 
- **Development**: Use an interpreter (Wasmi) for instant feedback in the CLI/Playground.
- **Production**: Target AOT-compatible WASM (e.g., Polkadot's PVF model uses Wasmtime).

---

## 3. Contract Runtime Architecture

A SwiftSec contract compiled to WASM will interact with the blockchain host via **Host Functions**.

### 3.1 Memory Layout
WASM uses a linear memory model.
- **Stack**: Managed by WASM engine (function locals, return addresses).
- **Heap**: Managed by SwiftSec's allocator (within the linear memory pages).
    - *Decision*: Use a simple **bump allocator** for short-lived transactions to minimize code size. No complex GC.

### 3.2 Host Interface (Imports)
The contract acts as a guest module importing functionality from the host blockchain.
```wat
(import "env" "storage_read" (func $storage_read (param i32 i32) (result i32)))
(import "env" "storage_write" (func $storage_write (param i32 i32 i32)))
(import "env" "transfer" (func $transfer (param i32 i32) (result i32)))
```

### 3.3 Entry Point (Exports)
The contract exports a `call` function or individual entry points.
```wat
(export "call" (func $call))
(export "query" (func $query))
```

---

## 4. Determinism & Limitations
To ensure consensus, WASM execution must be perfectly deterministic.
SwiftSec's compiler backend will enforce:
1.  **No Floating Point**: `f32` and `f64` instructions are banned. All decimals use fixed-point math libraries.
2.  **No Threads**: WASM threads proposal is disabled.
3.  **Bounded Resources**: Recursive depth limits and memory expansion limits.

## 5. Gas Metering (Instrumentation)
Since WASM doesn't have intrinsic gas metering, we must **inject metering instructions**.
- **Strategy**: Post-process the WASM binary.
- **Tool**: `wasm-instrument` or custom pass.
- **Implementation**: Insert `gas_consume(N)` before every basic block header.

## 6. Conclusion
SwiftSec will target the **wasm32-unknown-unknown** target profile. It will produce "pure" WASM modules that rely on a clearly defined "SwiftSec Host Interface" (SSHI) to talk to any blockchain, with adapter layers for specific chains (Polkadot, Near, etc.).
