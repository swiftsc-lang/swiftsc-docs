# SwiftSC Standard Library: Collections

## Overview

V1.0.3-beta introduces the `Collections` module to `swiftsc-stdlib`. This provides essential data structures for managing dynamic data in smart contracts.

## Available Collections

### 1. Vector (`Vec<T>`)

A growable list type. Useful for storing lists of signers, logging events, or managing queues.

*   **Location**: `std::collections::vec`
*   **Gas Cost**: Moderate (resizing requires memory allocation).

```swift
use std::collections::vec;

let mut list = vec::new();
list.push(10);
list.push(20);
```

### 2. Map (`HashMap<K, V>`)

A key-value store backed by linear probing (in memory) or persistent storage slots.

*   **Location**: `std::collections::map`
*   **Gas Cost**: High (hashing + memory management).

```swift
use std::collections::map;

let mut balances = map::new();
balances.insert(address, 100);
```

## Best Practices

*   **Limit Growth**: Avoid unbounded growth of collections. Iterating over a massive Vector can exceed block gas limits.
*   **Storage vs. Memory**: Currently, these collections live in WASM memory. Future versions will introduce `StorageMap` for direct interaction with contract state storage.
