# A cache manager for the publicsuffix crate

[![Build Status](https://travis-ci.org/rushmorem/psl.svg?branch=master)](https://travis-ci.org/rushmorem/psl) [![Latest Version](https://img.shields.io/crates/v/psl.svg)](https://crates.io/crates/psl) [![Docs](https://docs.rs/psl/badge.svg)](https://docs.rs/psl)

The [publicsuffix](https://github.com/rushmorem/publicsuffix) crate is a low level crate that allows you to optionally use Mozilla's Public Suffix List to parse domain names and email addresses. It provides methods for fetching the list but it doesn't try to handle caching for you. This is where this crate comes in. It downloads the list at a specific interval (once every week by default). When it downloads a new version it saves it to disk. After that, it won't try to download again until the saved one has expired.

## Quick Start

Add this crate to your dependencies:-

```toml
[dependencies]
psl = "0.0.2"
publicsuffix = "1.3"

# The following crates are optional but recommended. Without logging,
# you won't know if updates start failing in future.
slog = "1.2"
slog-term = "1.3"
```

Call `init` from your `main.rs`:-

```rust
extern crate psl;
extern crate publicsuffix;
extern crate slog;
extern crate slog_term;

use publicsuffix::LIST_URL;
use slog::{Logger, DrainExt};

fn main() {
  // Initialise the list
  psl::init(LIST_URL, None).unwrap();

  // Set up logging
  let log = Logger::root(slog_term::streamer().build().fuse(), o!("version" => env!("CARGO_PKG_VERSION")));
  psl::set_logger(&log);
}

fn anywhere() -> Result<()> {
  // To get access to an instance of the cached list call `psl::get()`. For example...
  let domain = psl::get().parse_domain("example.com")?;
}
```
