r2d2-diesel
===========

Provides [r2d2](https://github.com/sfackler/r2d2) support to allow connection pooling with Diesel.

Example
=======

This example creates a connection pool with default settings for a PostgreSQL database running on localhost, then creates a bunch of threads and acquires a connection from the pool for each thread.
An executable version of this example is in [examples/postgres.rs](examples/postgres.rs) which you can run with `cargo run --example postgres`.

```rust
extern crate diesel;
extern crate r2d2;
extern crate r2d2_diesel;

use std::thread;

use diesel::pg::PgConnection;
use r2d2_diesel::ConnectionManager;

fn main() {
    let config = r2d2::Config::default();
    let manager = ConnectionManager::<PgConnection>::new("postgres://localhost/");
    let pool = r2d2::Pool::new(config, manager).expect("Failed to create pool.");

    for _ in 0..10i32 {
        let pool = pool.clone();
        thread::spawn(move || {
            let connection = pool.get();

            assert!(connection.is_ok());
        });
    }
}
```
