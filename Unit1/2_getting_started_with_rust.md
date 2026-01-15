# Getting Started with Rust

## Learning Objectives

By the end of this section, you will:
- Understand the basics of Rust programming
- Write your first Rust application
- Build and run applications using Cargo

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

Rust is a systems programming language that runs blazingly fast and prevents segfaults. It guarantees memory safety through its ownership system, making it ideal for both performance-critical and concurrent applications.

### Why Rust?
- Memory safety without garbage collection
- Fearless concurrency
- Zero-cost abstractions
- Excellent tooling and documentation

## Writing Your First Rust Application

### Hello, World!

```rust
fn main() {
    println!("Hello, world!");
}
```

## Building and Running Your First Rust Application

To compile and run a Rust program:
```bash
rustc main.rs
./main
```

## Creating an Application by Using Cargo

Cargo is Rust's package manager and build system.

```bash
cargo new hello_world
cd hello_world
```

This creates a new project structure with a `Cargo.toml` file and `src/main.rs`.

## Building and Running an Application by Using Cargo

```bash
cargo build       # Build the project
cargo run         # Build and run the project
cargo build --release  # Optimized build
```

---

## Key Takeaways

- Rust combines performance with safety
- Cargo simplifies project management and building
- The basic structure of a Rust program starts with a `main()` function
