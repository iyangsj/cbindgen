# `cbindgen`

This project can be used to generate C bindings for Rust code. It is currently being developed to support creating bindings for [WebRender](https://github.com/servo/webrender/).

## Features

  * Builds bindings for a crate, its mods, its dependent crates, and their mods
  * Only the necessary types for exposed functions are given bindings
  * Can specify directives for controlling some aspects of binding
  * Generic structs can be exposed using `type IntFoo = Foo<i32>;`

## Use

### Command line

`cbindgen crate/ crate/bindings.h`

See `cbindgen --help` for more options.

### `build.rs`

`cbindgen` can also be used in build scripts. How this fits into compiling the native code depends on your project.

Here's an example build.rs script:
```rust
extern crate cbindgen;

use cbindgen::{Config, Library};

fn main() {
    let config = Config::from_file("bindconf.json");

    Library::load("../build-script", &config)
        .generate().unwrap()
        .write_to_file("bindings.h");
}

```

## Examples

See `examples/` for some examples of rust source that can be handled.

## How it works

1. All the structs, enums, type aliases, and functions that are representable in C are gathered
2. A dependency graph is built using the extern "C" functions as roots
    * This removes unneeded types from the bindings and sorts the structs that depend on each other
3. Some code generation is done to specialize generics that are specified as type aliases
3. The items are printed in dependency order in C syntax

## Future work

1. More customizable formatting and custom configs
2. Extensible system for specifying in source directives
3. Better support for types with fully specified names
4. Add a validation step
5. ...