---
title: "GSoC: Week 3 and 4"
date: 2022-07-10T15:35:40+05:30
---

### Tasks: ###
✅ casbin-rs/example respository maintainence \
✅ Add CI to axum middleware \
✅ Add tests and examples to axum-casbin-auth

## Workflow ##
### Casbin-rs/examples ###
The repository had the following set of errors when ran for the first time:

```error
error[E0252]: the name `IoError` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:20:10
   |
13 |     io::{BufReader, Error as IoError, ErrorKind},
   |                     ---------------- previous import of the type `IoError` here
...
20 |     io::{Error as IoError, ErrorKind},
   |          ^^^^^^^^^^^^^^^^--
   |          |
   |          `IoError` reimported here
   |          help: remove unnecessary import
   |
   = note: `IoError` must be defined only once in the type namespace of this module

error[E0252]: the name `ErrorKind` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:20:28
   |
13 |     io::{BufReader, Error as IoError, ErrorKind},
   |                                       --------- previous import of the type `ErrorKind` here
...
20 |     io::{Error as IoError, ErrorKind},
   |                            ^^^^^^^^^ `ErrorKind` reimported here
   |
   = note: `ErrorKind` must be defined only once in the type namespace of this module

error[E0252]: the name `Path` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:21:5
   |
14 |     path::Path,
   |     ---------- previous import of the type `Path` here
...
21 |     path::Path,
   |     ^^^^^^^^^^ `Path` reimported here
   |
   = note: `Path` must be defined only once in the type namespace of this module
help: you can use `as` to change the binding name of the import
   |
21 |     path::Path as OtherPath,
   |     ~~~~~~~~~~~~~~~~~~~~~~~

error[E0252]: the name `File` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:25:5
   |
11 |     fs::File,
   |     -------- previous import of the type `File` here
...
25 |     fs::File,
   |     ^^^^^^^^ `File` reimported here
   |
   = note: `File` must be defined only once in the type namespace of this module
help: you can use `as` to change the binding name of the import
   |
25 |     fs::File as OtherFile,
   |     ~~~~~~~~~~~~~~~~~~~~~

error[E0252]: the name `BufReader` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:26:42
   |
13 |     io::{BufReader, Error as IoError, ErrorKind},
   |          --------- previous import of the type `BufReader` here
...
26 |     io::{AsyncBufReadExt, AsyncWriteExt, BufReader},
   |                                          ^^^^^^^^^ `BufReader` reimported here
   |
   = note: `BufReader` must be defined only once in the type namespace of this module
help: you can use `as` to change the binding name of the import
   |
26 |     io::{AsyncBufReadExt, AsyncWriteExt, BufReader as OtherBufReader},
   |                                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~

error[E0252]: the name `Cursor` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:13:11
   |
6  |     io::{BufReader, Cursor, Error as IoError, ErrorKind},
   |                     ------ previous import of the type `Cursor` here
...
13 | use std::{io::Cursor, path::Path};
   |           ^^^^^^^^^^ `Cursor` reimported here
   |
   = note: `Cursor` must be defined only once in the type namespace of this module
help: you can use `as` to change the binding name of the import
   |
13 | use std::{io::Cursor as OtherCursor, path::Path};
   |           ~~~~~~~~~~~~~~~~~~~~~~~~~

error[E0252]: the name `Path` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:13:23
   |
10 | use async_std::{fs::File, path::Path};
   |                           ---------- previous import of the type `Path` here
...
13 | use std::{io::Cursor, path::Path};
   |                       ^^^^^^^^^^ `Path` reimported here
   |
   = note: `Path` must be defined only once in the type namespace of this module
help: you can use `as` to change the binding name of the import
   |
13 | use std::{io::Cursor, path::Path as OtherPath};
   |                       ~~~~~~~~~~~~~~~~~~~~~~~

error[E0252]: the name `BufReader` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:16:36
   |
6  |     io::{BufReader, Cursor, Error as IoError, ErrorKind},
   |          --------- previous import of the type `BufReader` here
...
16 |     AsyncBufReadExt, AsyncReadExt, BufReader, Error as IoError, ErrorKind,
   |                                    ^^^^^^^^^ `BufReader` reimported here
   |
   = note: `BufReader` must be defined only once in the type namespace of this module
help: you can use `as` to change the binding name of the import
   |
16 |     AsyncBufReadExt, AsyncReadExt, BufReader as OtherBufReader, Error as IoError, ErrorKind,
   |                                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~

error[E0252]: the name `IoError` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:16:47
   |
6  |     io::{BufReader, Cursor, Error as IoError, ErrorKind},
   |                             ---------------- previous import of the type `IoError` here
...
16 |     AsyncBufReadExt, AsyncReadExt, BufReader, Error as IoError, ErrorKind,
   |                                               ^^^^^^^^^^^^^^^^--
   |                                               |
   |                                               `IoError` reimported here
   |                                               help: remove unnecessary import
   |
   = note: `IoError` must be defined only once in the type namespace of this module

error[E0252]: the name `ErrorKind` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:16:65
   |
6  |     io::{BufReader, Cursor, Error as IoError, ErrorKind},
   |                                               --------- previous import of the type `ErrorKind` here
...
16 |     AsyncBufReadExt, AsyncReadExt, BufReader, Error as IoError, ErrorKind,
   |                                                                 ^^^^^^^^^-
   |                                                                 |
   |                                                                 `ErrorKind` reimported here
   |                                                                 help: remove unnecessary import
   |
   = note: `ErrorKind` must be defined only once in the type namespace of this module

error[E0252]: the name `File` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:20:5
   |
10 | use async_std::{fs::File, path::Path};
   |                 -------- previous import of the type `File` here
...
20 | use tokio::fs::File;
   |     ^^^^^^^^^^^^^^^ `File` reimported here
   |
   = note: `File` must be defined only once in the type namespace of this module
help: you can use `as` to change the binding name of the import
   |
20 | use tokio::fs::File as OtherFile;
   |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~

error[E0252]: the name `Path` is defined multiple times
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/model/default_model.rs:20:5
   |
17 | use async_std::path::Path;
   |     --------------------- previous import of the type `Path` here
...
20 | use std::path::Path;
   |     ^^^^^^^^^^^^^^^ `Path` reimported here
   |
   = note: `Path` must be defined only once in the type namespace of this module
help: you can use `as` to change the binding name of the import
   |
20 | use std::path::Path as OtherPath;
   |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   Compiling actix-codec v0.3.0
error[E0599]: no method named `next_line` found for struct `async_std::io::Lines` in the current scope
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:66:38
   |
66 |         while let Some(line) = lines.next_line().await? {
   |                                      ^^^^^^^^^ method not found in `async_std::io::Lines<async_std::io::BufReader<async_std::fs::File>>`

error[E0599]: no method named `next_line` found for struct `async_std::io::Lines` in the current scope
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:91:38
   |
91 |         while let Some(line) = lines.next_line().await? {
   |                                      ^^^^^^^^^ method not found in `async_std::io::Lines<async_std::io::BufReader<async_std::fs::File>>`

   Compiling h2 v0.2.7
Some errors have detailed explanations: E0252, E0599.
For more information about an error, try `rustc --explain E0252`.
error: could not compile `casbin` due to 14 previous errors
```

