Understanding Ownership in Rust

## Understanding local scope

Scope of Variables
- Variables defined inside a function have local scope and disappear at the end of the function.
- Block scope is introduced when variables are defined within blocks (e.g., if statements), and they are only accessible within that block.

In Rust, variable scope determines where a variable can be accessed and how long it lives. Variables follow lexical scoping rules, meaning they are valid from the point of declaration until the end of their enclosing scope.

**Function Scope:**
Variables defined inside a function have local scope and are destroyed when the function exits:

```rust
fn demonstrate_function_scope() {
    let x = 42;  // x is created here
    println!("x inside function: {}", x);
    
    // x is automatically dropped when function exits
}

fn main() {
    demonstrate_function_scope();
    // println!("{}", x);  // ERROR: x is not accessible here
}
```

**Block Scope:**
Variables defined within blocks (like if statements, loops, or arbitrary `{}` blocks) are only accessible within that block:

```rust
fn demonstrate_block_scope() {
    let outer = "I'm outside";
    
    {
        let inner = "I'm inside a block";
        println!("{}", outer);  // Can access outer variable
        println!("{}", inner);  // Can access inner variable
    }
    
    println!("{}", outer);  // Still accessible
    // println!("{}", inner);  // ERROR: inner is out of scope
    
    if true {
        let conditional_var = 100;
        println!("Inside if: {}", conditional_var);
    }
    // println!("{}", conditional_var);  // ERROR: out of scope
}
```

**Shadowing:**
Rust allows variable shadowing, where you can declare a new variable with the same name:

```rust
fn demonstrate_shadowing() {
    let x = 5;
    println!("First x: {}", x);
    
    let x = x + 1;  // Shadows the previous x
    println!("Second x: {}", x);
    
    {
        let x = x * 2;  // Shadows again in this block
        println!("Inner x: {}", x);  // Prints 12
    }
    
    println!("Outer x: {}", x);  // Prints 6 (inner shadow is gone)
}
```

### Memory Allocation
- By default, variables are allocated on the stack, created when entering a function and removed when exiting.
- Static storage can be used for global variables or variables declared within functions, allowing them to retain values across function calls.

Rust uses two primary memory regions for variable storage:

**Stack Allocation (Default):**
- Variables are allocated on the stack by default
- Stack allocation is fast and deterministic
- Memory is automatically reclaimed when variables go out of scope
- Stack-allocated data has a known, fixed size at compile time

```rust
fn stack_allocation_example() {
    let a = 10;           // i32 on stack
    let b = 3.14;         // f64 on stack
    let c = true;         // bool on stack
    let d = [1, 2, 3, 4]; // Array on stack (fixed size)
    
    // All variables are cleaned up when function exits
}
```

**Heap Allocation:**
- Used for dynamically-sized data
- Requires explicit allocation (via types like `String`, `Vec`, `Box`)
- Slightly slower than stack due to allocation overhead
- Ownership system ensures automatic cleanup

```rust
fn heap_allocation_example() {
    let s1 = String::from("hello");  // String data on heap
    let v1 = vec![1, 2, 3, 4, 5];   // Vector data on heap
    let b1 = Box::new(100);          // Boxed value on heap
    
    // Stack contains pointers to heap data
    // Heap memory is automatically freed when variables are dropped
}
```

**Memory Layout Visualization:**
```rust
fn memory_layout_demo() {
    let x = 42;                      // Stack: [x: 42]
    let s = String::from("Rust");    // Stack: [s: ptr, len, cap]
                                     // Heap: ['R', 'u', 's', 't']
    
    // s consists of:
    // - pointer to heap data
    // - length (4 bytes)
    // - capacity (4 bytes minimum)
}
```

### Ownership and Borrowing
- Rust emphasizes ownership, meaning when a variable is assigned to another, the ownership of the object must be considered.
- Understanding ownership and borrowing is crucial for writing robust and safe Rust programs, as it prevents common programming errors.

Rust's ownership system is its most distinctive feature, ensuring memory safety without garbage collection:

**Three Rules of Ownership:**
1. Each value in Rust has a single owner
2. There can only be one owner at a time
3. When the owner goes out of scope, the value is dropped

```rust
fn ownership_basics() {
    let s1 = String::from("hello");  // s1 owns the String
    
    // s1's ownership is moved to s2
    let s2 = s1;
    
    // println!("{}", s1);  // ERROR: s1 no longer owns the value
    println!("{}", s2);     // OK: s2 is the current owner
}
```

**Borrowing:**
Instead of transferring ownership, you can borrow a reference:

```rust
fn borrowing_example() {
    let s1 = String::from("hello");
    
    // Immutable borrow - s1 still owns the String
    let len = calculate_length(&s1);
    
    println!("String '{}' has length {}", s1, len);  // s1 is still valid
}

fn calculate_length(s: &String) -> usize {
    s.len()  // Can read but not modify
}
```

**Mutable Borrowing:**
```rust
fn mutable_borrowing() {
    let mut s = String::from("hello");
    
    // Mutable borrow allows modification
    append_world(&mut s);
    
    println!("{}", s);  // Prints: "hello, world!"
}

fn append_world(s: &mut String) {
    s.push_str(", world!");
}
```

**Borrowing Rules:**
1. You can have either one mutable reference OR any number of immutable references
2. References must always be valid (no dangling references)

```rust
fn borrowing_rules_example() {
    let mut data = vec![1, 2, 3];
    
    // Multiple immutable borrows are OK
    let r1 = &data;
    let r2 = &data;
    println!("{:?} {:?}", r1, r2);
    
    // Mutable borrow (r1 and r2 are no longer used)
    let r3 = &mut data;
    r3.push(4);
    
    // println!("{:?}", r1);  // ERROR: can't use r1 after mutable borrow
    println!("{:?}", r3);
}
```


## Defining Static Local Scope
This section focuses on the concept of static storage in Rust programming, contrasting it with stack-based storage.

Static Storage
- Static variables remain allocated permanently until the program terminates, allowing them to retain values across function calls.
- They can be defined locally within a function or globally, with local statics being visible only within the function.

Mutability and Lifetime
- Static variables can be mutable, but care must be taken regarding thread safety when modifying them.
- Unlike constants, which are not allocated in memory and replaced directly in code, static variables occupy memory and have a defined address.

Lazy Initialization
- To initialize a static variable with a runtime value safely, Rust uses a "lazy" type, which computes the value in a thread-safe manner upon first access.
- The lazy wrapper allows for the initialization of variables like timestamps, ensuring that the value is computed only when needed and in a thread-safe way.


Understanding Static Variables
- Static variables can be initialized at compile time or runtime, but direct runtime initialization is not thread-safe.
- A static variable initialized at compile time holds a constant value, while runtime initialization requires a lazy approach.

Using Lazy Initialization
- The `once_cell` crate is introduced for lazy initialization, allowing a variable to be initialized only when accessed for the first time.
- A closure (anonymous function) is used to define the initialization logic, which is executed upon the first access of the variable.

Demonstrating the Concept
- The example illustrates how a static variable can be initialized with the current time, showing the difference between the initial time and the time after a delay.
- The lazy initialization ensures that the closure runs only once, making it efficient and thread-safe, as the value is retained for subsequent accesses without re-evaluating the closure.


### Understanding Static Storage

Static variables have a `'static` lifetime, meaning they live for the entire duration of the program. Unlike regular local variables that are created and destroyed with each function call, static variables maintain their state across multiple invocations.

**Key Characteristics:**
- Allocated at program startup (not on the stack)
- Exist until program termination
- Only one instance exists regardless of how many times the function is called
- Must be initialized with a constant expression or lazy initialization

### Static vs Constant

