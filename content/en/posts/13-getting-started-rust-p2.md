---
title: "Learning Rust Series: GREP Lite"
date: 2021-12-05T17:50:05+05:30
slug: "rr-rust-2"
draft: false
toc: false
tags:
  - cargo
  - rust
---

In this post, I will be creating a simple grep-lite cli application that reads lines from a file or stdin and prints the lines that contain the search string. Since it's a cli application, I need to pass arguments to this app.

Let's get started.
> I would be using couple of external dependencies to make this work. `clap` and `regex`.

### Initialize the code.
To initialize the code in rust, run `cargo init`. It will create following file:
- `Cargo.toml`: It's a config file for cargo which hold information about the project and dependencies.
- `src/main.rs`: Entry point for the project.

To run the code, run `cargo run`. It will execute the `main.rs` file.

### Adding dependencies to the .toml file
```toml
[package]
name = "grep-lite"
version = "0.1.0"
edition = "2021"

[dependencies]
regex = "1"
clap = "2" 
```
> `clap` is a CLI Framework for Rust and `regex` is a crate for regular expressions.

Now that we have dependencies added, we can start writing code in the `main.rs` file.

Let's start by bringing the crates into the scope.
```rust 
// main.rs
use std::fs::File;              // LINE-1
use std::io;                    // LINE-2
use std::io::BufReader;         // LINE-3
use std::io::prelude::*;        // LINE-4
use regex::Regex;               // LINE-5
use clap::{App, Arg};           // LINE-6
```
Let's discuss the lines 1-6 briefly.
- Line 1: `use std::fs::File;`: This is a standard library crate for file operations.
- Line 2: `use std::io;`: This is a standard library crate for input/output operations.
- Line 3: `use std::io::BufReader;`: This is a standard library crate for buffered input/output operations.
- Line 4: `use std::io::prelude::*;`: This is a standard library crate for input/output operations.
- Line 5: `use regex::Regex;`: This is a crate for regular expressions.
- Line 6: `use clap::{App, Arg};`: We are bringing the `App` and `Arg` structs into scope from the `clap` crate which are needed for command line applications.

Now that we have the crates in scope, let's look at the `main` function.
```rust
fn main() {
  let args = App::new("grep-lite")
    .version("0.1.0")
    .about("searches for the given pattern")
    .arg(Arg::with_name("pattern")
        .help("the pattern to search for")
        .takes_value(true)
        .required(true)
    ).
    arg(Arg::with_name("input")
        .help("the input to search from")
        .takes_value(true)
        .required(false)
    ).get_matches();

  // MORE CODE FOLLOWS
}
```

In the code snippet above, we are using the `App` struct from the `clap` crate and creating a new application with name `grep-lite`, version `0.1.0` and adding two arguments. 

Each argument has different properties like name, help message, whether it takes value or not, and whether it is required or not.

`get_matches()` is a method of the `App` struct which parses the command line arguments and returns a `clap::ArgMatches` struct.

Now that we have the arguments parsed, we have to get value from the arguments.
```rust
let pattern = args.value_of("pattern").unwrap();        // Line 1
let input = args.value_of("input").unwrap_or("stdin");  // Line 2
let re = Regex::new(pattern).unwrap();                  // Line 3
```

The above code is used to get value from the arguments and assign it to the variables `pattern` and `input`. Line 3 initialises the regex with pattern passed, and check for None value.

`unwrap()` is a method of the `Option` struct which unwraps the value from the `Option` struct and panics if the value is `None`.

`unwrap_or()` is similar to `unwrap()` but returns the default value if the value is `None`. So, if the value of input is None, it will return the default value `stdin`.

Moving on,

```rust
if input == "stdin" {                     // Line 1
  let stdin = io::stdin();                // Line 2
  let reader = stdin.lock();              // Line 3
  process_lines(reader, re);              // Line 4
} else {                                  // Line 5
  let f = File::open(input).unwrap();     // Line 6
  let reader = BufReader::new(f);         // Line 7
  process_lines(reader, re);              // Line 8
}
```

In the code snippet above, we check for the value of input string. If the value is `stdin`, we read input from standard input(stdin) shown in line 1-4. If input is passed, we assume it's a filepath and try to open the file and read from it as shown in line 6-8. See the implementation of process_lines function below.

```rust
fn process_lines<T: BufRead + Sized>(reader: T, re: Regex) {
  for line in reader.lines() {                      // Line 1
    let line_ = line.unwrap();                      // Line 2
      match re.find(&line_) {                       // Line 3
        Some(_) => println!("{}", line_),           // Line 4
        None => (),                                 // Line 5
      }
  }
}
```

`process_lines` is a generic function that takes couple of params: reader (T) and re (Regex). This function can be called as long as `BufRead` & `Sized` trait are implemented. Since `reader` argument implements BufRead, it can be called when data is read from file or is given as input from stdin. 

- Line 1: `BufRead` trait exposes a `.lines()` that returns an iterator over the lines.
- Line 2: We check for error, panics if the line is None.
- Line 3: We try to match pattern in this line
- Line 4: If pattern is found, print the line.
- Line 5: If pattern is not found, do nothing

That's about it. We have all the functionality in place. You can find the entire code [here](https://github.com/goakshit/samwise/blob/master/grep-lite/src/main.rs).

Let test it by running the following command:
`cargo run <PATTERN>`, ie, `cargo run hello` followed by the text in which it should be found. You can refer the image below.

![](/img/13a-rust-lite-exec.png)

>Thats all for this article. I hope you got some idea about using crates, regex, and creating cli apps  in Rust. 