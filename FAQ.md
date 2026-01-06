# ❓ SwiftSC-Lang FAQ & Troubleshooting

## 🛠️ General Installation

### Q: How do I install the SwiftSC-Lang compiler?
A: You can install it via the provided APT repository:
```bash
wget -qO - https://repo.swiftsc-lang.org/pubkey.gpg | sudo apt-key add -
echo "deb https://repo.swiftsc-lang.org/stable all main" | sudo tee /etc/apt/sources.list.d/swiftsc.list
sudo apt update && sudo apt install swiftsc
```

### Q: Does it support Windows/macOS?
A: Currently, native support is focused on Linux (AMD64). For other platforms, we recommend using Docker or WSL2.

## 🛡️ Security Analysis

### Q: Why am I getting "Unchecked Arithmetic" warnings?
A: By default, the `swiftsc analyze` command flags potential overflows with `+` and `-`. To resolve this, use the `std::math` SafeMath modules:
```swift
use std::math::add;
let result = add(a, b)?;
```

### Q: What is SEC-002?
A: SEC-002 is a Reentrancy warning. Ensure that you are updating state *before* making external calls.

## 📚 Standard Library

### Q: How do I use dynamic arrays?
A: Use the `Vec<T>` type from `std::collections`:
```swift
use std::collections::Vec;
let mut list = Vec::<u64>::new();
list = list.push(10);
```

## 🤖 Troubleshooting

### Q: `cargo build` fails with "Workspace not found"
A: Ensure you are at the root of the project and that all `package.json` files are correctly mapped in the Dockerfile if building via container.
