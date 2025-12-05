# wasm-timer

[![Crates.io](https://img.shields.io/crates/v/wasm-timer.svg)](https://crates.io/crates/wasm-timer)
[![Docs.rs](https://docs.rs/wasm-timer/badge.svg)](https://docs.rs/wasm-timer)
[![License](https://img.shields.io/crates/l/wasm-timer.svg)](https://github.com/tomaka/wasm-timer/blob/master/LICENSE)

`wasm-timer` is an abstraction over `std::time::Instant` and `futures-timer` that works on both WASM and non-WASM targets.

- **Non-WASM targets**: it re-exports types from `std::time` and `futures-timer`.
- **WASM targets**: it uses `web-sys` to access the Performance API (`performance.now()`) for time measurement and `setTimeout`/`setInterval` for scheduling.

## Installation

Add this to your `Cargo.toml`:

```toml
[dependencies]
wasm-timer = "0.2.5"
```

## Usage

The crate provides `Instant`, `Delay`, `Interval`, and extension traits for timeouts.

### Measuring Time

```rust
use wasm_timer::Instant;

let start = Instant::now();
// ... do some work ...
let duration = start.elapsed();
println!("Time elapsed: {:?}", duration);
```

### Delay (Sleep)

```rust
use wasm_timer::Delay;
use std::time::Duration;

async fn example() {
    let duration = Duration::from_millis(500);
    Delay::new(duration).await.unwrap();
    println!("Waited 500ms!");
}
```

### Timeout

You can use the `TryFutureExt` trait to add timeouts to futures.

```rust
use wasm_timer::TryFutureExt;
use std::time::Duration;

async fn long_operation() -> Result<(), std::io::Error> {
    // ...
    Ok(())
}

async fn example() {
    let future = long_operation();
    let result = future.timeout(Duration::from_secs(1)).await;

    match result {
        Ok(val) => println!("Operation completed: {:?}", val),
        Err(e) => println!("Operation timed out or failed: {:?}", e),
    }
}
```
