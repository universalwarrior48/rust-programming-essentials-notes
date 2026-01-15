# Understanding Enums

## Learning Objectives

In this lesson, the focus is on understanding enums in Rust, which are a crucial data type that restricts values to a specific set.

Defining Simple Enums
- The lesson begins with the syntax for defining and using simple enums in Rust.
- It also covers how to avoid warning messages when starting with enums.

Data-Containing Enums
- The lesson progresses to defining enums that can convey data values, explaining the syntax involved.
- The usefulness of these data-containing enums is discussed.

Standard Enums in Rust Library
- Finally, two standard enums from the Rust library are introduced: the option enum, which represents a potentially empty value, and the result enum, which indicates a possible error condition.
- These enums are essential for managing data and error handling in Rust applications.

## Defining and Using Simple Enums - Part 1

The content focuses on the concept of enums in Rust, which are a data type that allows for a closed set of allowed values.

### Basic Enum Definition
- Enums are defined using the `enum` keyword, followed by a name and a list of allowed values called variants.
- Variants in Rust are self-describing and provide clarity compared to using integers.

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
- To use an enum, you specify the enum type followed by the variant using the scope operator (::).
- A match statement is typically used to test the value of an enum variable, allowing for clear and structured handling of different cases.

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


### Modules and Visibility
- Enums can be defined in separate module files, which are private by default unless declared public using the `pub` keyword.
- To use an enum from a module in another file, you must import the module and bring the enum into scope using the `use` keyword.

`user_role.rs`
```rust
// user_roles.rs
pub enum UserRole {
    Admin,
    Editor,
    Viewer,
}
```

Sure! Let's explore a real-life example related to Modules and Visibility of Enums in Rust, particularly focusing on how you might use these concepts in a back-end development scenario.

### Example: Managing User Roles in a Web Application

Imagine you are developing a web application that has different user roles, such as Admin, Editor, and Viewer. You can use an enum to define these roles and manage their visibility across different modules in your application.

#### Step-by-Step Breakdown:

- **Define the Enum:**
  You create an enum called `UserRole` in a separate module file named `user_roles.rs`. This enum will have three variants: `Admin`, `Editor`, and `Viewer`.

  ```rust
  // user_roles.rs
  pub enum UserRole {
      Admin,
      Editor,
      Viewer,
  }
  ```

- **Set Visibility:**
  By using the `pub` keyword, you make the `UserRole` enum public, allowing it to be accessed from other modules in your application.

- **Import the Module:**
  In your main application file (e.g., `main.rs`), you import the `user_roles` module so you can use the `UserRole` enum.

  ```rust
  // main.rs
  mod user_roles; // Import the module

  use user_roles::UserRole; // Bring UserRole into scope

  fn main() {
      let role = UserRole::Admin; // Assign a role
      match role {
          UserRole::Admin => println!("User has admin privileges."),
          UserRole::Editor => println!("User can edit content."),
          UserRole::Viewer => println!("User can view content."),
      }
  }
  ```

- **Using the Enum:**
  In the `main` function, you create a variable `role` of type `UserRole` and use a `match` statement to determine the privileges associated with that role. This ensures that only the defined roles can be assigned, preventing any invalid role assignments.



## Defining and Using Simple Enums - Part 2

Defining Enums
- An enum type called `color` is defined with variants: red, green, and blue.
- Another enum type called `house location` is mentioned but not discussed in detail.

Importing Modules
- The `mytypes.rs` module must be loaded in the `main.rs` file to use the defined enums.
- The `mod` keyword is used to import the module, while the `use` keyword allows direct access to the enum names without needing to qualify them.

Demonstrating Enums
- A function demonstrates the use of the `color` enum by declaring a variable of type `color` and matching it to output corresponding values.
- Warnings about unused code are discussed, highlighting the importance of using all defined enum variants in larger applications.

Scope and Namespace
- If the module is not imported, the compiler will not recognize the enum types, leading to errors.
- The `use` keyword helps to bring enum names into the current scope, simplifying their usage in the code.

Sure! Based on the content provided, I can help you with code samples for the `mytypes.rs` and `main.rs` files that demonstrate the use of enums in Rust. Here’s how you can structure these files:

### `mytypes.rs`
This file defines the enums that you will use in your application.

