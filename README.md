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

## 6. Enums and Pattern Matching

### 6.1 Defining an Enum

```rust
struct Ipv4Addr {
  // ...
}

struct Ipv6Addr {
  // ...
}

enum IpAddr {
  V4(Ipv4Addr),
  V6(Ipv6Addr)
}
```

```rust
enum Message {
  Quit,
  Write(String),
  Move { x: i32, y: i32 },
  ChangeColor(i32, i32, i32),
}

impl Message {
  fn call(&self) {
    // ...
  }
}

fn main() {
  let m = Message::Write(String::from("hello"));
  m.call();
}
```

#### The Option Enum

```rust
enum Option<T> {
  None,
  Some(T),
}
```

### 6.3 The Match Control Flow Construct

```rust
enum Coin {
  Penny,
  Nickel,
  Dime,
  Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
  match coin {
    Coin::Penny => 1,
    Coin::Nickel => 5,
    Coin::Dime => 10,
    Coin::Quarter => 25,
  }
}
```

#### The Option Match Pattern

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
  match x {
    None => None,
    Some(i) => Some(i + 1),
  }
}

fn main() {
  let five = plus_one(Some(4));
  let none = plus_one(None);
}
```

#### Catch-All Patterns

```rust
match dice_roll() {
  1 => do_a(),
  2 => do_b(),
  _ => (),
}
```

### 6.3 Consise Control Flow with if let and let else

#### `if let`

```rust
let mut count = 0;

if let Coin::Quarter(state) = coin {
  println!("State quarter from {state:?}!");
} else {
  count += 1;
}
```

#### `let else`

```rust
fn describe_state_quarter(coin: Coin) -> Option<String> {
  let Coin::Quarter = coin else {
    return None;
  };

  if state.existed_in(1900) {
    Some(format!("{state:?} is pretty old."));
  } else {
    Some(format!("{state:?} is relatively new."));
  }
}
```

## 7. Packages, Crates and Modules

### 7.1 Packages and Crates

#### Create Package

```shell
cargo new my-project
```

```shell
cargo new my-project --lib
```

### 7.2 Control Scope and Privacy with Modules

#### Crate Root

- `src/lib.rs` (library crate)
- `src/main.rs` (binary crate)

#### Modules

```rust
// main.rs
mod garden; // in src/garden.rs or src/garden/mod.rs
```

#### Submodules

```rust
// src/garden.rs
mod vegetables; // in src/garden/vegetables.rs or src/garden/vegetables/mod.rs
```

#### Example Structure

```
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs
```

#### Example Code

```rust
// main.rs
use crate::garden::vegetables::Aspargus;

pub mod garden; // includes src/garden.rs

fn main() {
    let plant = Aspargus {};
}
```

```rust
// src/garden.rs
pub mod vegetables; // includes src/garden/vegetables.rs
```

```rust
// src/garden/vegetables.rs
#[derive(Debug)]
pub struct Aspargus {}
```

### 7.3 Paths for Referring to an Item in the Module Tree

```rust
mod foo {
    mod bar {
        fn baz() {}
    }
}

pub fn qux() {
    crate::foo::bar::baz(); // absolute path
    foo::bar::baz();        // relative path
}
```

#### Starting Relative Paths with `super`

```rust
fn foo() {}

mod bar {
    fn baz() {
        super::foo();
    }
}
```

#### Making Structs and Enums Public

```rust
pub struct Breakfast {
    pub toast: String,  // public
    fruit: String,      // private
}
```

```rust
pub enum Appetizer {
    Soup,
    Salad
}
```

### 7.4 Bringing Paths into Scope with the `use` Keyword

```rust
mod foo {
    pub mod bar {
        pub fn baz() {}
    }
}

use crate::foo::bar;

pub fn qux() {
    bar::baz();
}
```

#### Providing New Names with the `as` Keyword

```rust
use std::fmt::Result as FmtResult;
use std::io::Result as IoResult;
```

#### Re-Exporting with `pub use`

```rust
mod foo {
    pub mod bar {
        pub fn baz() {}
    }
}

