# Implementing Flow Control

## Learning Objectives

By the end of this section, you will:
- Use if-else statements for conditional logic
- Use match expressions for pattern matching
- Implement loops and control flow

## If-Tests

### Basic If-Else

```rust
let number = 6;

if number % 4 == 0 {
    println!("number is divisible by 4");
} else if number % 3 == 0 {
    println!("number is divisible by 3");
} else {
    println!("number is not divisible by 4 or 3");
}
```

### If as an Expression

```rust
let number = 6;
let result = if number > 5 { "big" } else { "small" };
```

## Matching

The `match` expression is more powerful than if-else for pattern matching:

```rust
let number = 3;

match number {
    1 => println!("One"),
    2 => println!("Two"),
    3 => println!("Three"),
    _ => println!("Something else"),
}
```

## Loops

### Loop (Infinite Loop)

```rust
let mut count = 0;
loop {
    count += 1;
    if count == 5 {
        break;
    }
}
```

### While Loop

```rust
let mut number = 3;
while number != 0 {
    println!("{}!", number);
    number -= 1;
}
```

### For Loop

```rust
for number in 1..4 {
    println!("{}", number);
}

for item in &vec![1, 2, 3] {
    println!("{}", item);
}
```

## Using Break and Continue

### Break

Exits the loop immediately:
```rust
let mut count = 0;
loop {
    count += 1;
    if count == 5 {
        break;  // exits the loop
    }
}
```

### Continue

Skips to the next iteration:
```rust
for number in 1..6 {
    if number % 2 == 0 {
        continue;  // skip even numbers
    }
    println!("{}", number);
}
```

### Loop Labels

```rust
'outer: loop {
    loop {
        break 'outer;  // breaks out of outer loop
    }
}
```

---

## Key Takeaways

- Use `if`, `else if`, and `else` for simple conditionals
- Use `match` for exhaustive pattern matching
- Choose appropriate loop types: `loop`, `while`, or `for`
- Use `break` and `continue` to control loop flow
