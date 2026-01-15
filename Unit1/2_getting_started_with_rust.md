# Getting Started with Rust

## Learning Objectives

This lesson provides an introduction to Rust as a programming language and covers the essential tools needed to get started.

Overview of Rust
- Rust is a programming language known for its performance and safety features.
- The lesson begins with a quick overview of Rust and its capabilities.

Downloading the Rust Toolset
- Instructions are provided on how to download the Rust toolset necessary for development.
- This includes setting up the environment for writing and running Rust programs.

Writing and Running a Simple Program
- Learners will write a simple "Hello, World!" program to understand the basics of Rust syntax.
- The process of compiling and running the program manually is demonstrated.

Introduction to Cargo
- Cargo, a tool included in the Rust toolset, is introduced for managing Rust projects.
- The lesson shows how to use Cargo to create, build, and run applications more efficiently.

## Overview of Rust
- Rust is a flexible programming language that offers low-level access, speed, safety, and rigor, making it suitable for various applications.
- It can be used for both low-level applications, similar to C++, and high-level business applications, such as those interacting with databases.

### Installing the Rust Compiler
- Installation varies by operating system: Linux and Mac users can use a curl command to install Rustup, while Windows users need to install C++ build tools from Visual Studio first.
- After installing Rustup, users can install the Rust compiler, ensuring they check the installation by running a version command in the terminal.

### Using the Rust Playground
- The Rust Playground is an online tool that allows users to write and run Rust code without needing to install the compiler locally.
- This tool is useful for getting hands-on experience with Rust syntax and functionality before diving deeper into the language.


## Writing Your First Rust Application
This section introduces the basic syntax of Rust through a simple "Hello World" application.

**Hello World Application**
- The application is created in a file named `HelloWorld.rs` using Visual Studio Code or any text editor.
- The `main` function serves as the entry point, defined with the `fn` keyword, and currently does not take parameters or return a value.

**Understanding Macros**
- The `println!` function is actually a macro, indicated by the exclamation mark, which prints messages to the console.
- Macros in Rust allow for code expansion and can be defined using `macro_rules!`.

**Semicolons and Formatting**
- Semicolons in Rust act as statement separators, not terminators, meaning they are required between adjacent statements but not at the end of a function.
- The RustFormatter tool helps maintain code formatting by arranging curly brackets and eliminating unnecessary whitespace, ensuring adherence to Rust's layout preferences.

### Hello, World!

```rust
fn main() {
    println!("Hello, world!");
}
```

## Building and Running Your First Rust Application

The course content focuses on the fundamentals of Rust programming, aimed at both aspiring developers and experienced programmers.

**Core Concepts of Rust**
- Master the basics of Rust, including variable declaration, primitive data types, and type inference.
- Implement effective flow control using if-tests, matching, loops, and more.

**Data Management in Rust**
- Define and utilize enums to manage restricted data sets and handle potential errors.
- Organize data efficiently with arrays, tuples, vectors, and maps, leveraging Rust's powerful collection types.

**Practical Application**
- Engage in hands-on exercises to reinforce learning and apply core concepts in real-world scenarios.
- Develop skills to write efficient and robust Rust applications, laying a strong foundation for further programming endeavors.

To compile and run a Rust program:
```bash
rustc main.rs
./main
```

## Creating an Application by Using Cargo

This section focuses on using Cargo to create a new Rust application, emphasizing its importance in the Rust development process.

Understanding Cargo
- Cargo is an essential tool in the Rust toolset, used for creating, building, and running Rust applications.
- It provides a consistent directory structure and encourages best practices for application development.

Creating a New Application
- To generate a new application, use the command `Cargo new` followed by the application name in snake case (lowercase with underscores).
- Cargo creates a new folder containing the application, including a git repository, a `.gitignore` file, and a `Cargo.toml` file for package metadata.

Package Metadata and Dependencies
- The `Cargo.toml` file contains information about the application, such as its name, version, and dependencies.
- While the Rust library is comprehensive, additional third-party libraries can be added later for specific functionalities, like database access.


```bash
cargo new hello_world
cd hello_world
```

This creates a new project structure with a `Cargo.toml` file and `src/main.rs`.

## Building and Running an Application by Using Cargo

This content focuses on building and running a Rust application using Cargo, the Rust package manager.

Building Applications
- Applications can be built in debug mode for faster compilation and debugging, or in release mode for optimized performance.
- The command `cargo build` generates the application in the target debug directory, while `cargo build --release` creates an optimized version in the target release directory.

Running Applications
- Applications can be run from the command line using the executable generated in the target directories.
- The command `cargo run` builds and runs the application in debug mode, while `cargo run --release` does the same in release mode.

Syntax Checking
- The command `cargo check` performs a syntax check without generating an executable, allowing for quick validation of code correctness.

```bash
cargo build       # Build the project
cargo run         # Build and run the project
cargo build --release  # Optimized build
```