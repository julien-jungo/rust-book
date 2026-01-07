# The Rust Programming Language

## 1. Getting Started

### 1.1 Installation

#### Install rustup

```shell
$ curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

#### Update rustup

```shell
$ rustup update
```

#### Uninstall rustup

```shell
$ rustup self uninstall
```

### 1.2 Hello, World!

#### Compile & Run

```shell
$ rustc main.rs
$ ./main
```

#### Format

```shell
$ rustfmt main.rs
```

### 1.3 Hello, Cargo!

#### Create Project

```shell
$ cargo new hello_cargo
```

#### Create Cargo.toml

```shell
$ cargo init
```

#### Build Project

```shell
$ cargo build
$ cargo build --release
```

#### Run Code

```shell
$ cargo run
```

## 2. Programming the Guessing Game

[main.rs](../code/guessing_game/src/main.rs)

## 3. Common Programming Concepts

### 3.1 Variables and Mutability

```rust
fn main() {
    let x = 1;
    x = 2; // error

    let mut y = 1;
    y = 2; // ok
}
```

#### Constants

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3; // evaluated at compile time
```

#### Shadowing

```rust
fn main() {
    let x = 1;
    let x = x + 1;

    {
        let x = x * 2;
        println!("{x}"); // 4
    }

    println!("{x}"); // 2
}
```

```rust
let spaces = "   ";        // &str
let spaces = spaces.len(); // usize
```

### 3.2 Data Types

#### Integer Types

| Length                 | Signed | Unsigned |
|------------------------|--------|----------|
| 8-bit                  | i8     | u8       |
| 16-bit                 | i16    | u16      |
| 32-bit                 | i32    | u32      |
| 64-bit                 | i64    | u64      |
| 128-bit                | i128   | u128     |
| architecture dependent | isize  | usize    |

#### Floating-Point Types

```rust
let x = 1.0;      // f64
let y: f32 = 2.0; // f32
```

#### Boolean Type

```rust
let t: bool = true;
```

#### Character Type

```rust
let z: char = 'ℤ'; // unicode (4 bytes)
```

### Tuple Type

```rust
let tup = (500, 6.4, 1);

let (x, y, z) = tup;

println!("{}", tup.0); // 500

let mut tup = (1, 2);
x.0 = 0;
x.1 += 10;
```

### Array Type

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];

let b = [3; 5]; // [3, 3, 3, 3, 3]

println!("{}", a[0]); // 1
```

### 3.3 Functions

```rust
fn sum(x: i32, y: i32) {
  x + y
}

fn main() {
  println!("{}", sum(1, 2));
}
```

### 3.4 Comments

```rust
// singleline
/* multiline */
```

### 3.5 Control Flow

#### `if`

```rust
let num = 2;

if num % 2 == 0 {
  println!("even");
} else {
  println!("odd");
}
```

```rust
let parity = if num % 2 == 0 { "even" } else { "odd" };
```

#### `loop`

```rust
fn main() {
  let mut counter = 0;

  let result = loop {
    counter += 1;

    if counter == 10 {
      break counter * 2;
    }
  }
}
```

```rust
fn main() {
  let mut count = 0;

  'counting_up: loop {
    println!("count = {count}");

    let mut remaining = 10;

    loop {
      println!("remaining = {remaining}");

      if remaining == 9 {
        break;
      }
      if count == 2 {
        break 'counting_up;
      }

      remaining -= 1;
    }

    count += 1;
  }

  println!("end count = {count}"); // 2
}
```

#### `while`

```rust
fn main() {
  let mut num = 3;

  while num != 0 {
    println!("{num}!");
    num -= 1;
  }

  println!("Liftoff!");
}
```

#### `for`

```rust
fn main() {
  let a = [10, 20, 30, 40, 50];

  for num in a {
    println!("{num}");
  }
}
```

```rust
fn main() {
  for num in (1..=3).rev() {
    println!("{num}!");
  }
  println!("Liftoff!");
}
```

## 4. Understanding Ownership

### 4.1 What is Ownership?

- All heap data must be owned by exactly one variable
- Rust deallocates heap data once its owner goes out of scope
- Ownership can be transferred by moves, which happen on assignments and function calls
- Heap data can only be accessed through its current owner, not a previous owner

#### Stack

```rust
let a = [0; 1_000_000];
let b = a; // copied
```

#### Heap

```rust
let a = Box::new([0; 1_000_000]);
let b = a; // moved
```

#### Example: String

```rust
fn main() {
  let first = String::from("Ferris"); // first is owner
  let full = add_suffic(first);       // full is owner
  println!("{full}");
}

fn add_suffix(mut name: String) -> String {
  name.push_str(" Jr."); // name is owner
  name
}
```

```rust
fn main() {
  let first = String::from("Ferris");
  let full = add_suffix(first.clone());
  println!("{full}, originally {first}");
}

