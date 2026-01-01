# Gas Metering in SwiftSC V1.0.3-beta

## Overview

SwiftSC V1.0.3-beta introduces automated **Gas Metering** to the compiler backend. This feature ensures that all smart contract execution is finite and deterministic by injecting cost-accounting instructions into the generated WebAssembly.

## How It Works

The `swiftsc-backend` performs a pass over the WASM module during compilation. It identifies basic blocks (linear sequences of instructions) and inserts `gas_consume(amount)` calls at the beginning of each block.

### Cost Model (Beta)

| Operation Type | Base Cost | Notes |
| :--- | :--- | :--- |
| **Arithmetic** | 1 | `i32.add`, `i64.sub`, etc. |
| **Control Flow** | 2 | `br`, `if`, `loop` |
| **Memory Load/Store** | 3 | `i32.load`, `i64.store` |
| **Host Calls** | Dynamic | Costs vary by host function complexity. |

## Developer Impact

For most developers, this process is transparent. However, you should be aware of:

1.  **Loops**: Heavy loops will consume gas proportional to their iteration count. Infinite loops will trap.
2.  **Memory**: Allocating large structures (like Vectors/Maps) has a high gas cost due to memory expansion.
3.  **Host Functions**: Calling out to the chain (e.g., `storage_write`) is significantly more expensive than local computation.

## Future Work

*   **Calibration**: The cost table will be refined based on benchmarks against major networks.
*   **Optimization**: The compiler will eventually optimize gas injection to minimize runtime overhead (e.g., hoisting checks out of loops).
