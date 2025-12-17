# Gas Accounting Models

> **Status**: Phase 1 Research Findings  
> **Date**: 2025-12-17  
> **Author**: Systems Team

## 1. The Halting Problem
Smart contracts must terminate. Since we cannot statically prove termination for all programs, we must enforce limits at runtime via **Gas**.

## 2. Metering Strategy: Injection
WASM does not have a "gas" opcode. We must inject it.

**Original Code**:
```wat
(block
  (i32.add (get_local 0) (get_local 1))
)
```

**Instrumented Code**:
```wat
(block
  (call $gas_consume (i32.const 1)) ;; Cost for add
  (i32.add (get_local 0) (get_local 1))
)
```

## 3. Cost Schedule (Draft)
Costs are relative to execution time (1 unit ~ 1ns).

| Operation | Cost | Rationale |
|:---|:---|:---|
| `i32.add` | 1 | Basic arithmetic is fast. |
| `i64.mul` | 2 | Slightly more complex. |
| `memory.grow` | 1000 | Allocation is expensive. |
| `call` | 10 | Stack frame setup overhead. |
| `Host Function` | Dynamic | Depends on DB read/write cost. |

## 4. Super-Linear Gas for Memory
To prevent memory exhaustion attacks, the cost of `memory.grow` becomes quadratic or exponential as memory usage increases.