```rust
const MAX_POINTS: u32 = 100_000;  // Constant: inlined at compile time

fn demonstrate_const_vs_static() {
    static CALL_COUNT: std::sync::atomic::AtomicU32 = 
        std::sync::atomic::AtomicU32::new(0);
    
    // Constants are replaced inline (no memory address)
    println!("Max points: {}", MAX_POINTS);
    
    // Static has a fixed memory location
    let count = CALL_COUNT.fetch_add(1, std::sync::atomic::Ordering::SeqCst);
    println!("Function called {} times", count + 1);
}

fn main() {
    demonstrate_const_vs_static();  // Prints: Function called 1 times
    demonstrate_const_vs_static();  // Prints: Function called 2 times
    demonstrate_const_vs_static();  // Prints: Function called 3 times
}
```

### Static Local Variables with Compile-Time Initialization

Static local variables can be initialized with constant expressions:

```rust
fn get_greeting() -> &'static str {
    static GREETING: &str = "Hello, Rust!";  // Initialized at compile time
    GREETING
}

fn counter_demo() {
    static CALLS: std::sync::atomic::AtomicUsize = 
        std::sync::atomic::AtomicUsize::new(0);
    
    let current = CALLS.fetch_add(1, std::sync::atomic::Ordering::Relaxed);
    println!("This function has been called {} times", current + 1);
}

fn main() {
    println!("{}", get_greeting());
    
    for _ in 0..5 {
        counter_demo();
    }
}
```

### Lazy Initialization with Runtime Values

For values that can't be computed at compile time, use lazy initialization:

```rust
use std::sync::OnceLock;
use std::time::SystemTime;

fn get_program_start_time() -> SystemTime {
    // Static variable with lazy initialization
    static START_TIME: OnceLock<SystemTime> = OnceLock::new();
    
    // Initialize only on first call
    *START_TIME.get_or_init(|| {
        println!("Initializing start time...");
        SystemTime::now()
    })
}

fn main() {
    println!("First call:");
    let time1 = get_program_start_time();
    
    std::thread::sleep(std::time::Duration::from_secs(2));
    
    println!("\nSecond call (no re-initialization):");
    let time2 = get_program_start_time();
    
    // Both times are identical
    println!("Times match: {}", time1 == time2);
}
```

**Using LazyLock (Rust 1.80+):**
```rust
use std::sync::LazyLock;
use std::time::SystemTime;

static INIT_TIME: LazyLock<SystemTime> = LazyLock::new(|| {
    println!("Computing initialization time...");
    SystemTime::now()
});

fn show_elapsed_time() {
    let elapsed = SystemTime::now()
        .duration_since(*INIT_TIME)
        .unwrap();
    println!("Elapsed: {:?}", elapsed);
}

fn main() {
    println!("Program started");
    std::thread::sleep(std::time::Duration::from_millis(100));
    
    show_elapsed_time();
    std::thread::sleep(std::time::Duration::from_millis(200));
    show_elapsed_time();
}
```

### Practical Example: Caching Expensive Computations

```rust
use std::sync::OnceLock;

fn get_expensive_config() -> &'static String {
    static CONFIG: OnceLock<String> = OnceLock::new();
    
    CONFIG.get_or_init(|| {
        println!("Loading configuration (expensive operation)...");
        std::thread::sleep(std::time::Duration::from_secs(1));
        String::from("Config data loaded from disk")
    })
}

fn main() {
    println!("First access:");
    println!("{}", get_expensive_config());  // Takes 1 second
    
    println!("\nSecond access:");
    println!("{}", get_expensive_config());  // Instant (cached)
    
    println!("\nThird access:");
    println!("{}", get_expensive_config());  // Instant (cached)
}
```

### Thread-Safe Counter Example

```rust
use std::sync::atomic::{AtomicUsize, Ordering};
use std::sync::Arc;
use std::thread;

fn increment_counter() -> usize {
    static COUNTER: AtomicUsize = AtomicUsize::new(0);
    COUNTER.fetch_add(1, Ordering::SeqCst) + 1
}

fn main() {
    let handles: Vec<_> = (0..10)
        .map(|_| {
            thread::spawn(|| {
                for _ in 0..100 {
                    increment_counter();
                }
            })
        })
        .collect();
    
    for handle in handles {
        handle.join().unwrap();
    }
    
    println!("Final count: {}", increment_counter() - 1);  // Should be 1000
}
```

## Defining Static Global Scope

**Local Static Variables**
- Local static variables are declared within a function and retain their values across multiple calls.
- They are initialized on the first entry into the function and can be mutable, but care must be taken regarding thread safety.

**Global Static Variables**
- Global static variables can be accessed by all functions within the current file and potentially other files.
- They require the `static` keyword, a specified data type, and a value, which can be a constant or a runtime value.

**Visibility and Lazy Initialization**
- Global variables can be made public to be accessible in other modules by using the `pub` keyword.
- For lazy initialization of global variables, a lazy wrapper is used to ensure thread safety, executing initialization code only on the first access.

### Global Static Variables

Global static variables are accessible from any function within the module and can be made public to other modules. They must be initialized with constant expressions or use lazy initialization for runtime values.

**Basic Global Statics:**
```rust
// Global static with compile-time initialization
static GLOBAL_LIMIT: i32 = 1000;

// Global static string slice
static PROGRAM_NAME: &str = "Rust Application";

// Global static with lifetime annotation
static ERROR_MESSAGE: &'static str = "An error occurred";

fn use_globals() {
    println!("Program: {}", PROGRAM_NAME);
    println!("Limit: {}", GLOBAL_LIMIT);
    println!("Error: {}", ERROR_MESSAGE);
}

fn main() {
    use_globals();
}
```

### Public Global Statics for Module Export

```rust
// In lib.rs or module file
pub static MAX_CONNECTIONS: u32 = 100;
pub static DEFAULT_TIMEOUT: std::time::Duration = 
    std::time::Duration::from_secs(30);

// In another module or main.rs
fn main() {
    println!("Max connections: {}", MAX_CONNECTIONS);
    println!("Timeout: {:?}", DEFAULT_TIMEOUT);
}
```

### Global Lazy Initialization

For runtime-computed values, use `LazyLock` or `OnceLock`:

```rust
use std::sync::LazyLock;
use std::collections::HashMap;

// Global configuration loaded at first access
static CONFIG: LazyLock<HashMap<String, String>> = LazyLock::new(|| {
    println!("Loading global configuration...");
    let mut config = HashMap::new();
    config.insert("version".to_string(), "1.0.0".to_string());
    config.insert("author".to_string(), "Rust Developer".to_string());
    config
});

// Global timestamp
static APP_START: LazyLock<std::time::Instant> = LazyLock::new(|| {
    std::time::Instant::now()
});

fn main() {
    println!("App version: {}", CONFIG.get("version").unwrap());
    println!("App author: {}", CONFIG.get("author").unwrap());
    
    std::thread::sleep(std::time::Duration::from_millis(500));
    
    let uptime = std::time::Instant::now().duration_since(*APP_START);
    println!("Uptime: {:?}", uptime);
}
```

### Global Atomic Variables for Thread-Safe Counters

```rust
use std::sync::atomic::{AtomicU64, AtomicBool, Ordering};
use std::thread;

// Global request counter
static REQUEST_COUNT: AtomicU64 = AtomicU64::new(0);

// Global flag
static IS_RUNNING: AtomicBool = AtomicBool::new(true);

fn handle_request(id: u64) {
    if IS_RUNNING.load(Ordering::Relaxed) {
        let count = REQUEST_COUNT.fetch_add(1, Ordering::SeqCst);
        println!("Thread {} handling request #{}", id, count + 1);
    }
}

fn main() {
    let handles: Vec<_> = (0..5)
        .map(|i| {
            thread::spawn(move || {
                for _ in 0..10 {
                    handle_request(i);
                    thread::sleep(std::time::Duration::from_millis(10));
                }
            })
        })
        .collect();
    
    // Let threads run for a bit
    thread::sleep(std::time::Duration::from_millis(300));
    
    // Signal shutdown
    IS_RUNNING.store(false, Ordering::Relaxed);
    
    for handle in handles {
        handle.join().unwrap();
    }
    
    println!("\nTotal requests processed: {}", 
             REQUEST_COUNT.load(Ordering::SeqCst));
}
```

### Global Mutex for Complex Data

