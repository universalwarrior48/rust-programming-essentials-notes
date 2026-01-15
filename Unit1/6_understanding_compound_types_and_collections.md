# Understanding Compound Types and Collections

## Learning Objectives

By the end of this section, you will:
- Create and use arrays
- Understand array techniques
- Work with tuples, vectors, and maps
- Choose the right collection type for your needs

## Creating and Using Arrays


Arrays and Tuples
- An array is a fixed-size collection where all elements are of the same type, while a tuple can contain different types of data.
- Arrays are immutable by default, but can be made mutable with the `mut` keyword, allowing changes to their values but not their size.

Collection Classes
- Vectors are resizable collections, similar to arrays, while maps are dictionaries that store key-value pairs.
- Arrays use square bracket syntax for declaration, and their length can be checked using the `length` method.

Iterating Over Arrays
- You can iterate through array elements using a for-in loop, allowing you to process or print each element.
- The course includes a project that demonstrates the use of arrays and other collection types in Rust, with practical coding examples.

### Array Basics

Arrays are fixed-size collections of elements of the same type:

```rust
let array: [i32; 5] = [1, 2, 3, 4, 5];
let array = [3; 5];  // [3, 3, 3, 3, 3]
```

### Accessing Array Elements

```rust
let array = [1, 2, 3, 4, 5];
let first = array[0];  // 1
let second = array[1]; // 2
```

## Array Techniques

Here's the summary with code samples included:

### Summary of Array Techniques in Rust

**Array Declaration Techniques**
- You can declare an array variable with a specified type and size using square brackets, allowing you to assign values later.
- For example:
  ```rust
  let a1: [i32; 5]; // Declares an array variable a1 of five 32-bit integers
  ```

**Mutable Arrays and Filler Values**
- Mutable arrays can be initialized with a filler value for all elements, allowing for later modifications.
- Example:
  ```rust
  let mut a2: [i32; 5] = [99; 5]; // Declares a mutable array a2 filled with the value 99
  a2[0] = 58; // Changes the first element to 58
  a2[4] = 25; // Changes the last element to 25
  ```

**Debug Trait in Rust**
- Rust provides a debug trait that allows data types, including arrays, to implement a formatting function for easier debugging.
- Using specific syntax in the `println!` function, you can output the entire contents of an array in a debug format:
  ```rust
  println!("{:?}", a1); // Outputs the contents of a1 using debug formatting
  println!("{:?}", a2); // Outputs the contents of a2 using debug formatting
  ```

This summary highlights the key techniques for working with arrays in Rust, along with relevant code examples.displayed.

### Iterating Over Arrays

```rust
let array = [10, 20, 30];
for element in &array {
    println!("{}", element);
}
```

### Array Methods

```rust
let array = [1, 2, 3, 4, 5];
let length = array.len();  // 5
let sum: i32 = array.iter().sum();  // 15
```

## Creating and Using Tuples

Tuples Overview
- A tuple is created using parentheses instead of square brackets, allowing for a heterogeneous collection of elements.
- Tuples can be immutable or mutable; however, their size and types cannot be changed once defined.

Accessing Tuple Elements
- Elements in a tuple are accessed using dot notation and an index (e.g., `tuple.0`), unlike arrays which use square brackets.
- Rust performs compile-time checks to ensure that the index is valid, providing safety against out-of-bounds errors.

Use Cases and Limitations
- Tuples are useful for returning multiple values from functions without needing to define a separate structure.
- Unlike arrays, tuples do not support length retrieval or iteration due to their heterogeneous nature, requiring individual access to elements.

Tuples are fixed-size collections of elements of different types:

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tup;  // destructuring
let first = tup.0;    // 500
```

### Functions Returning Tuples

```rust
fn returns_tuple() -> (i32, String) {
    (5, String::from("hello"))
}

let (num, text) = returns_tuple();
```

## Creating and Using Vectors

Vectors are growable arrays:

```rust
let mut vec = Vec::new();
vec.push(5);
vec.push(6);
vec.push(7);

let vec = vec![1, 2, 3];
```

### Vector Operations

```rust
let mut vec = vec![1, 2, 3, 4, 5];
let third = &vec[2];           // 3
let maybe_third = vec.get(2);  // Some(&3)
vec.pop();                      // 5
vec.remove(0);                  // 1
```

### Iterating Over Vectors

```rust
for element in &vec {
    println!("{}", element);
}

for element in &mut vec {
    *element += 1;
}
```

## Creating and Using Maps

Collection Structures in Rust
- Rust offers multiple collection structures, including HashMap, B-tree map, HashSet, and B-tree set, which are not imported by default.
- HashMap is a dictionary-like structure that uses hashing to store key-value pairs, allowing for quick retrieval based on keys.

Creating and Using HashMap
- Two syntaxes exist for creating a mutable HashMap, specifying key and value types.
- Items can be added using the insert function, which updates existing keys or inserts new ones, and a more cautious approach can be taken to avoid overwriting existing keys.

Retrieving and Iterating Over HashMap
- Values can be retrieved using square brackets or the get function, with the latter providing a safer lookup.
- Iteration over a HashMap is non-destructive, and the order of items is not guaranteed due to the hashing mechanism.

Debugging and Example Usage
- The HashMap structure supports debug formatting for easy output of key-value pairs.
- An example demonstrates creating a HashMap for country codes and their corresponding telephone codes, showcasing insertion, retrieval, and iteration.

Maps store key-value pairs:

```rust
use std::collections::HashMap;

let mut map = HashMap::new();
map.insert("key1", "value1");
map.insert("key2", "value2");

let value = map.get("key1");  // Some(&"value1")
```

### Map Operations

```rust
for (key, value) in &map {
    println!("{}: {}", key, value);
}

map.remove("key1");
let contains = map.contains_key("key1");
```

---

## Comparison of Collections

| Collection | Type | Size | Mutable |
|------------|------|------|---------|
| Array | Fixed | Compile-time | Can be |
| Tuple | Fixed | Compile-time | Can be |
| Vector | Dynamic | Runtime | Yes |
| HashMap | Dynamic | Runtime | Yes |

## Key Takeaways

- Arrays are fixed-size and best for known-size collections
- Tuples are useful for returning multiple values
- Vectors are flexible for variable-size collections
- HashMaps are ideal for key-value associations
- Choose the right collection based on your needs