fn add_suffix(mut name: String) -> String {
  name.push_str(" Jr.");
  name
}
```

### 4.2 References and Borrowing

- All variables can read, own, and (optionally) write their data
- Creating a reference will transfer permissions from the borrowed place to the reference
- Permissions are returned once the reference’s lifetime has ended
- Data must outlive all references that point to it

#### References

References are **non-owning pointers**

```rust
fn main() {
  let m1 = String::from("Hello");
  let m2 = String::from("world");

  greet(&m1, &m2);                  // m1 & m2 are borrowed
  let s = format!("{} {}", m1, m2); // i.e. NOT deallocated
}

fn greet(g1: &String, g2: &String) {
  println!("{} {}!", g1, g2);
}
```

#### Dereferencing

```rust
let mut x: Box<i32> = Box::new(1);

let a: i32 = *x;        // reads heap value

*x += 1;                // modifies heap value

let r1: &Box<i32> = &x; // points to x on the stack
let b: i32 = **r1;      // reads heap value

let r2: &i32 = &*x;     // points to heap value
let c: i32 = *r2;       // reads heap value
```

```rust
let x: Box<i32> = Box::new(-1);
let x_abs1 = i32::abs(*x); // explicit dereference
let x_abs2 = x.abs();      // implicit dereference
assert_eq!(x_abs1, x_abs2);

let r: &Box<i32> = &x;
let r_abs1 = i32::abs(**r); // explicit dereference (twice)
let r_abs2 = r.abs();       // implicit dereference (twice)
assert_eq!(r_abs1, r_abs2);

let s = String::from("Hello");
let s_len1 = str::len(&s); // explicit reference
let s_len2 = s.len();      // implicit reference
assert_eq!(s_len1, s_len2);
```

#### Pointer Safety Principle

Data should never be aliased and mutated at the same time

#### Permissions

- **Read** (**R**): data can be copied to another location
- **Write** (**W**): data can be mutated
- **Own** (**O**): data can be moved or dropped

```rust
let mut v: Vec<i32> = vec![1, 2, 3];

// v:    R W O

let num: &i32 = &v[2];

// v:    R - -
// num:  R - O
// *num: R - -

println!("third element is {}", *num);

// v:    R W O
// num:  - - -
// *num: - - -

v.push(4);

// v:    - - -
```

```rust
let x = 0;

// x:      R - O

let mut x_ref = &x;

// x:      R - -
// x_ref:  R W O
// *x_ref: R - -
```

#### Mutable References

```rust
let mut v: Vec<i32> = vec![1, 2, 3];

// v:    R W O

let num: &mut i32 = &mut v[2];

// v:    - - -
// num:  R - O
// *num: R W -

*num += 1;

println!("third element is {}", *num);

// v:    R W O
// num:  - - -
// *num: - - -

println!("vector is now {:?}", v);

// v:    - - -
```

```rust
// ...

let num1: &mut i32 = &mut v[2];

// *num1: R W -
// ...

let num2: &i32 = &*num; // downgrade

// *num1: R - -
// *num2: R - -
// ...

println!("{} {}", *num1, *num2);
```

### 4.3 Fixing Ownership Errors

#### Returning a Reference to the Stack

##### Problem

```rust
fn function() -> &String {
  let s = String::from("Hello world");
  &s
}
```

##### Solutions

```rust
fn function() -> String {
  let s = String::from("Hello world");
  s
}
```

```rust
fn function() -> &'static str {
  "Hello world"
}
```

```rust
fn function() -> Rc<String> {
  let s = Rc::new(String::from("Hello world"));
  Rc::clone(&s)
}
```

```rust
fn function(output: &mut String) {
  output.replace_range(.., "Hello world");
}
```

#### Not Enough Permissions

##### Problem

```rust
fn function(name: &Vec<String>) -> String {
  name.push(String::from("Esq."));
  let full = name.join(" ");
  full
}
```

##### Solutions

```rust
fn function(name: &Vec<String>) -> String {
  let mut name_clone = name.clone();
  name_clone.push(String::from("Esq."));
  let full = name_clone.join(" ");
  full
}
```

```rust
fn function(name: &Vec<String>) -> String {
  let mut full = name.join(" ");
  full.push_str(" Esq.");
  full
}
```

#### Aliasing and Mutating a Data Structure

##### Problem

```rust
fn function(dst: &mut Vec<String>, src: &[String]) {
  let largest: &String =
    dst.iter().max_by_key(|s| s.len()).unwrap();
  
  for s in src {
    if s.len() > largest.len() {
      dst.push(s.clone());
    }
  }
}
```

##### Solutions

```rust
fn function(dst: &mut Vec<String>, src: &[String]) {
  let largest: String =
    dst.iter().max_by_key(|s| s.len()).unwrap().clone();
  
  for s in src {
    if s.len() > largest.len() {
      dst.push(s.clone());
    }
  }
}
```

```rust
fn function(dst: &mut Vec<String>, src: &[String]) {
  let largest: &String =
    dst.iter().max_by_key(|s| s.len()).unwrap();
  
  let to_add: Vec<String> =
    src.iter().filter(|s| s.len() > largest.len()).cloned().collect();

  dst.extend(to_add);
}
```

```rust
fn function(dst: &mut Vec<String>, src: &[String]) {
  let largest_len: usize =
    dst.iter().max_by_key(|s| s.len()).unwrap().len();
  
  for s in src {
    if s.len() > largest_len {
      dst.push(s.clone());
    }
  }
}
```

#### Copying vs Moving Out of a Collection

##### Problem

```rust
let v: Vec<String> = vec![String::from("Hello world")];
let s_ref: &String = &v[0];
let s: String = *s_ref;
```

##### Solutions

```rust
let v: Vec<String> = vec![String::from("Hello world")];
let s_ref: &String = &v[0];
println!("{s_ref}!");
```

```rust
let v: Vec<String> = vec![String::from("Hello world")];
let mut s: String = v[0].clone();
s.push("!");
println!("{s}");
```

```rust
let v: Vec<String> = vec![String::from("Hello world")];
let mut s: String = v.remove(0);
s.push("!");
println!("{s}");
assert!(v.len() == 0);
```

#### Mutating Different Array Elements

##### Problem

```rust
let mut a = [0, 1, 2, 3];
let x = &mut a[1];
let y = &a[2];
*x += *y;
```

##### Solutions

```rust
let mut a = [0, 1, 2, 3];
let (a_l, a_r) = a.split_at_mut(2);
let x = &mut a_l[1];
let y = &a_r[0];
*x += *y;
```

```rust
let mut a = [0, 1, 2, 3];
let x = &mut a[1]; as *mut i32;
let y = &a[2] as *const i32;
unsafe { *x += *y; }
```

### 4.4 The Slice Type

#### String Slices

```rust
let s: &str = "Hello world";
```

```rust
let s = String::from("Hello world");