After analyzing it was found that both `tokio` and `async-std` features of casbin were enabled which should be the case. I decided compile the individual examples and then compile the rest project.

#### actix-fileadapter-rbac and actix-pgsql-simple
There was no error thrown after updating the dependencies and compiled successfully

#### ntex-fileadapter-acl
All the depedencies were to the mark, but it threw following error after running `cargo build`

```error
error[E0432]: unresolved import `crate::rt::Signal`
 --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/ntex-0.5.24/src/server/builder.rs:8:24
  |
8 | use crate::rt::{spawn, Signal, System};
  |                        ^^^^^^ no `Signal` in `rt`

error[E0432]: unresolved import `crate::rt::tcp_connect`
 --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/ntex-0.5.24/src/server/test.rs:6:17
  |
6 | use crate::rt::{tcp_connect, System};
  |                 ^^^^^^^^^^^ no `tcp_connect` in `rt`

error[E0425]: cannot find function `signal` in module `crate::rt`
   --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/ntex-0.5.24/src/server/builder.rs:480:38
    |
480 |         if let Some(rx) = crate::rt::signal() {
    |                                      ^^^^^^ not found in `crate::rt`

error[E0425]: cannot find function `from_tcp_stream` in module `rt`
   --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/ntex-0.5.24/src/server/socket.rs:148:40
    |
148 |             Stream::Tcp(stream) => rt::from_tcp_stream(stream),
    |                                        ^^^^^^^^^^^^^^^ not found in `rt`
    |
help: consider importing one of these items
    |
1   | use crate::connect::net::from_tcp_stream;
    |
1   | use ntex_connect::net::from_tcp_stream;
    |
help: if you import `from_tcp_stream`, refer to it directly
    |
148 -             Stream::Tcp(stream) => rt::from_tcp_stream(stream),
148 +             Stream::Tcp(stream) => from_tcp_stream(stream),
    | 

error[E0425]: cannot find function `from_unix_stream` in module `rt`
   --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/ntex-0.5.24/src/server/socket.rs:150:40
    |
150 |             Stream::Uds(stream) => rt::from_unix_stream(stream),
    |                                        ^^^^^^^^^^^^^^^^ not found in `rt`
    |
help: consider importing one of these items
    |
1   | use crate::connect::net::from_unix_stream;
    |
1   | use ntex_connect::net::from_unix_stream;
    |
help: if you import `from_unix_stream`, refer to it directly
    |
150 -             Stream::Uds(stream) => rt::from_unix_stream(stream),
150 +             Stream::Uds(stream) => from_unix_stream(stream),
    | 

error[E0412]: cannot find type `Signal` in module `crate::rt`
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/ntex-0.5.24/src/server/mod.rs:67:23
   |
67 |     Signal(crate::rt::Signal),
   |                       ^^^^^^ not found in `crate::rt`
   |
help: there is an enum variant `crate::server::ServerCommand::Signal`; try using the variant's enum
   |
67 |     Signal(crate::server::ServerCommand),
   |            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~

error[E0412]: cannot find type `Signal` in module `crate::rt`
  --> /Users/macbookair/.cargo/registry/src/github.com-1ecc6299db9ec823/ntex-0.5.24/src/server/mod.rs:91:38
   |
91 |     fn signal(&self, sig: crate::rt::Signal) {
   |                                      ^^^^^^ not found in `crate::rt`
   |
help: there is an enum variant `crate::server::ServerCommand::Signal`; try using the variant's enum
   |
91 |     fn signal(&self, sig: crate::server::ServerCommand) {
   |                           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some errors have detailed explanations: E0412, E0425, E0432.
For more information about an error, try `rustc --explain E0412`.
error: could not compile `ntex` due to 7 previous errors
```

