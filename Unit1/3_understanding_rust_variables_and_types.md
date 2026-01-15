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

This section focuses on the floating point types available in Rust, specifically F32 and F64.

Floating Point Types in Rust
- Rust has two floating point types: F32 (32-bit) and F64 (64-bit), similar to float and double in other languages.
- It is recommended to use F64 for maximum precision, while F32 can be used to save space in large datasets.

Formatting Floating Point Numbers
- You can specify the number of decimal places when outputting a float using println, e.g., .2 for two decimal places.
- Field width and alignment can be controlled using format specifiers, allowing for left or right alignment and custom fill characters.

Scientific Notation
- Rust supports scientific notation, allowing numbers to be expressed in a more readable format, such as using E for powers of ten.
- Examples include representing the electronic charge and the speed of light in both standard and exponential formats.

Rust has two floating-point types:

- `f32`: 32-bit floating point
- `f64`: 64-bit floating point (default)

```rust
let x = 2.0;    // f64
let y: f32 = 3.0;
```

## Other Simple Types

This content focuses on the introduction of Boolean types and characters in Rust programming.

Boolean Types
- Rust has a `bool` type that can only be assigned the values `true` or `false`, with both keywords in lowercase.
- You can convert a Boolean to an integer using the `as` keyword, where `true` becomes `1` and `false` becomes `0`.

Boolean Logic
- Rust supports logical operators: `&&` (AND), `||` (OR), and `!` (NOT), which utilize short-circuit evaluation.
- If the first condition in an AND operation is false, the second condition is not evaluated, and similarly for OR operations.

Character Types
- Rust has a `char` type that supports Unicode characters, declared using single quotes.
- The size of a `char` can vary depending on the character, as it uses Unicode encoding.

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

This lesson focuses on advanced techniques for writing Rust code, emphasizing safety and efficiency.

Inferred Types
- Rust allows the compiler to deduce variable types based on assigned values, eliminating the need for explicit type declarations.
- By default, variables in Rust are immutable, promoting safety in multi-threaded applications.

Mutability and Variable Naming
- To declare a mutable variable, the `mut` keyword must be used; otherwise, the variable remains immutable.
- Unused variables trigger compiler warnings, but prefixing a variable name with an underscore suppresses these warnings.

Type Conversions and Constants
- Rust requires explicit type conversions using the `as` keyword, ensuring predictable code behavior.
- Compile-time constants can be declared using the `const` keyword, enhancing code readability and efficiency.

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