let hello: &str = &s[0..5];
let world: &str = &s[6..11];

let s2: &String = &s;
```

```rust
fn first_word(s: &String) -> &str {
  let bytes = s.as_bytes();

  for (i, &item) in bytes.iter().enumerate() {
    if item == b' ' {
      return &s[0..i];
    }
  }

  &s[..]
}
```

#### Range Syntax

```rust
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```

#### Other Slices

```rust
let a = [1, 2, 3, 4, 5];

let slice: &[i32] = &a[1..3];

assert_eq!(slice, &[2, 3]);
```

## 5. Using Structs to Structure Related Data

### 5.1 Defining and Instantiating Structs

```rust
struct User {
  email: String,
  username: String,
  signin_count: u64,
  active: bool,
}
```

```rust
fn main() {
  let mut user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    signin_count: 1,
    active: true,
  };

  user1.email = String.from("someuser1@example.com");

  let user2 = User {
    email: String::from("someuser2@example.com"),
    ..user1
  };
}
```

#### Field Init Shorthand

```rust
fn build_user(email: String, username: String) -> User {
  User {
    email,
    username,
    signin_count: 1,
    active: true,
  }
}
```

#### Tuple Structs

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
  let black = Color(0, 0, 0);
  let origin = Point(0, 0, 0);

  let Point(x, y, z) = origin;
}
```

#### Unit-Like Structs

```rust
struct AlwaysEqual;

fn main() {
  let subject = AlwaysEqual;
}
```

#### Borrowing Fields of a Struct

```rust
struct Point { x: i32, y: i32 };

let mut p = Point { x: 0, y: 0 };

// p:   R W O
// p.x: R W O
// p.y: R W O

let x = &mut p.x;

// p:   - - -
// p.x: - - -
// ...

*x += 1;

// p:   R W O
// p.x: R W O
// ...

println!("{}, {}", p.x, p.y);
```

### 5.2 An Example Program Using Structs

```rust
#[derive(Debug)]
struct Rectangle {
  width: u32,
  height: u32,
}

fn main() {
  let rect1 = Rectangle {
    width: 30,
    height: 50,
  };

  println!("rect1 is {rect1:?}");

  // rect1 is Rectangle { width: 30, height: 50 }

  println!("rect1 is {rect1:#?}");

  // rect1 is Rectangle {
  //   width: 30,
  //   height: 50,
  // }

  dbg!(&rect1);

  // &rect1 = Rectangle {
  //   width: 60,
  //   height: 50,
  // }
}
```

### 5.3 Methods

```rust
#[derive(Debug)]
struct Rectangle {
  width: u32,
  height: u32,
}

impl Rectangle {
  fn area(&self) -> u32 {
    self.width * self.height
  }
}

fn main() {
  let rect1 = Rectangle {
    width: 30,
    height: 50,
  };

  println!("The area of the rectangle is {} square pixels.", rect1.area());
}
```

#### Associated Functions

```rust
impl Rectangle {
  fn square(size: u32) -> Self {
    Self {
      width: size,
      height: size,
    }
  }
}

fn main() {
  let square = Rectangle::square(3);
}
```
