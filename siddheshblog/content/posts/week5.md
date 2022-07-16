---
title: "GSoC: Week 5"
date: 2022-07-11T15:35:40+05:30
---

### Tasks: ###
✅ casbin-example respository multiple depencies version conflict resolve \
✅ Reallife example for Axum middleware

### Workflow ###

Error Compilation

Tokio reimport error(after resolving error in actix-casbin and actix-casbin-auth):
```rust
error[E0252]: the name `IoError` is defined multiple times
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:20:10
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:20:28
   |
13 |     io::{BufReader, Error as IoError, ErrorKind},
   |                                       --------- previous import of the type `ErrorKind` here
...
20 |     io::{Error as IoError, ErrorKind},
   |                            ^^^^^^^^^ `ErrorKind` reimported here
   |
   = note: `ErrorKind` must be defined only once in the type namespace of this module

error[E0252]: the name `Path` is defined multiple times
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:21:5
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:25:5
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:26:42
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:13:11
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:13:23
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:16:36
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:16:47
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:16:65
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/config.rs:20:5
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
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/model/default_model.rs:20:5
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

error[E0599]: no method named `next_line` found for struct `async_std::io::Lines` in the current scope
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:66:38
   |
66 |         while let Some(line) = lines.next_line().await? {
   |                                      ^^^^^^^^^ method not found in `async_std::io::Lines<async_std::io::BufReader<async_std::fs::File>>`

error[E0599]: no method named `next_line` found for struct `async_std::io::Lines` in the current scope
  --> /home/siddhesh/.cargo/registry/src/github.com-1ecc6299db9ec823/casbin-2.0.9/src/adapter/file_adapter.rs:91:38
   |
91 |         while let Some(line) = lines.next_line().await? {
   |                                      ^^^^^^^^^ method not found in `async_std::io::Lines<async_std::io::BufReader<async_std::fs::File>>`

Some errors have detailed explanations: E0252, E0599.
For more information about an error, try `rustc --explain E0252`.
error: could not compile `casbin` due to 14 previous errors
```