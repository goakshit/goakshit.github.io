---
title: "Learning Rust Series: Code organization"
slug: "rr-rust-1"
tags: ["rust", "cargo"]
date: 2021-11-01T10:18:22+05:30
draft: false
---

# Learning Rust Series: Code organization

> **Prerequisites**: I am hoping you have Cargo and Rust installed. If not, head to the <u>[Guide](https://doc.rust-lang.org/cargo/getting-started/installation.html).

In this series, I will be explaining things that I come across on my Rust learning journey. This is the first installment of the Learning Rust Series which will cover code organization in rust, which is different from other languages.

Code organization is an interesting topic. Whenever I start learning a new language, this is the first thing that I look at. What are the best practices? What are the do's and don't? I try to get into best practise right away since it's easy to get the hang of it at the beginning than unlearning it later.

Let's get started.

### Initialize the code.
To initialize the code in rust, run `cargo init`. It will create following file:
- `Cargo.toml`: It's a config file for cargo which hold information about the project and dependencies.
- `src/main.rs`: Entry point for the project.

To run the code, run `cargo run`. It will execute the `main.rs` file.

#### Let's look at an example.
We will create a new directory under `./src` called `greeting` which will have couple of files (`mod.rs`, `greet.rs`). The directory structure will look like as shown below:
```
project
│   Cargo.toml
└─── /src
    │   main.rs
    └─── /greeting
        │   mod.rs
        │   greet.rs
```

We want to run a function `hello` in `greet.rs` file. To do that, this `hello` function should be visible and accessible from the `main.rs` file. We need to add the following line to `greet.rs` file:

```rust
// src/greeting/greet.rs
pub fn hello() {
    println!("Hello, world!");
}

```
The `pub` keyword is used to make the function visible from outside the module. Now that we have a public `hello` function, we need to tell `mod.rs` about the `greet.rs` file. To do that, we need to add the following line to `mod.rs` file:
```rust
// src/greeting/mod.rs
pub mod greet;
```

Now we have a public module named `greet`, which is visible from the `main.rs` file. To run the `hello` function, we need to import the `greet` module. Let's look at that:
```rust
// src/main.rs
mod greeter;

fn main() {
    greeter::greet::hello();
} 
```

In the line-1, we are importing `greeter` module. As discussed above, this module exposes the greet's `hello` function. Inside main function, we are calling the `hello` function which prints out `Hello, world!` on the console.

We can segregate similar logic into modules in similar way. Say, `greeter` directory can have another file called `good_bye.rs`, which should be included in the `mod.rs` file and then can be called in a similar fashion in the `main.rs` file.

>I hope this article gave you some idea about code organization in Rust. 