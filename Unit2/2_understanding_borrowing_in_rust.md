# Understanding Borrowing and Slices in Rust

## 1. Understanding Simple Borrowing

### What is Borrowing?

Borrowing is Rust's way of allowing temporary access to data without transferring ownership. It creates a reference to data, enabling multiple parts of code to access the same data safely.

**Key Concepts:**
- References allow you to refer to a value without taking ownership
- Indicated by the `&` symbol
- Original owner retains ownership
- Data can be accessed but not moved

### Immutable Borrowing Basics

```rust
fn immutable_borrowing_basics() {
    let s = String::from("Hello, Rust!");
    
    // Create an immutable reference (borrow)
    let r1 = &s;
    
    // Both s and r1 can be used
    println!("Original: {}", s);
    println!("Reference: {}", r1);
    
    // s still owns the data
    println!("Original still valid: {}", s);
}
```

**What Happens Under the Hood:**
```rust
fn borrowing_memory_layout() {
    let s = String::from("Hello");
    
    // Stack memory:
    // s: [ptr → heap, len: 5, cap: 5]
    // Heap: ['H', 'e', 'l', 'l', 'o']
    
    let r = &s;
    
    // Stack memory:
    // s: [ptr → heap, len: 5, cap: 5]
    // r: [ptr → s]  // r points to s, not directly to heap
    
    // r borrows s, but s still owns the heap data
    println!("{}", r);
}
```

### Reference Syntax

```rust
fn reference_syntax() {
    let x = 42;
    
    // Create a reference - ampersand before the value
    let r = &x;
    
    // Explicit type annotation - ampersand before the type
    let r2: &i32 = &x;
    
    // Dereferencing - access the value (usually automatic)
    println!("Value through reference: {}", *r);
    println!("Automatic dereferencing: {}", r);
    
    // Both syntaxes are equivalent
    assert_eq!(r, r2);
}
```

### Borrowing in Function Calls

```rust
// Function that borrows a String
fn calculate_length(s: &String) -> usize {
    s.len()  // Can read the value
    // s.push_str("!"); // ERROR: can't modify borrowed value
}

// Function that borrows any string-like data
fn print_text(text: &str) {
    println!("Text: {}", text);
}

fn borrowing_in_functions() {
    let my_string = String::from("Hello");
    
    // Borrow my_string
    let length = calculate_length(&my_string);
    
    // my_string is still valid!
    println!("String '{}' has length {}", my_string, length);
    
    // Can borrow multiple times
    print_text(&my_string);
    print_text(&my_string);
    println!("Original still accessible: {}", my_string);
}
```

### Multiple Immutable References

```rust
fn multiple_immutable_references() {
    let data = vec![1, 2, 3, 4, 5];
    
    // Create multiple immutable references
    let r1 = &data;
    let r2 = &data;
    let r3 = &data;
    
    // All references can be used simultaneously
    println!("r1: {:?}", r1);
    println!("r2: {:?}", r2);
    println!("r3: {:?}", r3);
    println!("Original: {:?}", data);
    
    // Calculate using multiple references
    let sum1: i32 = r1.iter().sum();
    let sum2: i32 = r2.iter().sum();
    
    assert_eq!(sum1, sum2);
}
```

### Mutable Borrowing

Mutable references allow you to modify borrowed data:

```rust
fn mutable_borrowing_basics() {
    let mut s = String::from("Hello");
    
    // Create a mutable reference
    let r = &mut s;
    
    // Can modify through mutable reference
    r.push_str(", World!");
    
    println!("Modified string: {}", r);
    
    // After r is done being used, s can be used again
    println!("Original variable: {}", s);
}
```

### Mutable Borrowing with Functions

```rust
// Function that mutably borrows
fn append_exclamation(text: &mut String) {
    text.push('!');
}

fn make_uppercase(text: &mut String) {
    *text = text.to_uppercase();
}

fn mutable_borrow_functions() {
    let mut message = String::from("Hello");
    
    // Pass mutable reference
    append_exclamation(&mut message);
    println!("After append: {}", message);  // "Hello!"
    
    make_uppercase(&mut message);
    println!("After uppercase: {}", message);  // "HELLO!"
}
```

### Borrowing with Different Types

```rust
fn borrowing_various_types() {
    // Borrowing integers
    let num = 42;
    let num_ref = &num;
    println!("Number: {}", num_ref);
    
    // Borrowing vectors
    let vec = vec![1, 2, 3];
    let vec_ref = &vec;
    println!("Vector length: {}", vec_ref.len());
    
    // Borrowing structs
    #[derive(Debug)]
    struct Point {
        x: i32,
        y: i32,
    }
    
    let point = Point { x: 10, y: 20 };
    let point_ref = &point;
    println!("Point: {:?}", point_ref);
    println!("X coordinate: {}", point_ref.x);
}
```

### Explicit Dereferencing

```rust
fn dereferencing_examples() {
    let x = 42;
    let r = &x;
    
    // Explicit dereferencing with *
    let value = *r;
    println!("Dereferenced value: {}", value);
    
    // Automatic dereferencing in many contexts
    println!("Direct use: {}", r);
    
    // Mutable reference dereferencing
    let mut y = 10;
    let mr = &mut y;
    *mr += 5;  // Must dereference to modify
    println!("Modified value: {}", y);
    
    // Working with complex types
    let mut s = String::from("Hello");
    let sr = &mut s;
    (*sr).push_str(", World!");  // Explicit dereference
    sr.push('!');                 // Automatic dereference
    println!("String: {}", s);
}
```

### Borrowing and Ownership Together

```rust
fn borrowing_and_ownership() {
    let s1 = String::from("Hello");
    
    // Borrow s1
    let len = get_length(&s1);
    println!("Length: {}", len);
    
    // s1 is still owned and can be moved
    let s2 = s1;  // Move occurs here
    // println!("{}", s1);  // ERROR: s1 was moved
    
    println!("{}", s2);
}

fn get_length(s: &String) -> usize {
    s.len()
}
```

### Practical Example: Processing Data Without Taking Ownership

```rust
#[derive(Debug)]
struct User {
    name: String,
    age: u32,
    email: String,
}

// Borrow to read data
fn display_user(user: &User) {
    println!("Name: {}", user.name);
    println!("Age: {}", user.age);
    println!("Email: {}", user.email);
}

// Borrow to validate data
fn is_adult(user: &User) -> bool {
    user.age >= 18
}

// Mutably borrow to update data
fn update_email(user: &mut User, new_email: String) {
    user.email = new_email;
}

fn main() {
    let mut user = User {
        name: String::from("Alice"),
        age: 25,
        email: String::from("alice@example.com"),
    };
    
    // Multiple immutable borrows
    display_user(&user);
    if is_adult(&user) {
        println!("User is an adult");
    }
    
    // Mutable borrow to update
    update_email(&mut user, String::from("alice.new@example.com"));
    
    // Can still use the original variable
    println!("Updated user: {:?}", user);
}
```

### Benefits of Borrowing

```rust
fn benefits_demonstration() {
    let large_data = vec![0; 1_000_000];
    
    // Without borrowing: expensive clone
    fn process_with_clone(data: Vec<i32>) -> usize {
        data.len()
    }
    
    // With borrowing: no copy needed
    fn process_with_borrow(data: &Vec<i32>) -> usize {
        data.len()
    }
    
    // Borrowing is efficient - no data copy
    let len = process_with_borrow(&large_data);
    
    // Original data still accessible
    println!("Data length: {}", len);
    println!("Original still valid with {} elements", large_data.len());
}
```

### Common Patterns

```rust
// Pattern 1: Read-only access
fn read_only_pattern() {
    let numbers = vec![1, 2, 3, 4, 5];
    
    fn sum(nums: &Vec<i32>) -> i32 {
        nums.iter().sum()
    }
    
    let total = sum(&numbers);
    println!("Sum: {}, Original: {:?}", total, numbers);
}

// Pattern 2: Modify in place
fn modify_in_place_pattern() {
    let mut numbers = vec![1, 2, 3, 4, 5];
    
    fn double_all(nums: &mut Vec<i32>) {
        for num in nums.iter_mut() {
            *num *= 2;
        }
    }
    
    double_all(&mut numbers);
    println!("Doubled: {:?}", numbers);
}

// Pattern 3: Chain borrows
fn chain_borrows_pattern() {
    let mut text = String::from("hello");
    
    fn add_comma(s: &mut String) {
        s.push(',');
    }
    
    fn add_space(s: &mut String) {
        s.push(' ');
    }
    
    fn add_world(s: &mut String) {
        s.push_str("world");
    }
    
    add_comma(&mut text);
    add_space(&mut text);
    add_world(&mut text);
    
    println!("Result: {}", text);
}
```
## 2. Understanding the Rust Borrow Checker

### What is the Borrow Checker?

The borrow checker is Rust's compile-time mechanism that enforces memory safety rules. It analyzes your code to ensure that references are used safely, preventing data races and dangling pointers at compile time.