```rust
use std::sync::Mutex;
use std::collections::VecDeque;

// Global message queue protected by Mutex
static MESSAGE_QUEUE: Mutex<VecDeque<String>> = 
    Mutex::new(VecDeque::new());

fn add_message(msg: String) {
    let mut queue = MESSAGE_QUEUE.lock().unwrap();
    queue.push_back(msg);
    println!("Added message. Queue size: {}", queue.len());
}

fn process_messages() {
    let mut queue = MESSAGE_QUEUE.lock().unwrap();
    while let Some(msg) = queue.pop_front() {
        println!("Processing: {}", msg);
    }
}

fn main() {
    add_message("First message".to_string());
    add_message("Second message".to_string());
    add_message("Third message".to_string());
    
    process_messages();
    
    // Queue is now empty
    let queue = MESSAGE_QUEUE.lock().unwrap();
    println!("Queue is empty: {}", queue.is_empty());
}
```

### Real-World Example: Global Logger Configuration

```rust
use std::sync::LazyLock;
use std::sync::Mutex;

#[derive(Debug, Clone, Copy, PartialEq)]
pub enum LogLevel {
    Debug,
    Info,
    Warning,
    Error,
}

pub struct Logger {
    level: LogLevel,
    enabled: bool,
}

impl Logger {
    fn new() -> Self {
        Logger {
            level: LogLevel::Info,
            enabled: true,
        }
    }
    
    fn log(&self, level: LogLevel, message: &str) {
        if self.enabled && level as u8 >= self.level as u8 {
            println!("[{:?}] {}", level, message);
        }
    }
}

// Global logger instance
static LOGGER: LazyLock<Mutex<Logger>> = LazyLock::new(|| {
    Mutex::new(Logger::new())
});

pub fn log_info(msg: &str) {
    LOGGER.lock().unwrap().log(LogLevel::Info, msg);
}

pub fn log_error(msg: &str) {
    LOGGER.lock().unwrap().log(LogLevel::Error, msg);
}

pub fn set_log_level(level: LogLevel) {
    LOGGER.lock().unwrap().level = level;
}

fn main() {
    log_info("Application starting...");
    log_error("This is an error message");
    
    set_log_level(LogLevel::Error);
    log_info("This won't be shown");
    log_error("This will be shown");
}
```

### Visibility and Module Organization

```rust
// In src/config.rs
pub mod config {
    use std::sync::LazyLock;
    
    pub static APP_NAME: &str = "MyApp";
    
    pub static VERSION: LazyLock<String> = LazyLock::new(|| {
        format!("{}.{}.{}", 1, 0, 0)
    });
    
    // Private to this module
    static SECRET_KEY: &str = "secret123";
    
    pub fn get_info() -> String {
        format!("{} v{}", APP_NAME, *VERSION)
    }
}

// In main.rs
fn main() {
    println!("App: {}", config::APP_NAME);
    println!("Version: {}", *config::VERSION);
    println!("Info: {}", config::get_info());
    
    // println!("{}", config::SECRET_KEY);  // ERROR: private
}
```

## Defining Static Mutable Variables


Static Immutable Variables
- An example of a static immutable variable is `message`, initialized with a compile-time constant, ensuring it remains unchanged.
- Another example is `timestamp`, which is initialized at runtime but is also immutable once set.

Mutable Static Variables and Thread Safety
- Mutable statics can be defined, but changing them requires ensuring thread safety, as the Rust compiler enforces this.
- Attempting to modify a mutable static variable without proper thread safety will result in a compilation error.

Using Mutex for Thread Safety
- A mutex (mutual exclusion lock) can be used to ensure that only one thread can access a mutable static variable at a time, preventing chaos from concurrent modifications.
- The concept is similar to locking a room where only one person can enter at a time.

Unsafe Code in Rust
- Rust allows marking functions as unsafe, which bypasses some safety checks, but this should be used cautiously.
- Unsafe functions can only be called from other unsafe functions, maintaining a level of control over thread safety.

Atomic Data Types
- Rust provides atomic data types that allow safe modifications of primitive types in a thread-safe manner without needing to write mutex code.
- These atomic types automatically handle locking, ensuring that operations on them are thread-safe.

Understanding Static Mutable Variables
- A global counter is declared using `atomic i32`, ensuring thread safety with an initial value of zero.
- Two functions are defined: one unsafe function with a local mutable counter and a safe function that uses a static local counter.

Thread Safety and Function Behavior
- The unsafe function increments a local counter and a global counter, requiring the unsafe keyword due to potential thread safety issues.
- The safe function manages its local counter in a thread-safe manner, allowing it to be called without the unsafe keyword.

Key Takeaways
- Static variables are not common and require careful handling to ensure thread safety, especially when accessed by multiple threads.
- Using atomics for primitive types or mutexes for general data types is essential for maintaining thread safety when modifying static variables.


### The Challenge of Mutable Statics

Mutable static variables are inherently unsafe in Rust because they can be accessed from multiple threads simultaneously, leading to data races. Rust requires explicit handling of thread safety for mutable statics.

**Why Mutable Statics Are Unsafe:**
```rust
// This compiles but requires unsafe to modify
static mut COUNTER: i32 = 0;

fn increment_unsafe() {
    unsafe {
        COUNTER += 1;  // Potential data race in multi-threaded code
        println!("Counter: {}", COUNTER);
    }
}

fn main() {
    increment_unsafe();
    increment_unsafe();
    // Output is predictable in single-threaded code,
    // but undefined behavior in multi-threaded scenarios
}
```

### Using Atomic Types for Thread-Safe Primitives

Atomic types provide thread-safe operations without requiring locks:

```rust
use std::sync::atomic::{AtomicI32, AtomicU64, AtomicBool, Ordering};
use std::thread;

// Global atomic counter (thread-safe)
static GLOBAL_COUNTER: AtomicI32 = AtomicI32::new(0);

// Global atomic flag
static SHUTDOWN_FLAG: AtomicBool = AtomicBool::new(false);

// Global request counter
static REQUEST_ID: AtomicU64 = AtomicU64::new(0);

fn increment_counter() -> i32 {
    // fetch_add returns the previous value
    GLOBAL_COUNTER.fetch_add(1, Ordering::SeqCst) + 1
}

fn get_next_request_id() -> u64 {
    REQUEST_ID.fetch_add(1, Ordering::SeqCst)
}

fn worker_thread(id: u32) {
    while !SHUTDOWN_FLAG.load(Ordering::Relaxed) {
        let count = increment_counter();
        let req_id = get_next_request_id();
        println!("Thread {}: count={}, request_id={}", id, count, req_id);
        thread::sleep(std::time::Duration::from_millis(100));
    }
    println!("Thread {} shutting down", id);
}

fn main() {
    // Spawn multiple threads
    let handles: Vec<_> = (0..3)
        .map(|i| thread::spawn(move || worker_thread(i)))
        .collect();
    
    // Let them run for a bit
    thread::sleep(std::time::Duration::from_secs(1));
    
    // Signal shutdown
    SHUTDOWN_FLAG.store(true, Ordering::Relaxed);
    
    // Wait for all threads
    for handle in handles {
        handle.join().unwrap();
    }
    
    println!("\nFinal counter: {}", GLOBAL_COUNTER.load(Ordering::SeqCst));
    println!("Total requests: {}", REQUEST_ID.load(Ordering::SeqCst));
}
```

### Memory Ordering Explained

```rust
use std::sync::atomic::{AtomicI32, Ordering};

static VALUE: AtomicI32 = AtomicI32::new(0);

fn demonstrate_ordering() {
    // Relaxed: No ordering guarantees, just atomicity
    VALUE.store(1, Ordering::Relaxed);
    
    // Acquire: All subsequent reads/writes stay after this
    let _v = VALUE.load(Ordering::Acquire);
    
    // Release: All previous reads/writes stay before this
    VALUE.store(2, Ordering::Release);
    
    // AcqRel: Combination of Acquire and Release
    VALUE.fetch_add(1, Ordering::AcqRel);
    
    // SeqCst: Strongest ordering, total ordering across threads
    VALUE.store(3, Ordering::SeqCst);
    let final_value = VALUE.load(Ordering::SeqCst);
    
    println!("Final value: {}", final_value);
}
```