After discussing with the ntex community it was found that incorrect features of `ntex` used. Entire conversation can be found [here](https://github.com/ntex-rs/ntex/discussions/127) `ntex = "0.5"` was changed to `ntex = { version = "0.5", features = ["tokio"] }`. Even after updating this, following error was thrown:
```error
error[E0599]: no method named `app_data` found for struct `App` in the current scope
  --> ntex-fileadapter-acl/src/main.rs:46:14
   |
46 |             .app_data(e.clone()) // <- create app with shared state
   |              ^^^^^^^^ method not found in `App<ntex::service::Identity, web::app::Filter<DefaultError>>`

```
This is due to the version upgrade of ntex, resolve can be found [here](https://github.com/casbin-rs/examples/pull/72/files#diff-63e997272b56167db79065780451a949630a1e40df2a762b297774fd2112769dL8)

#### actix-middleware-example ####

### CI for Axum middleware ###
Adding CI to enable github actions was the next task after adding the core code for the Axum middleware. I had `actix-casbin-auth` for reference and following commands were included in the CI.yml

* `cargo build --verbose`
* `cargo clippy -- -D warnings`
* `cargo fmt --all -- --check`

I have added `Swatinem/rust-cache@v1` in order to build from cache. More information about it can be found [here](https://github.com/Swatinem/rust-cache)

Following result was observed

![Image alt](/img/week3n4/citest.png)

### Add tests and examples to axum-casbin-auth ###
Used `actix-casbin-auth` as reference. It had three tests:
* test_middleware.rs: test basic middleware function
* test_middleware_domain.rs: test middleware function with domain
* test_set_enforcer.rs: test initializing middleware using `set_enforcer()`

We first implement a FakeAuthLayer which is used to insert subject and domain in `CasbinVals`.

```rust
fn call(&self, req: ServiceRequest) -> Self::Future {
        let svc = self.service.clone();

        Box::pin(async move {
            let vals = CasbinVals {
                subject: String::from("alice"),
                domain: Option::from(String::from("domain1")),
            };
            req.extensions_mut().insert(vals);
            svc.call(req).await
        })
```
Endpoints in FakeAuthLayer and Casbin are wrapped using `layer` in Axum.
```rust
let app = Router::new()
        .route("/pen/1", get(handler))
        .route("/pen/2", get(handler))
        .route("/book/:id", get(handler))
        .layer(casbin_middleware)
        .layer(FakeAuthLayer);
```
Axum web framework don't have its own TestClient unlike Poem and actix-web. Hence I had to use `axum_test_helper::TestClient`. 
Axum test looks like:
```rust
use axum::{response::Response, routing::get, BoxError, Router};
use axum_test_helper::TestClient;
//...

let app = Router::new()
        .route("/pen/1", get(handler))
        .route("/pen/2", get(handler))
        .route("/book/:id", get(handler))
        .layer(casbin_middleware) // casbin_middleware preceeds FakeAuthLayer
        .layer(FakeAuthLayer);

    let client = TestClient::new(app);

    let resp_pen_1 = client.get("/pen/1").send().await;
    assert_eq!(resp_pen_1.status(), StatusCode::OK);

    let resp_book = client.get("/book/2").send().await;
    assert_eq!(resp_book.status(), StatusCode::OK);

    let resp_pen_2 = client.get("/pen/2").send().await;
    assert_eq!(resp_pen_2.status(), StatusCode::FORBIDDEN);
}
```
Github repository of axum-casbin-auth can be found [here](https://github.com/casbin-rs/axum-casbin-auth)

### Target for Next week ###
1. casbin-rs/examples: resolve error due to simultaneous use of `tokio` and `async-std` \
2. Establish a real world example using axum-casbin-auth.