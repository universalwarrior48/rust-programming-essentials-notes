# Understanding Compound Types and Collections

## Learning Objectives

By the end of this section, you will:
- Create and use arrays
- Understand array techniques
- Work with tuples, vectors, and maps
- Choose the right collection type for your needs

## Creating and Using Arrays

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