### Using Mutex for Complex Mutable Data

For non-atomic types (structs, collections, etc.), use `Mutex`:

```rust
use std::sync::Mutex;
use std::thread;

struct Statistics {
    requests: u64,
    errors: u64,
    total_bytes: u64,
}

// Global statistics protected by Mutex
static STATS: Mutex<Statistics> = Mutex::new(Statistics {
    requests: 0,
    errors: 0,
    total_bytes: 0,
});

fn record_request(bytes: u64, is_error: bool) {
    let mut stats = STATS.lock().unwrap();
    stats.requests += 1;
    stats.total_bytes += bytes;
    if is_error {
        stats.errors += 1;
    }
}

fn print_stats() {
    let stats = STATS.lock().unwrap();
    println!("Statistics:");
    println!("  Requests: {}", stats.requests);
    println!("  Errors: {}", stats.errors);
    println!("  Total Bytes: {}", stats.total_bytes);
    println!("  Error Rate: {:.2}%", 
             (stats.errors as f64 / stats.requests as f64) * 100.0);
}

fn main() {
    let handles: Vec<_> = (0..5)
        .map(|i| {
            thread::spawn(move || {
                for j in 0..10 {
                    let bytes = (i * 100 + j * 10) as u64;
                    let is_error = j % 7 == 0;  // Some requests fail
                    record_request(bytes, is_error);
                    thread::sleep(std::time::Duration::from_millis(10));
                }
            })
        })
        .collect();
    
    for handle in handles {
        handle.join().unwrap();
    }
    
    print_stats();
}
```

### Using RwLock for Read-Heavy Workloads

`RwLock` allows multiple concurrent readers or one writer:

```rust
use std::sync::RwLock;
use std::thread;
use std::collections::HashMap;

// Global cache with RwLock (many readers, occasional writer)
static CACHE: RwLock<HashMap<String, String>> = 
    RwLock::new(HashMap::new());

fn get_from_cache(key: &str) -> Option<String> {
    let cache = CACHE.read().unwrap();  // Multiple threads can read
    cache.get(key).cloned()
}

fn update_cache(key: String, value: String) {
    let mut cache = CACHE.write().unwrap();  // Exclusive write access
    cache.insert(key, value);
}

fn main() {
    // Initial population
    update_cache("key1".to_string(), "value1".to_string());
    update_cache("key2".to_string(), "value2".to_string());
    
    // Multiple readers
    let readers: Vec<_> = (0..5)
        .map(|i| {
            thread::spawn(move || {
                for _ in 0..10 {
                    if let Some(val) = get_from_cache("key1") {
                        println!("Reader {}: {}", i, val);
                    }
                    thread::sleep(std::time::Duration::from_millis(50));
                }
            })
        })
        .collect();
    
    // One writer
    let writer = thread::spawn(|| {
        for i in 0..5 {
            update_cache(format!("key{}", i), format!("value{}", i));
            println!("Writer: updated key{}", i);
            thread::sleep(std::time::Duration::from_millis(200));
        }
    });
    
    for handle in readers {
        handle.join().unwrap();
    }
    writer.join().unwrap();
}
```

### Practical Example: Thread-Safe Event Counter

```rust
use std::sync::atomic::{AtomicU64, Ordering};
use std::sync::Mutex;
use std::collections::HashMap;
use std::thread;

#[derive(Debug, Clone, Copy, PartialEq, Eq, Hash)]
enum EventType {
    Login,
    Logout,
    PageView,
    ApiCall,
}

struct EventTracker {
    counts: HashMap<EventType, u64>,
}

impl EventTracker {
    fn new() -> Self {
        let mut counts = HashMap::new();
        counts.insert(EventType::Login, 0);
        counts.insert(EventType::Logout, 0);
        counts.insert(EventType::PageView, 0);
        counts.insert(EventType::ApiCall, 0);
        EventTracker { counts }
    }
    
    fn record(&mut self, event: EventType) {
        *self.counts.entry(event).or_insert(0) += 1;
    }
}

static EVENT_TRACKER: Mutex<EventTracker> = 
    Mutex::new(EventTracker { counts: HashMap::new() });
static TOTAL_EVENTS: AtomicU64 = AtomicU64::new(0);

fn record_event(event: EventType) {
    EVENT_TRACKER.lock().unwrap().record(event);
    TOTAL_EVENTS.fetch_add(1, Ordering::Relaxed);
}

fn print_summary() {
    let tracker = EVENT_TRACKER.lock().unwrap();
    println!("\n=== Event Summary ===");
    for (event, count) in &tracker.counts {
        println!("{:?}: {}", event, count);
    }
    println!("Total: {}", TOTAL_EVENTS.load(Ordering::Relaxed));
}

fn main() {
    // Simulate multiple threads generating events
    let handles: Vec<_> = (0..4)
        .map(|i| {
            thread::spawn(move || {
                let events = [
                    EventType::Login,
                    EventType::PageView,
                    EventType::ApiCall,
                    EventType::Logout,
                ];
                
                for _ in 0..25 {
                    let event = events[i % events.len()];
                    record_event(event);
                    thread::sleep(std::time::Duration::from_millis(10));
                }
            })
        })
        .collect();
    
    for handle in handles {
        handle.join().unwrap();
    }
    
    print_summary();
}
```

### Best Practices for Mutable Statics

1. **Prefer Atomic Types**: Use atomic types for simple counters and flags
2. **Use Mutex for Complex Data**: Protect structs and collections with `Mutex`
3. **Consider RwLock**: Use `RwLock` when reads far outnumber writes
4. **Avoid `static mut`**: Only use when absolutely necessary and document why
5. **Choose Appropriate Ordering**: Use the weakest memory ordering that maintains correctness

```rust
use std::sync::atomic::{AtomicBool, AtomicU64, Ordering};
use std::sync::Mutex;

// Good: Atomic for simple flag
static INITIALIZED: AtomicBool = AtomicBool::new(false);

// Good: Atomic for counter
static CALL_COUNT: AtomicU64 = AtomicU64::new(0);

// Good: Mutex for complex data
static DATA: Mutex<Vec<String>> = Mutex::new(Vec::new());

// Avoid: static mut (unsafe, easy to misuse)
// static mut BAD_COUNTER: i32 = 0;

fn example() {
    // Safe atomic operations
    if !INITIALIZED.swap(true, Ordering::Acquire) {
        println!("First initialization");
    }
    
    let count = CALL_COUNT.fetch_add(1, Ordering::Relaxed);
    
    // Safe mutex operations
    DATA.lock().unwrap().push(format!("Call {}", count));
}
```


## Understanding Strings in Rust
The content focuses on string handling in Rust, highlighting the differences between string literals and the string type.

String Literals
- String literals are immutable text enclosed in double quotes, statically allocated as part of the program.
- When declared, they are represented as string slices (e.g., `&str`), which include a pointer to the text and its length.

String Type
- The string type in Rust is mutable and dynamically allocated on the heap, allowing for changes and resizing.
- When a mutable string object goes out of scope, its memory is automatically deallocated, similar to a destructor in C++.

String Slices
- A string slice is a fat pointer that points to the first byte of the string and knows its length, ensuring memory safety.
- Rust uses lifetime specifiers to guarantee that the memory referenced by a string slice remains valid throughout its use.

Understanding String Objects
- A String object is created using the `String::from` method, which allocates memory on the heap for the text it holds.
- The String variable itself resides on the stack, while the actual text is stored on the heap, with the String object holding a pointer to this memory.

Memory Management and Deallocation
- When a String object goes out of scope, its destructor (the `drop` function) is automatically called to deallocate the memory on the heap.
- Rust does not use garbage collection; instead, it employs deterministic memory management, ensuring resources are cleaned up as objects are destroyed.

Mutable Strings and Methods
- Strings can be mutable, allowing modifications such as appending text or changing case.
- Various methods are available for querying and modifying String contents, such as `push_str` for appending and `trim` for removing whitespace.