pub use crate::foo::bar;

pub fn qux() {
    bar::baz();
}
```

#### Using External Packages

```toml
# Cargo.tml
rand = "0.8.5"
```

```rust
use rand::Rng;

fn main() {
    let secret_number = rand::thread_rng().gen_range(1..=100)
}
```

#### Using Nested Paths to Clean Up `use` Lists

```rust
// from
use std::cmp::Ordering;
use std::io;

// to
use std::{cmp::Ordering, io};
```

```rust
// from
use std::io;
use std::io::Write;

// to
use std::io::{self, Write};
```

#### Importing Items with the Glob Operator

```rust
use std::collections::*;
```

## 8. Common Collections

TODO

## 9. Error Handling

TODO

## 10. Generic Types, Traits and Lifetimes

TODO

## 11. Writing Automated Tests

TODO

## 12. An I/O Project: Building a Command Line Program

TODO

## 13. Functional Language Features

TODO

## 14. More about Cargo and Crates.io

TODO

## 15. Smart Pointers

### 15.1 Using `Box<T>` to Point to Data on the Heap

#### Usages

- When you have a type whose size can’t be known at compile time, and you want to use a value of that type in a context that requires an exact size
- When you have a large amount of data, and you want to transfer ownership but ensure that the data won’t be copied when you do so
- When you want to own a value, and you care only that it’s a type that implements a particular trait rather than being of a specific type

#### Storing Data on the Heap

```rust
let b = Box::new(5);
println!("b = {b}");
```

#### Enabling Recursive Types with Boxes

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}
```

```rust
let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))))
```

### 15.2 Treating Smart Pointers like Regular References

#### Using `Box<T>` like a Reference

```rust
let x = 5;
let y = Box::new(x);

assert_eq!(5, x);
assert_eq!(5, *y);
```

#### Implementing the `Deref` Trait

```rust
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}

impl<T> Deref for MyBox<T> {
    type Target = T;
    
    fn deref(&self) -> &Self::Target {
        &self.0
    }
}
```

### 15.3 Running Code on Cleanup with the `Drop` Trait

```rust
struct CustomSmartPointer {
    data: String
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping CustomSmartPointer with data `{}`", self.data)
    }
}
```

#### Automatic Cleanup

```rust
fn main() {
    let ptr = CustomSmartPointer {
        data: String::from("foo")
    };
}
```

#### Manual Cleanup

```rust
fn main() {
    let ptr = CustomSmartPointer {
        data: String::from("foo")
    };
    drop(ptr);
}
```

### 15.4 `Rc<T>`, the Reference-Counted Smart Pointer

```rust
enum List {
    Cons(i32, Rc<List>),
    Nil,
}

fn main() {
    let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
    let b = Cons(3, Rc::clone(&a));
    let c = Cons(4, Rc::clone(&a));
}
```

### 15.4 `RefCell<T>` and the Interior Mutability Pattern

```rust
#[derive(Debug)]
enum List {
    Cons(Rc<RefCell<i32>>, Rc<List>),
    Nil
}
```

```rust
let val = Rc::new(RefCell::new(5));

let a = Rc::new(Cons(Rc::clone(&val), Rc::new(Nil)));

let b = Cons(Rc::new(RefCell::new(3)), Rc::clone(&a));
let c = Cons(Rc::new(RefCell::new(4)), Rc::clone(&a));

*val.borrow_mut() += 10;

println!("a after = {a:?}"); // a after = Cons(RefCell { value: 15 }, Nil)
println!("b after = {b:?}"); // b after = Cons(RefCell { value: 3 }, Cons(RefCell { value: 15 }, Nil))
println!("c after = {c:?}"); // c after = Cons(RefCell { value: 4 }, Cons(RefCell { value: 15 }, Nil))
```

### 15.5 Reference Cycles can Leak Memory

#### Preventing Reference Cycles using `Weak<T>`

```rust
#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}
```

```rust
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());

    let branch = Rc::new(Node {
        value: 5,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![Rc::clone(&leaf)]),
    });

    *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
}
```