**Core Rules:**
1. At any given time, you can have either:
   - One mutable reference, OR
   - Any number of immutable references
2. References must always be valid (no dangling references)

### Rule 1: Multiple Immutable References

You can create many immutable references because they don't modify the data:

```rust
fn multiple_immutable_refs() {
    let data = String::from("Hello, Rust!");
    
    // Create multiple immutable references - all valid
    let r1 = &data;
    let r2 = &data;
    let r3 = &data;
    let r4 = &data;
    
    // All can be used simultaneously
    println!("r1: {}", r1);
    println!("r2: {}", r2);
    println!("r3: {}", r3);
    println!("r4: {}", r4);
    println!("Original: {}", data);
    
    // This is safe because none of them modify the data
}
```

### Rule 2: Only One Mutable Reference

Once you create a mutable reference, no other references can exist:

```rust
fn single_mutable_ref() {
    let mut data = String::from("Hello");
    
    // Create ONE mutable reference
    let r1 = &mut data;
    r1.push_str(", World!");
    
    println!("r1: {}", r1);
    
    // After r1 is done, we can create another reference
    let r2 = &data;
    println!("r2: {}", r2);
}

fn mutable_ref_error_example() {
    let mut data = String::from("Hello");
    
    let r1 = &mut data;
    // let r2 = &mut data;  // ERROR: cannot borrow as mutable more than once
    // let r3 = &data;      // ERROR: cannot borrow as immutable while borrowed as mutable
    
    r1.push_str("!");
    println!("{}", r1);
    
    // Now r1 is done, we can create new references
    let r2 = &data;
    println!("{}", r2);
}
```

### Reference Scope and Lifetime

The borrow checker tracks where references are used (their scope):

```rust
fn reference_scope() {
    let mut s = String::from("Hello");
    
    let r1 = &s;
    let r2 = &s;
    println!("{} {}", r1, r2);
    // r1 and r2 are no longer used after this point
    
    // This is OK because r1 and r2 are no longer in scope
    let r3 = &mut s;
    r3.push_str(", World!");
    println!("{}", r3);
}
```

**Non-Lexical Lifetimes (NLL):**
```rust
fn non_lexical_lifetimes() {
    let mut data = vec![1, 2, 3];
    
    let r1 = &data;
    println!("r1: {:?}", r1);
    // r1's last use is here ^
    
    // Even though r1 is technically still in lexical scope,
    // the borrow checker knows it's no longer used
    let r2 = &mut data;
    r2.push(4);
    println!("r2: {:?}", r2);
}
```

### Why These Rules Exist

The borrow checker prevents data races at compile time:

```rust
// This pattern would cause a data race if allowed
fn prevented_data_race() {
    let mut data = vec![1, 2, 3];
    
    // Imagine if this was allowed:
    let r1 = &data;        // Reading
    let r2 = &mut data;    // ERROR: Writing while reading!
    
    // If both existed simultaneously:
    // - r2 might resize the vector (reallocating memory)
    // - r1 would point to invalid memory (dangling pointer)
    // - CRASH or undefined behavior!
    
    // Rust prevents this at compile time!
}
```

### Visualizing Borrow Checker Rules

```rust
fn borrow_checker_visualization() {
    let mut value = 100;
    
    // Scenario 1: Multiple immutable borrows (OK)
    {
        let r1 = &value;
        let r2 = &value;
        let r3 = &value;
        println!("Reading: {} {} {}", r1, r2, r3);
        // All can coexist because they only read
    }
    
    // Scenario 2: One mutable borrow (OK)
    {
        let r1 = &mut value;
        *r1 += 10;
        println!("Modified: {}", r1);
        // Only one mutable reference exists
    }
    
    // Scenario 3: Mix immutable and mutable (ERROR if overlapping)
    {
        let r1 = &value;
        println!("Read: {}", r1);
        // r1 is done being used
        
        let r2 = &mut value;
        *r2 += 20;
        println!("Modified: {}", r2);
        // This works because r1 is no longer active
    }
}
```

### Common Borrow Checker Errors

**Error 1: Cannot borrow as mutable more than once**
```rust
fn error_multiple_mutable() {
    let mut s = String::from("hello");
    
    let r1 = &mut s;
    // let r2 = &mut s;  // ERROR: cannot borrow `s` as mutable more than once
    
    r1.push_str(" world");
    println!("{}", r1);
    
    // Solution: Use one at a time
    let r2 = &mut s;
    r2.push('!');
    println!("{}", r2);
}
```

**Error 2: Cannot borrow as mutable while immutable borrow exists**
```rust
fn error_mutable_while_immutable() {
    let mut s = String::from("hello");
    
    let r1 = &s;
    // let r2 = &mut s;  // ERROR: cannot borrow as mutable
    
    println!("{}", r1);
    
    // Solution: Wait until immutable references are done
    let r2 = &mut s;
    r2.push_str(" world");
    println!("{}", r2);
}
```

**Error 3: Cannot borrow as immutable while mutable borrow exists**
```rust
fn error_immutable_while_mutable() {
    let mut s = String::from("hello");
    
    let r1 = &mut s;
    // let r2 = &s;  // ERROR: cannot borrow as immutable
    
    r1.push_str(" world");
    println!("{}", r1);
    
    // Solution: Complete mutable operations first
    let r2 = &s;
    println!("{}", r2);
}
```

### Working with the Borrow Checker

**Pattern 1: Sequential borrows**
```rust
fn sequential_borrows() {
    let mut data = vec![1, 2, 3];
    
    // Immutable borrow scope
    {
        let r = &data;
        println!("Read: {:?}", r);
    }  // r goes out of scope
    
    // Mutable borrow scope
    {
        let r = &mut data;
        r.push(4);
        println!("Modified: {:?}", r);
    }  // r goes out of scope
    
    // Another immutable borrow
    {
        let r = &data;
        println!("Read again: {:?}", r);
    }
}
```

**Pattern 2: Early dropping**
```rust
fn early_dropping() {
    let mut data = String::from("Hello");
    
    let r1 = &data;
    println!("{}", r1);
    drop(r1);  // Explicitly drop the reference
    // Or just stop using r1
    
    // Now we can create a mutable reference
    let r2 = &mut data;
    r2.push_str(", World!");
    println!("{}", r2);
}
```

**Pattern 3: Splitting borrows**
```rust
fn splitting_borrows() {
    let mut data = [1, 2, 3, 4, 5];
    
    // Can borrow different parts mutably
    let (left, right) = data.split_at_mut(2);
    
    left[0] = 10;
    right[0] = 30;
    
    println!("Modified array: {:?}", data);
}
```

### Borrow Checker with Structs

```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: u32,
}

fn struct_borrowing() {
    let mut person = Person {
        name: String::from("Alice"),
        age: 30,
    };
    
    // Borrow fields independently
    let name_ref = &person.name;
    let age_ref = &mut person.age;
    
    // Can't borrow person as a whole while fields are borrowed
    // let person_ref = &person;  // ERROR
    
    *age_ref += 1;
    println!("Name: {}, Age: {}", name_ref, age_ref);
}

fn struct_field_independence() {
    let mut person = Person {
        name: String::from("Bob"),
        age: 25,
    };
    
    // Can borrow different fields mutably
    let name = &mut person.name;
    let age = &mut person.age;
    
    name.push_str(" Smith");
    *age += 1;
    
    println!("{:?}", person);
}
```

### Borrow Checker with Collections

```rust
fn collection_borrowing() {
    let mut vec = vec![1, 2, 3, 4, 5];
    
    // Immutable borrow to iterate
    for item in &vec {
        println!("Item: {}", item);
    }
    
    // Mutable borrow to modify
    for item in &mut vec {
        *item *= 2;
    }
    
    println!("Doubled: {:?}", vec);
}

fn collection_error_example() {
    let mut vec = vec![1, 2, 3];
    
    let first = &vec[0];  // Immutable borrow
    
    // vec.push(4);  // ERROR: can't mutate while borrowed
    
    println!("First: {}", first);
    
    // Now we can mutate
    vec.push(4);
    println!("Vec: {:?}", vec);
}
```

### Advanced: Interior Mutability

For cases where you need to mutate through shared references, Rust provides `Cell` and `RefCell`:

```rust
use std::cell::RefCell;

fn interior_mutability() {
    let data = RefCell::new(vec![1, 2, 3]);
    
    // Can borrow mutably through shared reference
    data.borrow_mut().push(4);
    
    // Multiple immutable borrows
    {
        let r1 = data.borrow();
        let r2 = data.borrow();
        println!("r1: {:?}, r2: {:?}", r1, r2);
    }  // Borrows dropped here
    
    // Another mutable borrow
    data.borrow_mut().push(5);
    
    println!("Final: {:?}", data.borrow());
}
```

### Practical Examples

