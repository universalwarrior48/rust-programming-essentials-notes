# Understanding Rust Variables and Types

## Learning Objectives

By the end of this section, you will:
- Declare and work with variables
- Understand Rust's type system
- Work with integers, floating-point numbers, and other types

## Integer Types

Rust provides signed and unsigned integer types:

| Type | Size | Signed |
|------|------|--------|
| i8 | 8-bit | Yes |
| u8 | 8-bit | No |
| i16 | 16-bit | Yes |
| u16 | 16-bit | No |
| i32 | 32-bit | Yes (default) |
| u32 | 32-bit | No |
| i64 | 64-bit | Yes |
| u64 | 64-bit | No |
| isize | arch | Yes |
| usize | arch | No |

```rust
let x: i32 = 42;
let y: u8 = 255;
```

## Floating-Point Types

Rust has two floating-point types:

- `f32`: 32-bit floating point
- `f64`: 64-bit floating point (default)

```rust
let x = 2.0;    // f64
let y: f32 = 3.0;
```

## Other Simple Types

### Booleans
```rust
let t = true;
let f: bool = false;
```

### Characters
```rust
let c = 'Z';
let emoji = '😻';
```

## Additional Techniques

### Type Inference
Rust can infer types from context:
```rust
let guess = "42".parse::<i32>().expect("Not a number!");
```

### Shadowing
You can redeclare variables with the same name:
```rust
let x = 5;
let x = x + 1;  // x is now 6
let x = "hello"; // x is now a string
```

### Constants
```rust
const MAX_POINTS: u32 = 100_000;
```

---

## Key Takeaways

- Rust is statically typed with excellent type inference
- Choose appropriate integer and float types for your use case
- Variables are immutable by default; use `mut` to make them mutable