### String Types Overview

Rust has two main string types, each serving different purposes:

1. **`&str` (String Slice)**: Immutable, borrowed reference to string data
2. **`String`**: Owned, growable, heap-allocated string

### String Literals and String Slices (`&str`)

String literals are hardcoded text in your program, stored in the binary:

```rust
fn string_literal_basics() {
    // String literal: type is &'static str
    let greeting: &str = "Hello, Rust!";
    
    // String literals are immutable
    println!("{}", greeting);
    
    // greeting.push_str(" More");  // ERROR: cannot modify &str
    
    // String literals have 'static lifetime
    let static_str: &'static str = "I live forever";
}
```

**String Slice Memory Layout:**
```rust
fn string_slice_internals() {
    let text = "Rust Programming";
    
    // A &str is a "fat pointer" containing:
    // 1. Pointer to the first byte of the string data
    // 2. Length (number of bytes)
    
    let slice: &str = &text[0..4];  // "Rust"
    println!("Slice: {}", slice);
    println!("Slice length: {}", slice.len());
    
    // String slices don't own their data
    // They're views into existing string data
}
```

**Working with String Slices:**
```rust
fn string_slice_operations() {
    let text = "  Hello, World!  ";
    
    // Trimming whitespace
    let trimmed = text.trim();
    println!("Trimmed: '{}'", trimmed);
    
    // Splitting strings
    let words: Vec<&str> = trimmed.split(", ").collect();
    println!("Words: {:?}", words);
    
    // Checking contents
    if trimmed.contains("World") {
        println!("Found 'World'");
    }
    
    // Case conversion (creates new String)
    let uppercase = trimmed.to_uppercase();  // Returns String
    println!("Uppercase: {}", uppercase);
    
    // Iterating over characters
    for ch in trimmed.chars() {
        print!("{} ", ch);
    }
    println!();
}
```

### The String Type

`String` is an owned, growable, UTF-8 encoded string type:

```rust
fn string_type_basics() {
    // Creating Strings
    let mut s1 = String::new();  // Empty string
    let s2 = String::from("Hello");  // From literal
    let s3 = "World".to_string();  // Convert &str to String
    let s4 = "Rust".to_owned();  // Another way to convert
    
    // Strings are mutable and growable
    s1.push_str("Hello");
    s1.push(' ');  // Push single char
    s1.push_str("Rust!");
    
    println!("{}", s1);  // "Hello Rust!"
}
```

**String Memory Layout:**
```rust
fn string_memory_layout() {
    let s = String::from("Hello");
    
    // String structure (on stack):
    // - ptr: pointer to heap data
    // - len: current length (5 bytes)
    // - capacity: allocated capacity (may be > len)
    
    println!("Length: {}", s.len());
    println!("Capacity: {}", s.capacity());
    
    // The actual string data ('H', 'e', 'l', 'l', 'o') is on the heap
    
    // String automatically deallocates heap memory when dropped
}
```

### String Ownership and Borrowing

```rust
fn string_ownership() {
    let s1 = String::from("Hello");
    
    // s1 is moved to s2 (not copied)
    let s2 = s1;
    
    // println!("{}", s1);  // ERROR: s1 no longer valid
    println!("{}", s2);     // OK
    
    // Borrowing strings
    let s3 = String::from("World");
    let len = calculate_length(&s3);  // Borrow, don't move
    println!("'{}' has length {}", s3, len);  // s3 still valid
}

fn calculate_length(s: &String) -> usize {
    s.len()
}

fn string_borrowing_best_practice(s: &str) {  // Accept &str, not &String
    println!("Length: {}", s.len());
}

fn example() {
    let owned = String::from("owned");
    let literal = "literal";
    
    // Both work with &str parameter
    string_borrowing_best_practice(&owned);
    string_borrowing_best_practice(literal);
}
```

### String Manipulation

```rust
fn string_manipulation() {
    let mut s = String::from("Hello");
    
    // Appending
    s.push_str(", World");
    s.push('!');
    println!("{}", s);
    
    // Concatenation with +
    let s1 = String::from("Hello");
    let s2 = String::from(" World");
    let s3 = s1 + &s2;  // s1 is moved, s2 is borrowed
    // println!("{}", s1);  // ERROR: s1 moved
    println!("{}", s3);
    
    // Concatenation with format! (doesn't take ownership)
    let s1 = String::from("Hello");
    let s2 = String::from("World");
    let s3 = format!("{}, {}!", s1, s2);
    println!("{}", s3);
    println!("{} {}", s1, s2);  // s1 and s2 still valid
}
```

### String Slicing and Indexing

```rust
fn string_slicing() {
    let s = String::from("Hello, Rust!");
    
    // String slicing
    let hello = &s[0..5];
    let rust = &s[7..11];
    println!("{} {}", hello, rust);
    
    // Be careful with slicing and UTF-8
    let greeting = String::from("Здравствуйте");  // Russian "Hello"
    
    // Each Cyrillic character is 2 bytes in UTF-8
    let slice = &greeting[0..4];  // First 2 characters
    println!("{}", slice);
    
    // let bad_slice = &greeting[0..1];  // PANIC: not on char boundary
}

fn string_iteration() {
    let s = String::from("Hello");
    
    // Iterate over characters
    for ch in s.chars() {
        println!("Char: {}", ch);
    }
    
    // Iterate over bytes
    for byte in s.bytes() {
        println!("Byte: {}", byte);
    }
    
    // For Unicode, use chars()
    let russian = "Привет";
    for ch in russian.chars() {
        println!("{}", ch);
    }
}
```

### String Methods

```rust
fn string_methods() {
    let mut s = String::from("  Hello, Rust!  ");
    
    // Query methods
    println!("Length: {}", s.len());
    println!("Is empty: {}", s.is_empty());
    println!("Starts with 'Hello': {}", s.trim().starts_with("Hello"));
    println!("Ends with 'Rust!': {}", s.trim().ends_with("Rust!"));
    println!("Contains 'Rust': {}", s.contains("Rust"));
    
    // Modification methods
    s = s.trim().to_string();
    println!("Trimmed: '{}'", s);
    
    let upper = s.to_uppercase();
    let lower = s.to_lowercase();
    println!("Upper: {}", upper);
    println!("Lower: {}", lower);
    
    let replaced = s.replace("Rust", "World");
    println!("Replaced: {}", replaced);
    
    // Clearing and truncating
    let mut mutable = String::from("Hello, Rust!");
    mutable.truncate(5);
    println!("Truncated: {}", mutable);
    
    mutable.clear();
    println!("Is empty after clear: {}", mutable.is_empty());
}
```

### String Capacity and Performance

```rust
fn string_capacity() {
    // String allocation and reallocation
    let mut s = String::new();
    println!("Initial capacity: {}", s.capacity());
    
    for _ in 0..5 {
        s.push_str("Hello");
        println!("Len: {}, Capacity: {}", s.len(), s.capacity());
    }
    
    // Pre-allocate to avoid reallocations
    let mut efficient = String::with_capacity(100);
    println!("Pre-allocated capacity: {}", efficient.capacity());
    
    for _ in 0..10 {
        efficient.push_str("Data");
    }
    println!("Final - Len: {}, Capacity: {}", 
             efficient.len(), efficient.capacity());
    
    // Shrink to fit
    efficient.shrink_to_fit();
    println!("After shrink - Capacity: {}", efficient.capacity());
}
```

### Practical String Examples

```rust
// Building formatted strings
fn build_report(name: &str, age: u32, score: f64) -> String {
    format!(
        "Name: {}\nAge: {}\nScore: {:.2}\nGrade: {}",
        name,
        age,
        score,
        if score >= 90.0 { "A" } else { "B" }
    )
}

// Parsing strings
fn parse_csv_line(line: &str) -> Vec<String> {
    line.split(',')
        .map(|s| s.trim().to_string())
        .collect()
}

// String validation
fn validate_email(email: &str) -> bool {
    email.contains('@') && email.contains('.')
}

fn main() {
    let report = build_report("Alice", 25, 95.5);
    println!("{}\n", report);
    
    let csv = "John, Doe, 30, Engineer";
    let fields = parse_csv_line(csv);
    println!("CSV fields: {:?}\n", fields);
    
    let email = "user@example.com";
    println!("Email valid: {}", validate_email(email));
}
```