**Example 1: Processing configuration**
```rust
struct Config {
    host: String,
    port: u16,
    timeout: u32,
}

fn validate_config(config: &Config) -> bool {
    !config.host.is_empty() && config.port > 0
}

fn update_timeout(config: &mut Config, timeout: u32) {
    config.timeout = timeout;
}

fn main() {
    let mut config = Config {
        host: String::from("localhost"),
        port: 8080,
        timeout: 30,
    };
    
    // Multiple reads
    if validate_config(&config) {
        println!("Config is valid");
    }
    
    // Single write
    update_timeout(&mut config, 60);
    
    println!("Host: {}, Port: {}, Timeout: {}", 
             config.host, config.port, config.timeout);
}
```

**Example 2: Buffer management**
```rust
fn process_buffer(buffer: &mut Vec<u8>) {
    buffer.clear();
    buffer.extend_from_slice(b"Hello, Rust!");
}

fn read_buffer(buffer: &Vec<u8>) -> String {
    String::from_utf8_lossy(buffer).to_string()
}

fn main() {
    let mut buffer = Vec::new();
    
    // Write to buffer
    process_buffer(&mut buffer);
    
    // Read from buffer (multiple times OK)
    let content = read_buffer(&buffer);
    println!("Content: {}", content);
    
    let content2 = read_buffer(&buffer);
    println!("Content again: {}", content2);
}
```

### Borrow Checker Best Practices

1. **Minimize borrow scope**: Use references for as short a time as possible
2. **Prefer immutable borrows**: Use `&T` unless you need to mutate
3. **Use blocks**: Create explicit scopes to end borrows early
4. **Understand NLL**: The borrow checker is smart about when references are actually used
5. **Split structures**: Borrow individual fields when possible

```rust
fn best_practices_example() {
    let mut data = vec![1, 2, 3, 4, 5];
    
    // Good: Narrow scope for immutable borrow
    {
        let sum: i32 = data.iter().sum();
        println!("Sum: {}", sum);
    }
    
    // Good: Narrow scope for mutable borrow
    {
        data.push(6);
    }
    
    // Good: Last use of reference is clear
    let first = &data[0];
    println!("First: {}", first);
    // first is no longer used
    
    data.push(7);  // OK to mutate now
    
    println!("Final: {:?}", data);
}
```
## 3. Introduction to String Slices

### Understanding String Types in Rust

Rust has two primary string types that serve different purposes:

1. **`String`**: Owned, heap-allocated, growable string
2. **`&str`** (String Slice): Borrowed reference to string data

### The String Type

`String` is a struct that owns its data on the heap:

```rust
fn string_type_overview() {
    let mut s = String::from("Hello");
    
    // String structure contains:
    // - ptr: pointer to heap data
    // - len: current length
    // - cap: capacity
    
    println!("Length: {}", s.len());
    println!("Capacity: {}", s.capacity());
    
    // String owns the data and is responsible for cleanup
    s.push_str(", World!");
    
    // When s goes out of scope, heap memory is automatically freed
}
```

**Memory Layout:**
```rust
fn string_memory() {
    let s = String::from("Rust");
    
    // Stack (String structure):
    // ┌─────────┬─────┬─────┐
    // │ ptr     │ len │ cap │
    // │ ↓       │  4  │  4  │
    // └─────────┴─────┴─────┘
    //
    // Heap (actual data):
    // ['R', 'u', 's', 't']
    
    println!("{}", s);
}  // Heap memory freed here
```

### The &str Type (String Slice)

`&str` is a reference to string data, often called a string slice:

```rust
fn string_slice_basics() {
    // String literal: type is &str
    let literal: &str = "Hello, Rust!";
    
    // String slice is a fat pointer containing:
    // - Pointer to first byte of the string
    // - Length in bytes
    
    println!("Slice: {}", literal);
    println!("Length: {}", literal.len());
}
```

**String Slice Memory Layout:**
```rust
fn slice_memory() {
    let s = String::from("Hello, Rust!");
    let slice: &str = &s[0..5];  // "Hello"
    
    // s (String on stack):
    // ┌─────────┬──────┬──────┐
    // │ ptr     │ len  │ cap  │
    // │ ↓       │  12  │  12  │
    // └─────────┴──────┴──────┘
    //    │
    //    ↓ Heap: ['H','e','l','l','o',',',' ','R','u','s','t','!']
    //
    // slice (&str on stack):
    // ┌─────────┬──────┐
    // │ ptr     │ len  │
    // │ ↓       │  5   │
    // └─────────┴──────┘
    //    │
    //    └→ Points to 'H' in the heap
    
    println!("Slice: {}", slice);
}
```

### String Literals Are String Slices

String literals are `&str` with a `'static` lifetime:

```rust
fn string_literals() {
    // Type is &'static str
    let greeting: &'static str = "Hello";
    
    // String literals are stored in the program binary
    // They have a 'static lifetime (live for entire program)
    
    println!("Greeting: {}", greeting);
    
    // Can be used anywhere that expects &str
    print_message(greeting);
}

fn print_message(msg: &str) {
    println!("Message: {}", msg);
}
```

### Creating String Slices from Strings

```rust
fn creating_slices() {
    let s = String::from("Hello, Rust!");
    
    // Borrow the entire String as a slice
    let slice1: &str = &s;
    println!("Full slice: {}", slice1);
    
    // Borrow a portion using range syntax
    let slice2: &str = &s[0..5];   // "Hello"
    let slice3: &str = &s[7..11];  // "Rust"
    
    println!("Partial slices: {} and {}", slice2, slice3);
    
    // Convenient range shortcuts
    let slice4 = &s[..5];     // From start to index 5
    let slice5 = &s[7..];     // From index 7 to end
    let slice6 = &s[..];      // Entire string
    
    println!("Shortcuts: {}, {}, {}", slice4, slice5, slice6);
}
```

### String to &str Coercion

Rust automatically converts `&String` to `&str` (deref coercion):

```rust
fn deref_coercion() {
    let s = String::from("Hello");
    
    // Function expects &str
    fn print_str(text: &str) {
        println!("{}", text);
    }
    
    // These all work due to deref coercion
    print_str(&s);              // &String → &str
    print_str(&s[..]);          // Explicit slice
    print_str("literal");       // &str literal
}
```

### Why Use &str in Function Parameters?

Using `&str` makes functions more flexible:

```rust
// Less flexible: only accepts String references
fn greet_string(name: &String) -> String {
    format!("Hello, {}!", name)
}

// More flexible: accepts both &String and &str
fn greet_str(name: &str) -> String {
    format!("Hello, {}!", name)
}

fn comparison() {
    let owned = String::from("Alice");
    let literal = "Bob";
    
    // greet_string only works with &String
    greet_string(&owned);
    // greet_string(literal);  // ERROR: expected &String
    
    // greet_str works with both
    greet_str(&owned);   // &String coerces to &str
    greet_str(literal);  // &str works directly
}
```

### Slices Don't Own Data

String slices are views into existing data:

```rust
fn slices_dont_own() {
    let s = String::from("Hello");
    let slice = &s[..];
    
    // slice is just a view, doesn't own the data
    // s still owns the data
    
    println!("Owner: {}", s);
    println!("Slice: {}", slice);
    
    // Can't drop s while slice exists
    // drop(s);  // ERROR: cannot move out of `s` because it is borrowed
    
    println!("Slice still valid: {}", slice);
}
```

### Mutable String References

You can borrow a String mutably:

```rust
fn mutable_string_borrow() {
    let mut s = String::from("Hello");
    
    // Mutable borrow of String
    let s_mut: &mut String = &mut s;
    
    // Can modify through the mutable reference
    s_mut.push_str(", World!");
    s_mut.push('!');
    
    println!("Modified: {}", s_mut);
    
    // After s_mut is done, can use s again
    println!("Original: {}", s);
}
```

### String vs &str: When to Use Which

```rust
// Use String when:
// 1. You need to own the string data
// 2. You need to modify/grow the string
// 3. You're building strings dynamically

fn use_string() -> String {
    let mut result = String::new();
    result.push_str("Built ");
    result.push_str("dynamically");
    result  // Return owned string
}

// Use &str when:
// 1. You only need to read the string
// 2. You want to accept both String and &str
// 3. You're working with string literals

fn use_str(text: &str) -> usize {
    text.len()
}

fn when_to_use() {
    // Building a string: use String
    let owned = use_string();
    println!("Owned: {}", owned);
    
    // Reading a string: use &str
    let len1 = use_str(&owned);
    let len2 = use_str("literal");
    println!("Lengths: {}, {}", len1, len2);
}
```

### Converting Between String and &str

```rust
fn conversions() {
    // &str → String
    let slice: &str = "Hello";
    let owned1: String = slice.to_string();
    let owned2: String = slice.to_owned();
    let owned3: String = String::from(slice);
    
    println!("Owned strings: {}, {}, {}", owned1, owned2, owned3);
    
    // String → &str (borrowing)
    let s = String::from("World");
    let slice1: &str = &s;
    let slice2: &str = s.as_str();
    let slice3: &str = &s[..];
    
    println!("Slices: {}, {}, {}", slice1, slice2, slice3);
}
```