```rust
// mytypes.rs

// Define a public enum for colors
pub enum Color {
    Red,
    Green,
    Blue,
}

// Define another public enum for house locations (to be discussed later)
pub enum HouseLocation {
    North,
    South,
    East,
    West,
}
```

### `main.rs`
This file is the entry point of your application and imports the enums defined in `mytypes.rs`.

```rust
// main.rs

// Import the mytypes module
mod mytypes;

// Use the Color enum from mytypes
use mytypes::Color;

fn main() {
    // Call the function to demonstrate simple enums
    demo_simple_enums();
}

// Function to demonstrate simple enums
fn demo_simple_enums() {
    // Declare a variable of type Color
    let color = Color::Red;

    // Match on the color and output corresponding messages
    match color {
        Color::Red => println!("Cork"),
        Color::Green => println!("Girth"),
        Color::Blue => println!("Glass"),
    }
}
```

### Explanation
- **`mytypes.rs`**: This file contains the definitions of the enums. The `Color` enum has three variants: `Red`, `Green`, and `Blue`. The `HouseLocation` enum is defined but not used in this example.
  
- **`main.rs`**: This is where the application starts. It imports the `mytypes` module and uses the `Color` enum. The `demo_simple_enums` function demonstrates how to declare a variable of the `Color` type and match on its value to print different outputs based on the color.



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


Understanding Enum Variants
- An enum type called "color" is defined with three variants: red, green, and blue.
- If some variants are not used in the code, the Rust compiler issues warnings about "dead code."

Suppressing Warnings
- To suppress these warnings, Rust allows the use of an attribute (using a hash symbol and square brackets) to permit dead code.
- This attribute can be applied to each variant individually or at the crate level to avoid warnings across the entire application.

Practical Application
- The lesson demonstrates how to implement this attribute in a module to manage unused variants effectively.
- By applying the attribute at the crate level, developers can prevent warnings for all unused code, simplifying the development process.

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

Understanding Enums
- Enums can represent a variable that can hold different states, such as colors (red, green, blue) or house locations (number, name, unknown).
- Each variant in an enum can also carry associated data, like an integer for a house number or a string for a house name.

Creating and Using Enums
- You can create enum variables that specify their state and associated value, such as a house location variable that can hold either a number or a name.
- The size of an enum variable is determined by the largest type it can hold, allowing for flexibility in value assignment.

Extracting Values from Enums
- To retrieve values from an enum, you can use a match statement to check the current state and extract the corresponding value.
- This mechanism allows for efficient handling of different data types within a single variable, enhancing the power of enums in Rust programming.
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
Understanding the `Option` Enum
- The `Option` enum can be in one of two states: `Some`, which contains a value of type T, or `None`, which indicates the absence of a value.
- It serves as a safer alternative to null pointers found in other programming languages, helping to avoid null pointer exceptions.

Using the `Option` Enum in Functions
- Functions can return an `Option` type to indicate whether a value is available or not, allowing for safer error handling.
- An example function, `second_of_day`, checks the validity of input time values and returns an `Option<u32>` representing the seconds past midnight or `None` for invalid inputs.

Extracting Values from `Option`
- The `unwrapOr` method can be used to retrieve the value from an `Option`, providing a default value if it is `None`.
- This method simplifies the process of checking for values compared to using match statements.

Documentation and Further Learning
- The Rust standard library provides extensive documentation on the `Option` enum, including various methods available for use.
- As learners progress in Rust, they will encounter and utilize the `Option` type frequently in their programming tasks.

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


Understanding the `Result` Enum
- The `result` type has two variants: `OK`, representing a successful outcome with a value of type T, and `Err`, indicating an error with an error object.
- This enum is useful for functions that might fail, such as database connections, providing a clear way to handle success and error states.

Error Handling in Rust
- Unlike languages like Java or C++, Rust does not use exception handling (try-catch blocks). Instead, it returns a `result` enum to indicate success or failure.
- This approach simplifies the flow of control in Rust applications, making it easier to determine if a function call was successful or resulted in an error.

Practical Example
- An example is provided using the `from_str_radix` function, which attempts to parse a string into a number based on a specified base (radix).
- The function returns a `result` that can either be an `OK` variant with the parsed number or an `Err` variant with an error message, demonstrating how to handle both outcomes effectively.

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