### String vs &str: When to Use Which

```rust
// Return &str when returning string literals or borrowed slices
fn get_greeting() -> &'static str {
    "Hello"
}

// Return String when creating new strings or need ownership
fn create_greeting(name: &str) -> String {
    format!("Hello, {}!", name)
}

// Accept &str for maximum flexibility (works with both String and &str)
fn print_message(msg: &str) {
    println!("{}", msg);
}

// Accept String only if you need ownership
fn take_ownership(s: String) {
    println!("{}", s);
    // s is dropped here
}

fn main() {
    let literal = get_greeting();
    let owned = create_greeting("Alice");
    
    print_message(literal);      // &str works
    print_message(&owned);       // &String coerces to &str
    
    take_ownership(owned);       // Moves ownership
    // println!("{}", owned);    // ERROR: owned was moved
}
```

### UTF-8 and Unicode Handling

```rust
fn unicode_examples() {
    // Rust strings are always valid UTF-8
    let hello = String::from("Hello");           // ASCII
    let nihao = String::from("你好");             // Chinese
    let zdras = String::from("Здравствуйте");    // Russian
    let emoji = String::from("👋🌍");             // Emojis
    
    println!("{} {} {} {}", hello, nihao, zdras, emoji);
    
    // Character counts
    println!("\n'你好' has {} bytes, {} chars",
             nihao.len(),           // Bytes
             nihao.chars().count() // Unicode scalar values
    );
    
    // Working with multi-byte characters
    for (i, ch) in emoji.chars().enumerate() {
        println!("Char {}: {} (Unicode: U+{:X})", i, ch, ch as u32);
    }
}
```

## Copying vs. Moving

Copying and Moving in Rust
- The `copy` trait determines how values are assigned and copied in Rust. Simple types like integers and booleans implement this trait, allowing for straightforward bit copying.
- For types that do not implement the `copy` trait, such as strings, assigning one variable to another transfers ownership. The original variable becomes unusable after the assignment.

Ownership Transfer
- When a variable that does not implement `copy` is assigned to another, ownership of the data is moved. The original variable loses its ability to access the data, leading to a compiler error if accessed afterward.
- This ownership model simplifies memory management, ensuring that only one variable owns the data at a time, which helps prevent memory leaks and dangling pointers.

### The Copy Trait

Rust uses traits to determine whether a type is copied or moved during assignment:

**Copy Trait:**
- Implemented by types that can be duplicated by simply copying bits
- Stack-only data types (no heap allocation)
- Assignment creates a new, independent copy
- Original value remains valid after assignment

**Types that Implement Copy:**
```rust
fn copy_trait_examples() {
    // All scalar types implement Copy
    let x = 5;           // i32: Copy
    let y = x;           // x is copied to y
    println!("x: {}, y: {}", x, y);  // Both are valid
    
    let a = 3.14;        // f64: Copy
    let b = a;
    println!("a: {}, b: {}", a, b);
    
    let flag1 = true;    // bool: Copy
    let flag2 = flag1;
    println!("flag1: {}, flag2: {}", flag1, flag2);
    
    let ch1 = 'A';       // char: Copy
    let ch2 = ch1;
    println!("ch1: {}, ch2: {}", ch1, ch2);
    
    // Tuples of Copy types are Copy
    let point1 = (10, 20);
    let point2 = point1;
    println!("point1: {:?}, point2: {:?}", point1, point2);
    
    // Fixed-size arrays of Copy types are Copy
    let arr1 = [1, 2, 3];
    let arr2 = arr1;
    println!("arr1: {:?}, arr2: {:?}", arr1, arr2);
}
```

### Types that Don't Implement Copy

Types that manage heap resources don't implement Copy:

```rust
fn non_copy_types() {
    // String doesn't implement Copy (has heap data)
    let s1 = String::from("Hello");
    let s2 = s1;  // s1 is moved to s2
    
    // println!("{}", s1);  // ERROR: value moved to s2
    println!("{}", s2);     // OK
    
    // Vec doesn't implement Copy
    let v1 = vec![1, 2, 3];
    let v2 = v1;  // v1 is moved to v2
    
    // println!("{:?}", v1);  // ERROR: value moved
    println!("{:?}", v2);
    
    // Box doesn't implement Copy
    let b1 = Box::new(42);
    let b2 = b1;  // b1 is moved
    
    // println!("{}", b1);  // ERROR
    println!("{}", b2);
}
```

### Move Semantics

When a value doesn't implement Copy, assignment transfers ownership:

```rust
fn move_semantics() {
    let s1 = String::from("Hello");
    
    // Move occurs here
    let s2 = s1;
    
    // What happened:
    // 1. s2 now points to the same heap data as s1 did
    // 2. s1 is invalidated (no longer accessible)
    // 3. Only s2 can access the data
    // 4. When s2 goes out of scope, the data is freed once
    
    println!("{}", s2);
}

fn move_in_function_calls() {
    let s = String::from("Hello");
    
    take_ownership(s);  // s is moved into function
    
    // println!("{}", s);  // ERROR: s was moved
}

fn take_ownership(some_string: String) {
    println!("Received: {}", some_string);
}  // some_string is dropped here
```

### Move Semantics with Function Returns

```rust
fn move_with_returns() {
    let s1 = gives_ownership();  // Return value moved to s1
    println!("s1: {}", s1);
    
    let s2 = String::from("Hello");
    let s3 = takes_and_gives_back(s2);  // s2 moved in, return value moved to s3
    
    // println!("{}", s2);  // ERROR: s2 was moved
    println!("s3: {}", s3);
}

fn gives_ownership() -> String {
    let some_string = String::from("Yours");
    some_string  // Moved to caller
}

fn takes_and_gives_back(a_string: String) -> String {
    a_string  // Moved back to caller
}
```

### Why Move Semantics?

Move semantics prevent common bugs:

```rust
// Imagine if Rust allowed this:
fn hypothetical_problem() {
    let s1 = String::from("Hello");
    let s2 = s1;  // If both were valid...
    
    // Both s1 and s2 would point to the same heap memory
    // When s1 goes out of scope, memory is freed
    // When s2 goes out of scope, double-free error!
    
    // Rust prevents this with move semantics
}
```

**Benefits of Move Semantics:**
1. **No Double-Free**: Only one owner can free memory
2. **Clear Ownership**: Always know who owns what
3. **Zero-Cost**: No runtime overhead
4. **Memory Safety**: Compile-time guarantees

### Implementing Copy for Custom Types

You can implement Copy for your own types if they only contain Copy types:

```rust
// Simple struct with Copy types can derive Copy
#[derive(Copy, Clone, Debug)]
struct Point {
    x: i32,
    y: i32,
}

fn copy_custom_type() {
    let p1 = Point { x: 10, y: 20 };
    let p2 = p1;  // p1 is copied (not moved)
    
    println!("p1: {:?}", p1);  // p1 still valid
    println!("p2: {:?}", p2);
}

// Struct with non-Copy field cannot derive Copy
struct Container {
    name: String,  // String doesn't implement Copy
}

fn non_copy_struct() {
    let c1 = Container {
        name: String::from("Container1"),
    };
    
    let c2 = c1;  // c1 is moved
    
    // println!("{}", c1.name);  // ERROR
    println!("{}", c2.name);
}
```

### Copy vs Clone