### Practical Examples

**Example 1: First word finder**
```rust
fn first_word(text: &str) -> &str {
    let bytes = text.as_bytes();
    
    for (i, &byte) in bytes.iter().enumerate() {
        if byte == b' ' {
            return &text[..i];
        }
    }
    
    text  // No space found, return entire string
}

fn main() {
    let sentence = String::from("Hello Rust World");
    let word = first_word(&sentence);
    println!("First word: {}", word);
    
    // Works with literals too
    let word2 = first_word("Quick brown fox");
    println!("First word: {}", word2);
}
```

**Example 2: String processing**
```rust
fn process_text(input: &str) -> String {
    let trimmed = input.trim();
    let lowercase = trimmed.to_lowercase();
    format!("Processed: {}", lowercase)
}

fn main() {
    let text = String::from("  HELLO, RUST!  ");
    let result = process_text(&text);
    
    println!("Original: '{}'", text);
    println!("Result: '{}'", result);
}
```

**Example 3: String validation**
```rust
fn is_valid_email(email: &str) -> bool {
    email.contains('@') && email.contains('.') && email.len() > 3
}

fn validate_input(input: &str) -> Result<(), String> {
    if input.is_empty() {
        Err(String::from("Input cannot be empty"))
    } else if input.len() < 3 {
        Err(String::from("Input too short"))
    } else {
        Ok(())
    }
}

fn main() {
    let email1 = "user@example.com";
    let email2 = "invalid";
    
    println!("Email 1 valid: {}", is_valid_email(email1));
    println!("Email 2 valid: {}", is_valid_email(email2));
    
    match validate_input("Hi") {
        Ok(_) => println!("Valid input"),
        Err(e) => println!("Error: {}", e),
    }
}
```

### UTF-8 and String Slices

String slices must respect UTF-8 character boundaries:

```rust
fn utf8_slicing() {
    let hello = String::from("Hello");
    let slice = &hello[0..2];  // OK: "He"
    println!("{}", slice);
    
    let russian = String::from("Здравствуйте");
    // let bad = &russian[0..1];  // PANIC: not on char boundary!
    
    let good = &russian[0..2];  // OK: "З" (Cyrillic Z is 2 bytes)
    println!("{}", good);
    
    // Safe way: iterate by characters
    let first_char = russian.chars().next().unwrap();
    println!("First char: {}", first_char);
}
```

### String Slice Lifetime

String slices have lifetimes tied to their source:

```rust
fn slice_lifetime() {
    let s = String::from("Hello");
    let slice = &s[..];
    
    // slice cannot outlive s
    println!("{}", slice);
    
    // This would fail:
    // drop(s);  // ERROR: cannot drop s while slice exists
}

// Returning string slices
fn get_greeting() -> &'static str {
    "Hello"  // String literal has 'static lifetime
}

// Cannot return slice of local String
// fn bad_function() -> &str {
//     let s = String::from("Hello");
//     &s[..]  // ERROR: s is dropped, slice would dangle
// }

// Solution: Return owned String
fn good_function() -> String {
    String::from("Hello")
}
```
## 4. String Slice Techniques

### Anatomy of a String Slice

A string slice (`&str`) is a fat pointer containing two pieces of information:

```rust
fn slice_structure() {
    let text = String::from("Hello, Rust!");
    let slice = &text[7..11];  // "Rust"
    
    // slice contains:
    // 1. Pointer to the byte at index 7 ('R')
    // 2. Length: 4 bytes
    
    println!("Slice: {}", slice);
    println!("Length: {} bytes", slice.len());
    
    // Get pointer as usize (for demonstration)
    let ptr = slice.as_ptr();
    println!("Pointer address: {:p}", ptr);
}
```

### Range Syntax for Slicing

Rust provides flexible range syntax for creating slices:

```rust
fn range_syntax() {
    let text = String::from("Hello, World!");
    
    // Full range specifications
    let slice1 = &text[0..5];      // "Hello"
    let slice2 = &text[7..12];     // "World"
    
    // Shorthand: from start
    let slice3 = &text[..5];       // "Hello" (same as [0..5])
    
    // Shorthand: to end
    let slice4 = &text[7..];       // "World!" (from 7 to end)
    
    // Shorthand: entire string
    let slice5 = &text[..];        // "Hello, World!"
    
    // Using RangeTo, RangeFrom, RangeFull
    let slice6 = &text[..=5];      // "Hello," (inclusive end)
    
    println!("slice1: {}", slice1);
    println!("slice2: {}", slice2);
    println!("slice3: {}", slice3);
    println!("slice4: {}", slice4);
    println!("slice5: {}", slice5);
    println!("slice6: {}", slice6);
}
```

### UTF-8 Encoding and String Slices

Rust strings are UTF-8 encoded, which affects how slicing works:

```rust
fn utf8_encoding() {
    // ASCII characters: 1 byte each
    let ascii = String::from("Hello");
    println!("ASCII length: {} bytes, {} chars", 
             ascii.len(), 
             ascii.chars().count());
    
    // Cyrillic characters: 2 bytes each
    let cyrillic = String::from("Привет");  // Russian "Hello"
    println!("Cyrillic length: {} bytes, {} chars",
             cyrillic.len(),
             cyrillic.chars().count());
    
    // Chinese characters: 3 bytes each
    let chinese = String::from("你好");  // "Hello"
    println!("Chinese length: {} bytes, {} chars",
             chinese.len(),
             chinese.chars().count());
    
    // Emojis: 4 bytes each
    let emoji = String::from("👋🌍");
    println!("Emoji length: {} bytes, {} chars",
             emoji.len(),
             emoji.chars().count());
}
```

### Slicing Must Respect Character Boundaries

```rust
fn character_boundaries() {
    let text = String::from("Hello");
    let slice = &text[0..2];  // OK: "He" (both ASCII)
    println!("{}", slice);
    
    let russian = String::from("Привет");
    
    // Each Cyrillic char is 2 bytes
    let good_slice = &russian[0..2];   // OK: "П" (first char, 2 bytes)
    println!("{}", good_slice);
    
    // let bad_slice = &russian[0..1];  // PANIC! Not on char boundary
    
    // let bad_slice2 = &russian[0..3];  // PANIC! Splits second char
    
    let good_slice2 = &russian[0..4];  // OK: "Пр" (first 2 chars, 4 bytes)
    println!("{}", good_slice2);
}
```

### Iterating Over String Slices

**Iterating by characters:**
```rust
fn iterate_chars() {
    let text = "Hello, 世界! 🌍";
    
    println!("Characters:");
    for (i, ch) in text.chars().enumerate() {
        println!("  {}: '{}' (Unicode: U+{:04X})", i, ch, ch as u32);
    }
    
    // Count characters
    let char_count = text.chars().count();
    println!("Total characters: {}", char_count);
}
```

**Iterating by bytes:**
```rust
fn iterate_bytes() {
    let text = "Hi 👋";
    
    println!("Bytes:");
    for (i, byte) in text.bytes().enumerate() {
        println!("  {}: {} ({:08b})", i, byte, byte);
    }
    
    // Count bytes
    let byte_count = text.len();
    println!("Total bytes: {}", byte_count);
}
```

**Iterating by grapheme clusters (requires unicode-segmentation crate):**
```rust
// This would require: unicode-segmentation = "1.10"
fn iterate_graphemes_concept() {
    // Some characters are actually multiple Unicode code points
    let text = "é";  // Can be: "é" (1 code point) or "e" + "´" (2 code points)
    
    println!("String: {}", text);
    println!("Bytes: {}", text.len());
    println!("Chars: {}", text.chars().count());
    
    // For proper grapheme handling, use unicode-segmentation crate
}
```

### String Slice Methods

**Checking content:**
```rust
fn slice_checking_methods() {
    let text = "Hello, Rust!";
    
    // Check if empty
    println!("Is empty: {}", text.is_empty());
    
    // Check prefix/suffix
    println!("Starts with 'Hello': {}", text.starts_with("Hello"));
    println!("Ends with '!': {}", text.ends_with("!"));
    
    // Check for substring
    println!("Contains 'Rust': {}", text.contains("Rust"));
    
    // Find position
    match text.find("Rust") {
        Some(pos) => println!("'Rust' found at byte {}", pos),
        None => println!("'Rust' not found"),
    }
}
```

**Trimming:**
```rust
fn slice_trimming() {
    let text = "  Hello, Rust!  \n\t";
    
    // Remove leading/trailing whitespace
    let trimmed = text.trim();
    println!("Trimmed: '{}'", trimmed);
    
    // Remove only leading whitespace
    let trim_start = text.trim_start();
    println!("Trim start: '{}'", trim_start);
    
    // Remove only trailing whitespace
    let trim_end = text.trim_end();
    println!("Trim end: '{}'", trim_end);
    
    // Trim specific characters
    let custom = "###Hello###";
    let trimmed_custom = custom.trim_matches('#');
    println!("Custom trim: '{}'", trimmed_custom);
}
```

