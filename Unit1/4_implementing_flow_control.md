# Implementing Flow Control

## Learning Objectives

This lesson focuses on flow control in Rust, covering essential concepts familiar to those with experience in other programming languages.

**Simple If Tests**
- Introduces the basic structure and syntax of if statements in Rust.
- Discusses how to evaluate conditions and execute code based on true or false outcomes.

**Matching**
- Explains the matching feature in Rust, which is similar to switch statements in other languages but offers more flexibility.
- Highlights the power of pattern matching and its applications in various scenarios.

**Loops and Control Flow**
- Covers different types of loops available in Rust, such as for, while, and loop.
- Describes how to use break and continue statements to manage the flow of execution within loops.

## If-Tests

This section focuses on writing if statements in Rust, highlighting key syntax differences compared to other programming languages.


### Basic If-Else

Basic Syntax of If Statements
- The test expression must be a Boolean; unlike C++, you cannot use non-zero values as truthy.
- Parentheses around the test condition are not required and will generate a compiler warning if used unnecessarily.

If-Else Statements
- Curly brackets are mandatory for the body of the if statement, even for single statements.
- You can implement if-else tests to handle multiple conditions, with each part requiring its own curly brackets.

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

If-Else as an Expression
- Rust allows if-else to be used as an expression, similar to the ternary operator in other languages.
- The result of the if-else expression can be assigned to a variable based on the test condition's outcome. 

```rust
let number = 6;
let result = if number > 5 { "big" } else { "small" };
```

## Matching

The content focuses on the use of the `match` keyword in Rust, which is a powerful control flow construct.

Understanding the `match` Keyword
- The `match` keyword is similar to `switch` statements in other languages but offers more capabilities, allowing for matching against integers, booleans, enums, arrays, tuples, and structures.
- Each possible outcome in a `match` statement is called an "arm," and you can group multiple statements in an arm using curly brackets.

Basic Syntax and Exhaustiveness
- The basic syntax involves specifying the value to test and defining arms for each possible outcome, with a catch-all arm (underscore) to handle any unmatched values.
- Arms must be exhaustive; Rust will raise an error if not all possible values are accounted for.

Matching Ranges and Multiple Patterns
- You can match ranges of values using two dots (e.g., `25..=50` for inclusive ranges) and use the pipe symbol (`|`) to match multiple patterns in a single arm.
- Conditions can also be added to arms, known as match guards, allowing for more complex matching scenarios.

Using `match` as an Expression
- The `match` keyword can be used as an expression, allowing the result of the match to be assigned to a variable, which helps avoid code duplication and keeps the code clean.

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

This section focuses on writing loops in Rust, highlighting the different types of loops available in the language.


### Loop (Infinite Loop)

- The `loop` keyword creates an infinite loop, useful for event-driven applications that continuously wait for events.
- Unlike C++ or Java, Rust does not use traditional for loops for infinite iterations.

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

- The `while` loop functions similarly to other programming languages, requiring a mutable loop counter for iterations.
- Curly braces are mandatory to define the body of the loop, and Rust does not support the `++` or `--` operators.

```rust
let mut number = 3;
while number != 0 {
    println!("{}!", number);
    number -= 1;
}
```

### For Loop

- The `for in` loop iterates through elements in a collection, such as arrays, without traditional initialization and update syntax.
- Rust allows specifying exclusive and inclusive upper limits for numeric ranges, which is different from other languages.

```rust
for number in 1..4 {
    println!("{}", number);
}

for item in &vec![1, 2, 3] {
    println!("{}", item);
}
```

## Using Break and Continue

This section focuses on using the `break` and `continue` keywords in Rust to control loop execution.

### Break

- The `break` keyword terminates the loop when a specified condition is met, allowing you to stop processing once you've found what you're looking for.
- Common use case: Looping through a collection (e.g., lines of text) and stopping when a specific item is found.

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

- The `continue` keyword skips the current iteration of the loop and proceeds to the next iteration without terminating the loop.
- Typical scenario: When processing items, if an invalid element is encountered, you can use `continue` to skip it and move on to the next item.

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

- In nested loops, `break` and `continue` only affect the innermost loop by default.
- To control the outer loop, you can label it using a syntax (e.g., `outer:`) and then use that label with `break` or `continue` to affect the outer loop specifically.

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