```rust
#[derive(Copy, Clone, Debug)]
struct SmallData {
    value: i32,
}

#[derive(Clone, Debug)]
struct LargeData {
    values: Vec<i32>,
}

fn copy_vs_clone() {
    // Copy: implicit, cheap, always bitwise copy
    let small1 = SmallData { value: 42 };
    let small2 = small1;  // Automatic copy
    println!("small1: {:?}, small2: {:?}", small1, small2);
    
    // Clone: explicit, may be expensive, can do deep copy
    let large1 = LargeData {
        values: vec![1, 2, 3, 4, 5],
    };
    // let large2 = large1;  // Would move
    let large2 = large1.clone();  // Explicit clone
    println!("large1: {:?}", large1);  // Still valid
    println!("large2: {:?}", large2);
}
```

### Practical Examples

```rust
// Working with Copy types
fn process_number(n: i32) -> i32 {
    n * 2  // i32 is Copy, so n is copied in
}

fn use_copy_types() {
    let num = 10;
    let doubled = process_number(num);
    println!("num: {}, doubled: {}", num, doubled);  // num still valid
}

// Working with Move types - Pattern 1: Return ownership
fn process_string_return(s: String) -> String {
    let result = s.to_uppercase();
    result  // Return ownership
}

fn use_move_with_return() {
    let text = String::from("hello");
    let upper = process_string_return(text);
    // text is moved, but we got upper back
    println!("{}", upper);
}

// Working with Move types - Pattern 2: Borrowing (better)
fn process_string_borrow(s: &str) -> String {
    s.to_uppercase()
}

fn use_move_with_borrow() {
    let text = String::from("hello");
    let upper = process_string_borrow(&text);
    println!("Original: {}, Upper: {}", text, upper);  // text still valid
}

// Working with Move types - Pattern 3: Mutable borrowing
fn process_string_mut(s: &mut String) {
    s.make_ascii_uppercase();
}

fn use_move_with_mut_borrow() {
    let mut text = String::from("hello");
    process_string_mut(&mut text);
    println!("{}", text);  // Modified in place
}
```

### Move Semantics in Collections

```rust
fn moves_in_collections() {
    let strings = vec![
        String::from("Hello"),
        String::from("World"),
    ];
    
    // Indexing moves the value out
    // let first = strings[0];  // ERROR: can't move out of Vec
    
    // Options to access without moving:
    
    // 1. Borrow
    let first_ref = &strings[0];
    println!("Borrowed: {}", first_ref);
    
    // 2. Clone
    let first_clone = strings[0].clone();
    println!("Cloned: {}", first_clone);
    
    // 3. Iterate by reference
    for s in &strings {
        println!("Ref: {}", s);
    }
    
    // 4. Move out with methods
    let mut mut_strings = vec![String::from("A"), String::from("B")];
    if let Some(first) = mut_strings.pop() {
        println!("Popped: {}", first);
    }
}
```

### Summary Table

| Aspect | Copy Types | Move Types |
|--------|-----------|------------|
| **Examples** | i32, f64, bool, char, tuples of Copy types | String, Vec, Box, HashMap |
| **Assignment** | Creates independent copy | Transfers ownership |
| **After assignment** | Both variables valid | Original variable invalid |
| **Memory** | Stack-only data | May have heap allocation |
| **Performance** | Very cheap (bitwise copy) | Zero-cost (no copy) |
| **Trait** | Requires `Copy` + `Clone` | Only `Clone` (if any) |


## Cloning
Ownership and Copy Trait
- In Rust, types that do not implement the copy trait, like strings, transfer ownership when assigned to another variable, invalidating the original variable.
- For example, when a string is assigned from S1 to S2, S1 becomes invalid, and attempting to use it results in a compiler error.

Cloning with the Clone Trait
- The clone trait allows for a deep copy of data, creating a duplicate of the original value rather than just copying the pointer.
- By calling the clone method on a string, both the original and the cloned string can coexist without losing their values.

Practical Example
- In a practical example, a mutable string S1 can be cloned to create S2, allowing modifications to S1 without affecting S2.
- The lesson concludes with a mention of the next topic: borrowing, which involves referencing an object without claiming ownership.

### Understanding Clone

The `Clone` trait enables explicit deep copying of data. Unlike `Copy`, which happens implicitly and is always a simple bitwise copy, `Clone` requires explicit invocation and can perform complex operations like heap allocation.

**Key Differences:**
- **Copy**: Implicit, cheap, bitwise, stack-only data
- **Clone**: Explicit, potentially expensive, can duplicate heap data

### Basic Cloning

```rust
fn basic_cloning() {
    let s1 = String::from("Hello");
    
    // Clone creates a deep copy
    let s2 = s1.clone();
    
    // Both are now valid and independent
    println!("s1: {}", s1);
    println!("s2: {}", s2);
    
    // Modifying one doesn't affect the other
    let mut s3 = s1.clone();
    s3.push_str(", World!");
    
    println!("s1: {}", s1);  // Still "Hello"
    println!("s3: {}", s3);  // "Hello, World!"
}
```

### Clone vs Move

```rust
fn clone_vs_move() {
    // Without clone: move occurs
    let v1 = vec![1, 2, 3];
    let v2 = v1;  // v1 moved to v2
    // println!("{:?}", v1);  // ERROR: v1 moved
    
    // With clone: both remain valid
    let v3 = vec![4, 5, 6];
    let v4 = v3.clone();
    println!("v3: {:?}", v3);  // Still valid
    println!("v4: {:?}", v4);  // Also valid
}
```

### How Clone Works

```rust
// Clone creates a deep copy of heap data
fn clone_internals() {
    let original = String::from("Rust");
    
    // Memory before clone:
    // Stack: [original: ptr→heap, len: 4, cap: 4]
    // Heap: ['R', 'u', 's', 't']
    
    let cloned = original.clone();
    
    // Memory after clone:
    // Stack: [original: ptr→heap1, len: 4, cap: 4]
    //        [cloned: ptr→heap2, len: 4, cap: 4]
    // Heap1: ['R', 'u', 's', 't']  (original)
    // Heap2: ['R', 'u', 's', 't']  (cloned copy)
    
    println!("Original: {}", original);
    println!("Cloned: {}", cloned);
}
```

### Clone with Collections

```rust
fn clone_collections() {
    // Cloning vectors
    let v1 = vec![1, 2, 3, 4, 5];
    let v2 = v1.clone();
    
    println!("v1: {:?}", v1);
    println!("v2: {:?}", v2);
    
    // Cloning nested data structures
    let nested = vec![
        vec![1, 2],
        vec![3, 4],
        vec![5, 6],
    ];
    let nested_clone = nested.clone();
    
    println!("Original: {:?}", nested);
    println!("Clone: {:?}", nested_clone);
    
    // Cloning HashMaps
    use std::collections::HashMap;
    
    let mut map1 = HashMap::new();
    map1.insert("key1", "value1");
    map1.insert("key2", "value2");
    
    let map2 = map1.clone();
    
    println!("map1: {:?}", map1);
    println!("map2: {:?}", map2);
}
```

### Implementing Clone for Custom Types

```rust
// Automatic derivation (recommended)
#[derive(Clone, Debug)]
struct Person {
    name: String,
    age: u32,
}

fn derive_clone() {
    let person1 = Person {
        name: String::from("Alice"),
        age: 30,
    };
    
    let person2 = person1.clone();
    
    println!("person1: {:?}", person1);
    println!("person2: {:?}", person2);
}

// Manual implementation for custom behavior
struct CustomData {
    id: u32,
    data: Vec<i32>,
}

impl Clone for CustomData {
    fn clone(&self) -> Self {
        println!("Cloning CustomData with id: {}", self.id);
        CustomData {
            id: self.id,
            data: self.data.clone(),  // Clone the Vec
        }
    }
}

fn manual_clone() {
    let data1 = CustomData {
        id: 1,
        data: vec![10, 20, 30],
    };
    
    let data2 = data1.clone();
    println!("Cloned data with id: {}", data2.id);
}
```

### Clone with Reference Counted Types