**Splitting:**
```rust
fn slice_splitting() {
    let text = "apple,banana,cherry,date";
    
    // Split by delimiter
    let fruits: Vec<&str> = text.split(',').collect();
    println!("Fruits: {:?}", fruits);
    
    // Split with limit
    let limited: Vec<&str> = text.splitn(3, ',').collect();
    println!("Limited split: {:?}", limited);
    
    // Split by whitespace
    let sentence = "Hello Rust World";
    let words: Vec<&str> = sentence.split_whitespace().collect();
    println!("Words: {:?}", words);
    
    // Split by predicate
    let data = "a1b2c3d4";
    let parts: Vec<&str> = data.split(|c: char| c.is_numeric()).collect();
    println!("Split by predicate: {:?}", parts);
}
```

**Replacing:**
```rust
fn slice_replacing() {
    let text = "Hello, World!";
    
    // Replace all occurrences
    let replaced = text.replace("o", "0");
    println!("Replaced: {}", replaced);
    
    // Replace first n occurrences
    let replaced_n = text.replacen("l", "L", 2);
    println!("Replaced n: {}", replaced_n);
    
    // Note: replace returns a new String
    println!("Original unchanged: {}", text);
}
```

**Case conversion:**
```rust
fn slice_case_conversion() {
    let text = "Hello, Rust!";
    
    // Convert to uppercase (returns new String)
    let upper = text.to_uppercase();
    println!("Uppercase: {}", upper);
    
    // Convert to lowercase (returns new String)
    let lower = text.to_lowercase();
    println!("Lowercase: {}", lower);
    
    // Works with Unicode
    let turkish = "İstanbul";  // Turkish capital İ
    println!("Turkish lowercase: {}", turkish.to_lowercase());
}
```

### Parsing from String Slices

```rust
fn slice_parsing() {
    let number_str = "42";
    let float_str = "3.14";
    let bool_str = "true";
    
    // Parse to number
    let number: i32 = number_str.parse().unwrap();
    println!("Parsed number: {}", number);
    
    // Parse to float
    let float: f64 = float_str.parse().unwrap();
    println!("Parsed float: {}", float);
    
    // Parse to bool
    let boolean: bool = bool_str.parse().unwrap();
    println!("Parsed bool: {}", boolean);
    
    // Handle parse errors
    let invalid = "not a number";
    match invalid.parse::<i32>() {
        Ok(n) => println!("Parsed: {}", n),
        Err(e) => println!("Parse error: {}", e),
    }
}
```

### Mutable String Slices

While `&str` is immutable, you can use `&mut str` for in-place modifications:

```rust
fn mutable_string_slices() {
    let mut buffer = String::from("hello, world!");
    
    // Get a mutable slice
    let slice = buffer.as_mut_str();
    
    // Convert to uppercase in place
    slice.make_ascii_uppercase();
    
    println!("Modified: {}", buffer);  // "HELLO, WORLD!"
}

fn mutable_slice_operations() {
    let mut data = String::from("rust programming");
    
    // Modify part of the string
    {
        let slice = &mut data[..4];
        slice.make_ascii_uppercase();
    }
    
    println!("Partially modified: {}", data);  // "RUST programming"
}
```

### Advanced Slicing Techniques

**Safe slicing with get:**
```rust
fn safe_slicing() {
    let text = "Hello";
    
    // Using indexing (can panic)
    let slice1 = &text[0..2];  // OK
    // let bad = &text[0..100];  // PANIC: out of bounds
    
    // Using get (returns Option)
    match text.get(0..2) {
        Some(s) => println!("Safe slice: {}", s),
        None => println!("Out of bounds"),
    }
    
    match text.get(0..100) {
        Some(s) => println!("Safe slice: {}", s),
        None => println!("Out of bounds safely handled"),
    }
}
```

**Working with indices:**
```rust
fn index_operations() {
    let text = "Hello, Rust!";
    
    // Find index of substring
    if let Some(pos) = text.find("Rust") {
        let before = &text[..pos];
        let word = &text[pos..pos+4];
        let after = &text[pos+4..];
        
        println!("Before: '{}'", before);
        println!("Word: '{}'", word);
        println!("After: '{}'", after);
    }
    
    // Find last occurrence
    if let Some(pos) = text.rfind('l') {
        println!("Last 'l' at position {}", pos);
    }
}
```

**Pattern matching with slices:**
```rust
fn pattern_matching() {
    let texts = ["", "Hello", "Hello, World!"];
    
    for text in &texts {
        match *text {
            "" => println!("Empty string"),
            "Hello" => println!("Just a greeting"),
            s if s.starts_with("Hello") => println!("Greeting with more: {}", s),
            _ => println!("Something else"),
        }
    }
}
```

### Practical Examples

**Example 1: Extract domain from email**
```rust
fn extract_domain(email: &str) -> Option<&str> {
    email.split('@').nth(1)
}

fn main() {
    let email1 = "user@example.com";
    let email2 = "invalid";
    
    println!("Domain: {:?}", extract_domain(email1));
    println!("Domain: {:?}", extract_domain(email2));
}
```

**Example 2: Word count**
```rust
fn word_count(text: &str) -> usize {
    text.split_whitespace().count()
}

fn main() {
    let sentence = "The quick brown fox jumps over the lazy dog";
    println!("Word count: {}", word_count(sentence));
}
```

**Example 3: Parse CSV line**
```rust
fn parse_csv_line(line: &str) -> Vec<&str> {
    line.split(',')
        .map(|field| field.trim())
        .collect()
}

fn main() {
    let csv = "John, Doe, 30, Engineer";
    let fields = parse_csv_line(csv);
    println!("Fields: {:?}", fields);
}
```

**Example 4: Title case conversion**
```rust
fn to_title_case(text: &str) -> String {
    text.split_whitespace()
        .map(|word| {
            let mut chars = word.chars();
            match chars.next() {
                Some(first) => {
                    let rest: String = chars.collect();
                    format!("{}{}", first.to_uppercase(), rest.to_lowercase())
                }
                None => String::new(),
            }
        })
        .collect::<Vec<String>>()
        .join(" ")
}

fn main() {
    let text = "hello rust world";
    println!("Title case: {}", to_title_case(text));
}
```

**Example 5: Remove comments**
```rust
fn remove_comments(code: &str) -> String {
    code.lines()
        .map(|line| {
            if let Some(pos) = line.find("//") {
                &line[..pos]
            } else {
                line
            }
        })
        .map(|line| line.trim_end())
        .collect::<Vec<&str>>()
        .join("\n")
}

fn main() {
    let code = "let x = 5; // This is a comment\nlet y = 10; // Another comment";
    println!("Code without comments:\n{}", remove_comments(code));
}
```

### Performance Considerations

```rust
fn performance_tips() {
    let text = "Hello, Rust!";
    
    // Good: Slicing is cheap (just pointer + length)
    let slice1 = &text[..5];
    let slice2 = &text[7..];
    println!("{} {}", slice1, slice2);
    
    // Good: No allocation for slicing
    fn get_first_word(s: &str) -> &str {
        s.split_whitespace().next().unwrap_or("")
    }
    
    // Be aware: Methods like replace/to_uppercase allocate new Strings
    let upper = text.to_uppercase();  // Allocates
    let replaced = text.replace("Rust", "World");  // Allocates
    
    println!("{}, {}", upper, replaced);
}
```
## 5. Introduction to Array Slices

### Understanding Arrays in Rust

Arrays are fixed-size collections of elements of the same type, allocated on the stack:

```rust
fn array_basics() {
    // Create an array with explicit values
    let numbers = [1, 2, 3, 4, 5];
    
    // Explicit type annotation: [type; length]
    let explicit: [i32; 5] = [1, 2, 3, 4, 5];
    
    // Initialize with same value
    let zeros = [0; 10];  // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
    
    println!("numbers: {:?}", numbers);
    println!("explicit: {:?}", explicit);
    println!("zeros: {:?}", zeros);
}
```

**Array Characteristics:**
- Fixed size known at compile time
- Elements are the same type
- Allocated on the stack
- Size is part of the type: `[i32; 5]` ≠ `[i32; 10]`

```rust
fn array_characteristics() {
    let arr1: [i32; 3] = [1, 2, 3];
    let arr2: [i32; 5] = [1, 2, 3, 4, 5];
    
    // arr1 and arr2 are different types!
    // let x = arr1;
    // let x = arr2;  // ERROR: different types
    
    println!("arr1 length: {}", arr1.len());
    println!("arr2 length: {}", arr2.len());
}
```

### Array Memory Layout

Arrays are stored contiguously in memory:

