# SwiftSC-Lang Lexical Specification

> **Status**: Draft (M2.1)  
> **Date**: 2025-12-17  
> **Author**: Language Design Team

## 1. Input Format
- **Encoding**: UTF-8.
- **Whitespace**: Space (U+0020), Tab (U+0009), Line Feed (U+000A), Carriage Return (U+000D).
- **Comments**:
    - Line comments: `// ...`
    - Block comments: `/* ... */` (can be nested)

## 2. Identifiers
- Start with `a-z`, `A-Z`, or `_`.
- Followed by `a-z`, `A-Z`, `0-9`, or `_`.
- Regex: `[a-zA-Z_][a-zA-Z0-9_]*`
- **Raw Identifiers**: `r#identifier` (reserved for future use/ffi).

## 3. Keywords
| Keyword | Usage |
|:---|:---|
| `contract` | Define a smart contract module. |
| `pub` | Public visibility modifier. |
| `fn` | Function definition. |
| `let` | Variable binding (immutable by default). |
| `mut` | Mutable binding modifier. |
| `return` | Return from function. |
| `if`, `else` | Control flow. |
| `match` | Pattern matching. |
| `for`, `in` | Iteration. |
| `while` | Loop. |
| `break`, `continue` | Loop control. |
| `struct` | Composite data type. |
| `enum` | Algebraic data type. |
| `resource` | Linear type definition (cannot be dropped). |
| `init` | Contract constructor. |
| `self`, `Self` | Instance and Type references. |
| `true`, `false` | Boolean literals. |
| `storage` | Storage block definition. |
| `emit` | Event emission. |
| `const` | Compile-time constant. |
| `type` | Type alias. |
| `use` | Import/use declaration. |
| `mod` | Module declaration. |
| `unsafe` | Unsafe block marker. |

## 4. Literals

### 4.1 Integers
Can include underscores for readability.
- Decimal: `123`, `1_000`
- Hex: `0xDeadBeef`
- Binary: `0b1010`
- Octal: `0o777`
- Suffixes (optional but recommended): `1u8`, `100u64`, `10i32`.

### 4.2 Strings
Double-quoted UTF-8 strings.
- Normal: `"Hello world"`
- Escapes: `\n`, `\t`, `\"`, `\\`, `\u{1F600}`.
- Raw strings: `r"No \ escapes"`

### 4.3 Bytes
- Byte string: `b"ASCII data"` (Type: `[u8; N]`)
- Hex string: `x"deadbeef"` (Type: `[u8; N]`)

## 5. Operators & Punctuation

| Category | Symbols |
|:---|:---|
| **Arithmetic** | `+`, `-`, `*`, `/`, `%` |
| **Bitwise** | `&`, `|`, `^`, `<<`, `>>` |
| **Logic** | `&&`, `||`, `!` |
| **Comparison** | `==`, `!=`, `<`, `>`, `<=`, `>=` |
| **Assignment** | `=`, `+=`, `-=`, `*=`, `/=`, `%=` |
| **Access** | `.`, `::`, `->` (return type) |
| **Delimiters** | `{`, `}`, `(`, `)`, `[`, `]`, `,`, `;`, `:` |