```rust
use std::rc::Rc;
use std::sync::Arc;

fn clone_reference_counted() {
    // Rc: Reference counted pointer (single-threaded)
    let rc1 = Rc::new(String::from("Shared data"));
    let rc2 = rc1.clone();  // Increments reference count, doesn't clone data
    let rc3 = rc1.clone();
    
    println!("rc1: {}, strong count: {}", rc1, Rc::strong_count(&rc1));
    println!("rc2: {}, strong count: {}", rc2, Rc::strong_count(&rc2));
    println!("rc3: {}, strong count: {}", rc3, Rc::strong_count(&rc3));
    
    // Arc: Atomic reference counted (thread-safe)
    let arc1 = Arc::new(vec![1, 2, 3]);
    let arc2 = arc1.clone();
    
    println!("arc1: {:?}, strong count: {}", arc1, Arc::strong_count(&arc1));
    println!("arc2: {:?}, strong count: {}", arc2, Arc::strong_count(&arc2));
}
```

### Performance Considerations

```rust
fn performance_considerations() {
    // Clone can be expensive for large data
    let large_vec = vec![0; 1_000_000];
    
    // This allocates 1 million integers on the heap
    let cloned_vec = large_vec.clone();
    
    println!("Cloned {} elements", cloned_vec.len());
    
    // Consider alternatives:
    
    // 1. Borrow instead of clone
    fn process_data(data: &Vec<i32>) {
        println!("Processing {} elements", data.len());
    }
    
    process_data(&large_vec);  // No clone needed
    
    // 2. Use Rc/Arc for shared ownership
    let shared = Arc::new(large_vec);
    let shared_ref = shared.clone();  // Cheap, just increments counter
    
    // 3. Move if original isn't needed
    fn take_ownership(data: Vec<i32>) {
        println!("Took ownership of {} elements", data.len());
    }
    
    // take_ownership(large_vec);  // Move, no clone
}
```

### Clone in Function Parameters

```rust
// Anti-pattern: unnecessary clone
fn process_name_bad(name: String) {
    println!("Hello, {}!", name);
}

fn use_bad() {
    let name = String::from("Alice");
    process_name_bad(name.clone());  // Unnecessary clone
    println!("Name: {}", name);
}

// Better: borrow
fn process_name_good(name: &str) {
    println!("Hello, {}!", name);
}

fn use_good() {
    let name = String::from("Alice");
    process_name_good(&name);  // No clone needed
    println!("Name: {}", name);
}

// When to clone: need independent copy
fn modify_and_return(mut data: Vec<i32>) -> Vec<i32> {
    data.push(100);
    data
}

fn use_clone_when_needed() {
    let original = vec![1, 2, 3];
    let modified = modify_and_return(original.clone());  // Clone needed
    
    println!("Original: {:?}", original);
    println!("Modified: {:?}", modified);
}
```

### Clone in Data Structures

```rust
#[derive(Clone, Debug)]
struct Employee {
    id: u32,
    name: String,
    department: String,
}

#[derive(Clone, Debug)]
struct Company {
    name: String,
    employees: Vec<Employee>,
}

fn clone_nested_structures() {
    let company1 = Company {
        name: String::from("TechCorp"),
        employees: vec![
            Employee {
                id: 1,
                name: String::from("Alice"),
                department: String::from("Engineering"),
            },
            Employee {
                id: 2,
                name: String::from("Bob"),
                department: String::from("Sales"),
            },
        ],
    };
    
    // Deep clone: copies all nested data
    let mut company2 = company1.clone();
    company2.name = String::from("TechCorp Copy");
    company2.employees[0].name = String::from("Alice Copy");
    
    println!("Company 1: {}", company1.name);
    println!("  Employee: {}", company1.employees[0].name);
    println!("Company 2: {}", company2.name);
    println!("  Employee: {}", company2.employees[0].name);
}
```

### Clone with Ownership Patterns

```rust
fn clone_ownership_patterns() {
    let data = vec![1, 2, 3, 4, 5];
    
    // Pattern 1: Clone to pass but keep original
    let result1 = process_and_transform(data.clone());
    println!("Original kept: {:?}", data);
    println!("Result: {:?}", result1);
    
    // Pattern 2: Clone inside function if needed
    fn process_maybe_clone(data: &Vec<i32>, modify: bool) -> Vec<i32> {
        if modify {
            let mut cloned = data.clone();
            cloned.push(999);
            cloned
        } else {
            data.clone()
        }
    }
    
    let result2 = process_maybe_clone(&data, true);
    println!("Result with modification: {:?}", result2);
    
    // Pattern 3: Clone on write (COW - Copy on Write)
    use std::borrow::Cow;
    
    fn process_cow(data: Cow<str>) -> Cow<str> {
        if data.contains("modify") {
            Cow::Owned(data.to_uppercase())
        } else {
            data  // No clone if not modified
        }
    }
    
    let s1 = "hello";
    let s2 = "modify this";
    
    println!("COW 1: {}", process_cow(Cow::Borrowed(s1)));
    println!("COW 2: {}", process_cow(Cow::Borrowed(s2)));
}

fn process_and_transform(mut data: Vec<i32>) -> Vec<i32> {
    data.iter_mut().for_each(|x| *x *= 2);
    data
}
```

### Shallow vs Deep Clone

```rust
use std::rc::Rc;

#[derive(Clone, Debug)]
struct ShallowExample {
    id: u32,
    shared_data: Rc<Vec<i32>>,  // Rc clone is shallow
}

fn shallow_vs_deep() {
    let original = ShallowExample {
        id: 1,
        shared_data: Rc::new(vec![1, 2, 3]),
    };
    
    let cloned = original.clone();
    
    println!("Original Rc count: {}", Rc::strong_count(&original.shared_data));
    println!("Cloned Rc count: {}", Rc::strong_count(&cloned.shared_data));
    // Both show 2: they share the same data
    
    // Compare with deep clone
    #[derive(Clone, Debug)]
    struct DeepExample {
        id: u32,
        owned_data: Vec<i32>,  // Vec clone is deep
    }
    
    let deep_original = DeepExample {
        id: 1,
        owned_data: vec![1, 2, 3],
    };
    
    let deep_cloned = deep_original.clone();
    // Each has its own independent Vec
}
```

### Best Practices

```rust
// 1. Prefer borrowing over cloning
fn best_practice_borrow(data: &[i32]) -> i32 {
    data.iter().sum()
}

// 2. Clone only when necessary
fn best_practice_selective_clone(data: &Vec<String>, indices: &[usize]) -> Vec<String> {
    indices.iter()
        .filter_map(|&i| data.get(i))
        .cloned()  // Clone only selected elements
        .collect()
}

// 3. Document expensive clones
/// Performs a deep clone of the entire dataset.
/// Warning: This operation is O(n) and allocates heap memory.
fn expensive_operation(data: &Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    data.clone()
}

// 4. Use Rc/Arc for shared ownership
fn best_practice_shared(data: Vec<i32>) -> (Arc<Vec<i32>>, Arc<Vec<i32>>) {
    let shared = Arc::new(data);
    (shared.clone(), shared.clone())  // Cheap clones
}

fn main() {
    let data = vec![1, 2, 3, 4, 5];
    
    // Good: borrow
    let sum = best_practice_borrow(&data);
    
    // Good: selective clone
    let subset = best_practice_selective_clone(&vec![
        String::from("a"),
        String::from("b"),
        String::from("c"),
    ], &[0, 2]);
    
    // Document when expensive
    let nested = vec![vec![1, 2], vec![3, 4]];
    let cloned = expensive_operation(&nested);
    
    println!("Sum: {}", sum);
    println!("Subset: {:?}", subset);
    println!("Cloned: {:?}", cloned);
}
```

### Summary

**When to Clone:**
- Need independent copies that can be modified separately
- Returning owned data from functions while keeping original
- Working with data that needs to outlive borrowed references
- Explicitly want a deep copy of heap-allocated data

**When NOT to Clone:**
- Can use borrowing instead (&T or &mut T)
- Can move ownership (when original isn't needed)
- Can use Rc/Arc for shared ownership
- Performance is critical and data is large

**Key Takeaways:**
1. Clone is explicit and potentially expensive
2. Always consider borrowing first
3. Use Rc/Arc for cheap shared ownership
4. Derive Clone for custom types when possible
5. Document expensive clone operations