```rust
fn array_memory() {
    let arr = [10, 20, 30, 40, 50];
    
    // Stack memory layout:
    // [10][20][30][40][50]
    //  ↑   ↑   ↑   ↑   ↑
    //  0   1   2   3   4  (indices)
    
    println!("Array: {:?}", arr);
    println!("Size in memory: {} bytes", std::mem::size_of_val(&arr));
    
    // Each i32 is 4 bytes, so 5 elements = 20 bytes
}
```

### What is an Array Slice?

An array slice (`&[T]`) is a view into an array (or part of it):

```rust
fn array_slice_basics() {
    let arr = [1, 2, 3, 4, 5];
    
    // Create a slice of the entire array
    let slice: &[i32] = &arr;
    
    // Slices are references, not owned data
    println!("Array: {:?}", arr);
    println!("Slice: {:?}", slice);
    
    // Both arr and slice are valid
    println!("Array still accessible: {:?}", arr);
}
```

### Array Slice Structure

An array slice is a fat pointer containing:
1. **Pointer**: Address of the first element
2. **Length**: Number of elements in the slice

```rust
fn slice_structure() {
    let arr = [10, 20, 30, 40, 50];
    let slice = &arr[1..4];  // [20, 30, 40]
    
    // slice contains:
    // - ptr: pointer to arr[1] (the value 20)
    // - len: 3 (three elements)
    
    println!("Slice: {:?}", slice);
    println!("Slice length: {}", slice.len());
    
    // Get raw pointer (for demonstration)
    let ptr = slice.as_ptr();
    println!("Slice pointer: {:p}", ptr);
}
```

### Creating Array Slices

**Slice the entire array:**
```rust
fn slice_entire_array() {
    let arr = [1, 2, 3, 4, 5];
    
    // Multiple ways to slice entire array
    let slice1: &[i32] = &arr;
    let slice2 = &arr[..];
    let slice3 = &arr[0..arr.len()];
    
    println!("slice1: {:?}", slice1);
    println!("slice2: {:?}", slice2);
    println!("slice3: {:?}", slice3);
    
    // All produce the same result
    assert_eq!(slice1, slice2);
    assert_eq!(slice2, slice3);
}
```

**Slice part of an array:**
```rust
fn slice_partial_array() {
    let arr = [0, 10, 20, 30, 40, 50];
    
    // Slice using range syntax
    let slice1 = &arr[1..4];     // [10, 20, 30]
    let slice2 = &arr[..3];      // [0, 10, 20]
    let slice3 = &arr[3..];      // [30, 40, 50]
    let slice4 = &arr[1..=3];    // [10, 20, 30] (inclusive end)
    
    println!("slice1: {:?}", slice1);
    println!("slice2: {:?}", slice2);
    println!("slice3: {:?}", slice3);
    println!("slice4: {:?}", slice4);
}
```

### Type Annotations for Slices

```rust
fn slice_type_annotations() {
    let arr = [1, 2, 3, 4, 5];
    
    // Explicit type: &[type]
    let slice1: &[i32] = &arr[..];
    
    // Inferred type (preferred)
    let slice2 = &arr[..];
    
    // The type doesn't include length!
    // &[i32] can refer to any length of i32 slice
    
    println!("slice1: {:?}", slice1);
    println!("slice2: {:?}", slice2);
}
```

### Accessing Slice Elements

```rust
fn accessing_slice_elements() {
    let arr = [10, 20, 30, 40, 50];
    let slice = &arr[1..4];  // [20, 30, 40]
    
    // Index access
    println!("First element: {}", slice[0]);   // 20
    println!("Second element: {}", slice[1]);  // 30
    
    // Note: indices are relative to the slice
    // slice[0] is arr[1]
    
    // Safe access with get()
    match slice.get(0) {
        Some(&value) => println!("Value at 0: {}", value),
        None => println!("Index out of bounds"),
    }
    
    // Out of bounds check
    match slice.get(10) {
        Some(&value) => println!("Value: {}", value),
        None => println!("Index 10 is out of bounds"),
    }
}
```

### Slice Length and Bounds

```rust
fn slice_properties() {
    let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
    let slice = &arr[2..7];  // [3, 4, 5, 6, 7]
    
    // Get length
    println!("Slice length: {}", slice.len());
    
    // Check if empty
    println!("Is empty: {}", slice.is_empty());
    
    // First and last elements
    println!("First: {:?}", slice.first());
    println!("Last: {:?}", slice.last());
    
    // Get subslice
    let subslice = &slice[1..3];  // [4, 5]
    println!("Subslice: {:?}", subslice);
}
```

### Slices vs Arrays

```rust
fn slices_vs_arrays() {
    // Array: fixed size, owned, on stack
    let arr: [i32; 5] = [1, 2, 3, 4, 5];
    
    // Slice: view into data, borrowed, size not in type
    let slice: &[i32] = &arr;
    
    // Functions can accept slices of any size
    fn print_slice(s: &[i32]) {
        println!("Slice: {:?}", s);
    }
    
    let arr1 = [1, 2, 3];
    let arr2 = [1, 2, 3, 4, 5];
    
    // Both work with the same function
    print_slice(&arr1);
    print_slice(&arr2);
    
    // But arrays of different sizes are different types
    // fn print_array(a: [i32; 3]) {}  // Only accepts size 3
}
```

### Why Use Array Slices?

**Flexibility in function parameters:**
```rust
// Less flexible: specific array size
fn sum_array_fixed(arr: &[i32; 5]) -> i32 {
    arr.iter().sum()
}

// More flexible: any size
fn sum_slice(slice: &[i32]) -> i32 {
    slice.iter().sum()
}

fn flexibility_demo() {
    let arr1 = [1, 2, 3, 4, 5];
    let arr2 = [10, 20, 30];
    
    // sum_array_fixed only works with size 5
    println!("Sum 1: {}", sum_array_fixed(&arr1));
    // println!("Sum 2: {}", sum_array_fixed(&arr2));  // ERROR: wrong size
    
    // sum_slice works with any size
    println!("Sum 1: {}", sum_slice(&arr1));
    println!("Sum 2: {}", sum_slice(&arr2));
    println!("Sum partial: {}", sum_slice(&arr1[..3]));
}
```

### Slices Don't Own Data

```rust
fn slices_dont_own() {
    let arr = [1, 2, 3, 4, 5];
    let slice = &arr[..];
    
    // slice is just a view, arr owns the data
    println!("Owner: {:?}", arr);
    println!("View: {:?}", slice);
    
    // Can create multiple slices
    let slice1 = &arr[..2];
    let slice2 = &arr[2..];
    println!("slice1: {:?}, slice2: {:?}", slice1, slice2);
    
    // All coexist because they're just views
    println!("Original: {:?}", arr);
}
```

### Array Slices with Different Types

```rust
fn slices_different_types() {
    // Integer slice
    let ints = [1, 2, 3, 4, 5];
    let int_slice: &[i32] = &ints;
    println!("Integers: {:?}", int_slice);
    
    // Float slice
    let floats = [1.0, 2.0, 3.0];
    let float_slice: &[f64] = &floats;
    println!("Floats: {:?}", float_slice);
    
    // Boolean slice
    let bools = [true, false, true, false];
    let bool_slice: &[bool] = &bools;
    println!("Booleans: {:?}", bool_slice);
    
    // String slice (slice of string slices!)
    let strings = ["hello", "world", "rust"];
    let string_slice: &[&str] = &strings;
    println!("Strings: {:?}", string_slice);
    
    // Tuple slice
    let tuples = [(1, 2), (3, 4), (5, 6)];
    let tuple_slice: &[(i32, i32)] = &tuples;
    println!("Tuples: {:?}", tuple_slice);
}
```

### Practical Examples

**Example 1: Find maximum in slice**
```rust
fn find_max(slice: &[i32]) -> Option<i32> {
    if slice.is_empty() {
        return None;
    }
    
    let mut max = slice[0];
    for &value in slice {
        if value > max {
            max = value;
        }
    }
    
    Some(max)
}

fn main() {
    let numbers = [3, 7, 2, 9, 1, 5];
    match find_max(&numbers) {
        Some(max) => println!("Maximum: {}", max),
        None => println!("Empty slice"),
    }
}
```

**Example 2: Array partitioning**
```rust
fn partition_array(arr: &[i32]) -> (&[i32], &[i32]) {
    let mid = arr.len() / 2;
    let (left, right) = arr.split_at(mid);
    (left, right)
}

fn main() {
    let data = [1, 2, 3, 4, 5, 6];
    let (left, right) = partition_array(&data);
    println!("Left: {:?}", left);
    println!("Right: {:?}", right);
}
```

**Example 3: Processing windows**
```rust
fn process_windows(arr: &[i32], window_size: usize) {
    for (i, window) in arr.windows(window_size).enumerate() {
        println!("Window {}: {:?}", i, window);
    }
}

fn main() {
    let numbers = [1, 2, 3, 4, 5, 6];
    process_windows(&numbers, 3);
}
```

### Vector Slices

