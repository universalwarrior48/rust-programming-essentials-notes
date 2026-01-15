# Understanding Enums

## Learning Objectives

By the end of this section, you will:
- Define and use simple enums
- Work with enums that contain data
- Use the Option and Result enums
- Understand pattern matching with enums

## Defining and Using Simple Enums - Part 1

### Basic Enum Definition

```rust
enum Direction {
    North,
    South,
    East,
    West,
}

let direction = Direction::North;
```

### Using Enums in Functions

```rust
fn move_direction(direction: Direction) {
    match direction {
        Direction::North => println!("Moving north"),
        Direction::South => println!("Moving south"),
        Direction::East => println!("Moving east"),
        Direction::West => println!("Moving west"),
    }
}
```

## Defining and Using Simple Enums - Part 2

### Enums with Methods

```rust
impl Direction {
    fn is_vertical(&self) -> bool {
        match self {
            Direction::North | Direction::South => true,
            Direction::East | Direction::West => false,
        }
    }
}
```

## Avoiding Warnings about Unused Variants

Use the `#[allow(dead_code)]` attribute:

```rust
#[allow(dead_code)]
enum Status {
    Active,
    Inactive,
    Pending,
}
```

## Defining and Using Enums with Data

Enums can hold associated data:

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

let msg = Message::Move { x: 10, y: 20 };
```

### Pattern Matching with Data

```rust
match msg {
    Message::Quit => println!("Quit"),
    Message::Move { x, y } => println!("Move to ({}, {})", x, y),
    Message::Write(text) => println!("Text: {}", text),
    Message::ChangeColor(r, g, b) => println!("RGB({}, {}, {})", r, g, b),
}
```

## Using the Option Enum

`Option<T>` is used for values that may or may not exist:

```rust
enum Option<T> {
    Some(T),
    None,
}

let some_number = Some(5);
let no_number: Option<i32> = None;
```

### Working with Option

```rust
let x: Option<i32> = Some(5);
match x {
    Some(value) => println!("Got: {}", value),
    None => println!("Got nothing"),
}

// Using if let
if let Some(value) = x {
    println!("Got: {}", value);
}
```

## Using the Result Enum

`Result<T, E>` is used for operations that might fail:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}

fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(a / b)
    }
}
```

### Working with Result

```rust
let result = divide(10, 2);
match result {
    Ok(value) => println!("Result: {}", value),
    Err(e) => println!("Error: {}", e),
}

// Using unwrap (panics on error)
let value = divide(10, 2).unwrap();

// Using expect
let value = divide(10, 2).expect("Division failed");
```

---

## Key Takeaways

- Enums allow you to define a type with multiple variants
- Enums can hold associated data
- Pattern matching with `match` is the primary way to use enums
- `Option<T>` and `Result<T, E>` are powerful enums for handling absence and errors