Vectors can also be sliced (they're dynamically sized arrays):

```rust
fn vector_slices() {
    let vec = vec![10, 20, 30, 40, 50];
    
    // Slice a vector the same way
    let slice: &[i32] = &vec[1..4];
    println!("Vector slice: {:?}", slice);
    
    // Functions accepting slices work with both arrays and vectors
    fn process(data: &[i32]) {
        println!("Processing: {:?}", data);
    }
    
    let arr = [1, 2, 3];
    process(&arr);      // Array
    process(&vec);      // Vector
    process(slice);     // Slice
}
```

### Memory Safety with Slices

```rust
fn memory_safety() {
    let arr = [1, 2, 3, 4, 5];
    
    // Slices prevent out-of-bounds access at compile time when possible
    let slice = &arr[..];
    
    // Runtime bounds checking
    // println!("{}", slice[10]);  // PANIC at runtime
    
    // Safe access
    if let Some(&value) = slice.get(10) {
        println!("Value: {}", value);
    } else {
        println!("Index out of bounds (safely handled)");
    }
    
    // Slice size is checked at runtime
    let safe_slice = &arr[1..4];  // OK
    // let bad_slice = &arr[1..10];  // PANIC: out of bounds
}
```
## 6. Array Slice Techniques

### Iterating Over Slices

**Basic iteration:**
```rust
fn basic_iteration() {
    let arr = [10, 20, 30, 40, 50];
    let slice = &arr[..];
    
    // Iterate over elements (by reference)
    for element in slice {
        println!("Element: {}", element);
    }
    
    // Iterate with index
    for (i, element) in slice.iter().enumerate() {
        println!("Index {}: {}", i, element);
    }
}
```

**Different iteration methods:**
```rust
fn iteration_methods() {
    let arr = [1, 2, 3, 4, 5];
    let slice = &arr[..];
    
    // iter() - iterate by reference
    for item in slice.iter() {
        println!("By ref: {}", item);
    }
    
    // into_iter() - for slices, same as iter()
    for item in slice.into_iter() {
        println!("Into iter: {}", item);
    }
    
    // Direct iteration (also by reference for slices)
    for item in slice {
        println!("Direct: {}", item);
    }
    
    // Iterate in reverse
    for item in slice.iter().rev() {
        println!("Reverse: {}", item);
    }
}
```

### Creating Subslices

**Range-based slicing:**
```rust
fn range_slicing() {
    let arr = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90];
    
    // Standard ranges
    let slice1 = &arr[2..5];      // [20, 30, 40]
    let slice2 = &arr[..4];       // [0, 10, 20, 30]
    let slice3 = &arr[6..];       // [60, 70, 80, 90]
    let slice4 = &arr[..];        // Entire array
    
    // Inclusive range
    let slice5 = &arr[2..=5];     // [20, 30, 40, 50]
    
    println!("slice1: {:?}", slice1);
    println!("slice2: {:?}", slice2);
    println!("slice3: {:?}", slice3);
    println!("slice4: {:?}", slice4);
    println!("slice5: {:?}", slice5);
}
```

**Dynamic slicing:**
```rust
fn dynamic_slicing() {
    let arr = [1, 2, 3, 4, 5, 6, 7, 8];
    
    // Calculate slice bounds at runtime
    let start = 2;
    let end = 6;
    let slice = &arr[start..end];
    println!("Dynamic slice: {:?}", slice);
    
    // First half
    let mid = arr.len() / 2;
    let first_half = &arr[..mid];
    let second_half = &arr[mid..];
    
    println!("First half: {:?}", first_half);
    println!("Second half: {:?}", second_half);
}
```

### Mutable Slices

Creating and using mutable slices:

```rust
fn mutable_slices() {
    let mut arr = [1, 2, 3, 4, 5];
    
    // Create a mutable slice
    let slice: &mut [i32] = &mut arr;
    
    // Modify elements through the slice
    slice[0] = 10;
    slice[1] = 20;
    
    println!("Modified array: {:?}", arr);  // [10, 20, 3, 4, 5]
}
```

**Modifying slice elements:**
```rust
fn modify_slice_elements() {
    let mut arr = [1, 2, 3, 4, 5];
    let slice = &mut arr[..];
    
    // Modify each element
    for element in slice.iter_mut() {
        *element *= 2;
    }
    
    println!("Doubled: {:?}", arr);  // [2, 4, 6, 8, 10]
}
```

**Mutable subslices:**
```rust
fn mutable_subslices() {
    let mut arr = [0, 10, 20, 30, 40, 50];
    
    // Modify only part of the array
    {
        let slice = &mut arr[1..4];
        for element in slice {
            *element += 5;
        }
    }
    
    println!("Modified subarray: {:?}", arr);  // [0, 15, 25, 35, 40, 50]
}
```

### Borrow Checker Rules with Slices

**Multiple immutable slices:**
```rust
fn multiple_immutable_slices() {
    let arr = [1, 2, 3, 4, 5];
    
    // Can have multiple immutable slices
    let slice1 = &arr[..2];
    let slice2 = &arr[2..];
    let slice3 = &arr[..];
    
    println!("slice1: {:?}", slice1);
    println!("slice2: {:?}", slice2);
    println!("slice3: {:?}", slice3);
}
```

**Only one mutable slice:**
```rust
fn single_mutable_slice() {
    let mut arr = [1, 2, 3, 4, 5];
    
    // Only one mutable slice at a time
    {
        let slice = &mut arr[..];
        slice[0] = 10;
        println!("Mutable slice: {:?}", slice);
    }  // slice dropped here
    
    // Now we can create another reference
    println!("Array: {:?}", arr);
}
```

**Non-overlapping mutable slices:**
```rust
fn split_mutable_slices() {
    let mut arr = [1, 2, 3, 4, 5, 6];
    
    // Can have multiple mutable slices if they don't overlap
    let (left, right) = arr.split_at_mut(3);
    
    // Both are mutable and don't overlap
    left[0] = 10;
    right[0] = 40;
    
    println!("Left: {:?}", left);    // [10, 2, 3]
    println!("Right: {:?}", right);  // [40, 5, 6]
    println!("Array: {:?}", arr);    // [10, 2, 3, 40, 5, 6]
}
```

### Slice Methods

**Searching:**
```rust
fn slice_searching() {
    let arr = [10, 20, 30, 20, 40];
    let slice = &arr[..];
    
    // Check if contains
    println!("Contains 30: {}", slice.contains(&30));
    println!("Contains 100: {}", slice.contains(&100));
    
    // Find position
    match slice.iter().position(|&x| x == 20) {
        Some(pos) => println!("First 20 at index: {}", pos),
        None => println!("Not found"),
    }
    
    // Find last position
    match slice.iter().rposition(|&x| x == 20) {
        Some(pos) => println!("Last 20 at index: {}", pos),
        None => println!("Not found"),
    }
}
```

**Splitting slices:**
```rust
fn splitting_slices() {
    let arr = [1, 2, 3, 4, 5, 6, 7, 8];
    let slice = &arr[..];
    
    // Split at index
    let (left, right) = slice.split_at(4);
    println!("Split at 4: {:?} | {:?}", left, right);
    
    // Split first
    if let Some((first, rest)) = slice.split_first() {
        println!("First: {}, Rest: {:?}", first, rest);
    }
    
    // Split last
    if let Some((last, rest)) = slice.split_last() {
        println!("Last: {}, Rest: {:?}", last, rest);
    }
    
    // Split by predicate
    let parts: Vec<&[i32]> = slice.split(|&x| x % 3 == 0).collect();
    println!("Split by divisible by 3: {:?}", parts);
}
```

**Chunking:**
```rust
fn chunking_slices() {
    let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
    let slice = &arr[..];
    
    // Fixed-size chunks
    println!("Chunks of 3:");
    for chunk in slice.chunks(3) {
        println!("  {:?}", chunk);
    }
    
    // Exact chunks (panics if not evenly divisible)
    println!("Exact chunks of 3:");
    for chunk in slice.chunks_exact(3) {
        println!("  {:?}", chunk);
    }
    
    // Remainder from chunks_exact
    let mut iter = slice.chunks_exact(3);
    while let Some(chunk) = iter.next() {
        println!("  {:?}", chunk);
    }
    println!("  Remainder: {:?}", iter.remainder());
}
```

**Windows:**
```rust
fn windows_slices() {
    let arr = [1, 2, 3, 4, 5];
    let slice = &arr[..];
    
    // Sliding windows
    println!("Windows of size 3:");
    for window in slice.windows(3) {
        println!("  {:?}", window);
    }
    // Output:
    // [1, 2, 3]
    // [2, 3, 4]
    // [3, 4, 5]
}
```

**Sorting and searching:**
```rust
fn sorting_searching() {
    let mut arr = [5, 2, 8, 1, 9, 3];
    let slice = &mut arr[..];
    
    // Sort the slice
    slice.sort();
    println!("Sorted: {:?}", arr);
    
    // Binary search (slice must be sorted)
    match slice.binary_search(&5) {
        Ok(pos) => println!("Found 5 at index: {}", pos),
        Err(pos) => println!("5 would be inserted at: {}", pos),
    }
    
    // Sort in reverse
    let mut arr2 = [5, 2, 8, 1, 9, 3];
    arr2.sort_by(|a, b| b.cmp(a));
    println!("Sorted descending: {:?}", arr2);
}
```

**Reversing:**
```rust
fn reversing_slices() {
    let mut arr = [1, 2, 3, 4, 5];
    
    // Reverse in place
    arr.reverse();
    println!("Reversed: {:?}", arr);  // [5, 4, 3, 2, 1]
    
    // Iterate in reverse (without modifying)
    let arr2 = [1, 2, 3, 4, 5];
    for item in arr2.iter().rev() {
        print!("{} ", item);
    }
    println!();
}
```

### Copying and Cloning Slices

**Copy to another slice:**
```rust
fn copy_slices() {
    let src = [1, 2, 3, 4, 5];
    let mut dest = [0; 5];
    
    // Copy entire slice
    dest.copy_from_slice(&src);
    println!("Copied: {:?}", dest);
    
    // Copy partial slice
    let mut partial = [0; 3];
    partial.copy_from_slice(&src[1..4]);
    println!("Partial copy: {:?}", partial);
}
```

**Clone slice to vector:**
```rust
fn clone_to_vec() {
    let arr = [1, 2, 3, 4, 5];
    let slice = &arr[1..4];
    
    // Clone slice into a Vec
    let vec: Vec<i32> = slice.to_vec();
    println!("Vector from slice: {:?}", vec);
    
    // Alternative
    let vec2: Vec<i32> = slice.iter().cloned().collect();
    println!("Vector from iter: {:?}", vec2);
}
```

### Comparing Slices

```rust
fn comparing_slices() {
    let arr1 = [1, 2, 3, 4, 5];
    let arr2 = [1, 2, 3, 4, 5];
    let arr3 = [1, 2, 3, 4, 6];
    
    let slice1 = &arr1[..];
    let slice2 = &arr2[..];
    let slice3 = &arr3[..];
    
    // Equality
    println!("slice1 == slice2: {}", slice1 == slice2);
    println!("slice1 == slice3: {}", slice1 == slice3);
    
    // Ordering
    println!("slice1 < slice3: {}", slice1 < slice3);
    
    // Starts with / ends with
    println!("Starts with [1, 2]: {}", slice1.starts_with(&[1, 2]));
    println!("Ends with [4, 5]: {}", slice1.ends_with(&[4, 5]));
}
```

### Advanced Slice Techniques

**Swap elements:**
```rust
fn swap_elements() {
    let mut arr = [1, 2, 3, 4, 5];
    
    // Swap two elements
    arr.swap(0, 4);
    println!("After swap: {:?}", arr);  // [5, 2, 3, 4, 1]
    
    // Swap ranges (requires std::mem)
    let mut arr2 = [1, 2, 3, 4, 5, 6];
    let (left, right) = arr2.split_at_mut(3);
    left[0..2].swap_with_slice(&mut right[0..2]);
    println!("After range swap: {:?}", arr2);
}
```

**Fill slice:**
```rust
fn fill_slice() {
    let mut arr = [0; 10];
    
    // Fill entire slice
    arr.fill(42);
    println!("Filled: {:?}", arr);
    
    // Fill with function
    let mut arr2 = [0; 5];
    arr2.fill_with(|| rand::random::<i32>() % 100);
    println!("Filled with random: {:?}", arr2);
    
    // Fill part of slice
    let mut arr3 = [0; 10];
    arr3[2..7].fill(99);
    println!("Partially filled: {:?}", arr3);
}
```

**Rotate slice:**
```rust
fn rotate_slice() {
    let mut arr = [1, 2, 3, 4, 5];
    
    // Rotate left
    arr.rotate_left(2);
    println!("Rotated left: {:?}", arr);  // [3, 4, 5, 1, 2]
    
    // Rotate right
    let mut arr2 = [1, 2, 3, 4, 5];
    arr2.rotate_right(2);
    println!("Rotated right: {:?}", arr2);  // [4, 5, 1, 2, 3]
}
```

### Practical Examples

**Example 1: Running average**
```rust
fn running_average(data: &[f64], window_size: usize) -> Vec<f64> {
    data.windows(window_size)
        .map(|window| window.iter().sum::<f64>() / window_size as f64)
        .collect()
}

fn main() {
    let data = [1.0, 2.0, 3.0, 4.0, 5.0, 6.0];
    let averages = running_average(&data, 3);
    println!("Running averages: {:?}", averages);
}
```

**Example 2: Find pairs that sum to target**
```rust
fn find_pair_sum(arr: &[i32], target: i32) -> Option<(i32, i32)> {
    for (i, &a) in arr.iter().enumerate() {
        for &b in &arr[i+1..] {
            if a + b == target {
                return Some((a, b));
            }
        }
    }
    None
}

fn main() {
    let numbers = [1, 3, 5, 7, 9, 11];
    match find_pair_sum(&numbers, 12) {
        Some((a, b)) => println!("Found pair: {} + {} = 12", a, b),
        None => println!("No pair found"),
    }
}
```

**Example 3: Partition by predicate**
```rust
fn partition_slice(arr: &[i32]) -> (Vec<i32>, Vec<i32>) {
    let (evens, odds): (Vec<i32>, Vec<i32>) = arr.iter()
        .partition(|&&x| x % 2 == 0);
    (evens, odds)
}

fn main() {
    let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
    let (evens, odds) = partition_slice(&numbers);
    println!("Evens: {:?}", evens);
    println!("Odds: {:?}", odds);
}
```

**Example 4: Matrix operations (2D slices)**
```rust
fn print_matrix(matrix: &[&[i32]]) {
    for row in matrix {
        for &val in *row {
            print!("{:3} ", val);
        }
        println!();
    }
}

fn main() {
    let row1 = [1, 2, 3];
    let row2 = [4, 5, 6];
    let row3 = [7, 8, 9];
    
    let matrix: &[&[i32]] = &[&row1, &row2, &row3];
    
    println!("Matrix:");
    print_matrix(matrix);
}
```

**Example 5: Deduplication**
```rust
fn deduplicate(slice: &mut [i32]) -> usize {
    if slice.is_empty() {
        return 0;
    }
    
    slice.sort();
    let mut write_pos = 1;
    
    for read_pos in 1..slice.len() {
        if slice[read_pos] != slice[write_pos - 1] {
            slice[write_pos] = slice[read_pos];
            write_pos += 1;
        }
    }
    
    write_pos
}

fn main() {
    let mut numbers = [1, 3, 2, 1, 4, 3, 5, 2, 4];
    let unique_count = deduplicate(&mut numbers);
    println!("Unique elements: {:?}", &numbers[..unique_count]);
}
```

### Performance Considerations

```rust
fn performance_tips() {
    let data = [1, 2, 3, 4, 5, 6, 7, 8];
    
    // Good: Slicing is cheap (just pointer + length)
    let slice1 = &data[..4];
    let slice2 = &data[4..];
    
    // Good: Iteration over slices is efficient
    let sum: i32 = data.iter().sum();
    
    // Good: In-place operations avoid allocations
    let mut mutable = [1, 2, 3, 4, 5];
    mutable[2..4].reverse();
    
    // Be aware: to_vec() allocates
    let vec = data.to_vec();  // Allocates heap memory
    
    // Be aware: clone() creates deep copy
    let cloned = data.clone();  // For arrays: stack copy
    
    println!("Sum: {}", sum);
    println!("Vec: {:?}", vec);
    println!("Cloned: {:?}", cloned);
}
```

### Best Practices

1. **Prefer slices in function parameters** for flexibility
2. **Use mutable slices** when you need to modify in place
3. **Split slices** to work with non-overlapping mutable portions
4. **Check bounds** with `.get()` for safe access
5. **Use iterators** for transformations instead of index loops
6. **Leverage built-in methods** like `sort`, `reverse`, `rotate` for efficiency

```rust
// Good: Flexible parameter
fn process_data(data: &[i32]) -> i32 {
    data.iter().sum()
}

// Good: Mutable slice for in-place modification
fn double_values(data: &mut [i32]) {
    for val in data {
        *val *= 2;
    }
}

// Good: Safe access
fn safe_get(data: &[i32], index: usize) -> Option<i32> {
    data.get(index).copied()
}

fn main() {
    let arr = [1, 2, 3, 4, 5];
    println!("Sum: {}", process_data(&arr));
    
    let mut arr2 = [1, 2, 3];
    double_values(&mut arr2);
    println!("Doubled: {:?}", arr2);
    
    println!("Safe access: {:?}", safe_get(&arr, 2));
    println!("Safe access OOB: {:?}", safe_get(&arr, 10));
}
```
