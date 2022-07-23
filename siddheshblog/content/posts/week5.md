---
title: "GSoC: Week 5"
date: 2022-07-15T22:43:56+05:30
---

## Tasks: ##
✅ casbin-example respository multiple depencies version conflict resolve \
🟩 Real-life example for Axum middleware

## Outcomes ##
1. **casbin-example:** \
[tokio and async-std conflict resolved](https://github.com/casbin-rs/examples/pull/72/commits/565baf177a3961ff49f5b8135ad8d211418e7ac0) \
[fix: Update tokio](https://github.com/casbin-rs/actix-casbin/pull/25) 

2. **axum-middleware-example:** \
[filestructure and initial db implementation](https://github.com/casbin-rs/examples/pull/72/commits/bcdc9ddd0f32af2b7c36e0267ffb45749e585239)

## Workflow ##

### casbin-example ###
Tokio reimport error(after resolving error in actix-casbin and actix-casbin-auth):
```error
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
After ensuring the build compiles in the individual directories in the casbin-rs/examples, the next task in the line was to ensure that the main workspace compile successfully. From the error console it was not clear what was exactly causing the error. I figured out that the error was due to the simultanous usage of `tokio` and `async-std` feature of casbin with the help of tokio community. Initially I tried to enable async-std feature and disable tokio since there was reimport of tokio. But since actix uses tokio, there was still import of the tokio. Hence I had to enable `tokio-runtime` and disable `async-std-runtimes`. 

Following error was encountered after resolving the above conflict:

![Image alt](/img/week3n4/actixdep.png)

`actix-casbin` had deprecated version of tokio which was causing different versions of tokio and hence had to update it. Opened a PR to do so.


### axum-middleware-example ###

The next task in list was to develop a real-life example using `axum-casbin-auth`. I had `actix-middleware-example` developed by Eason Chai for reference. I did some research and found following example good enough to be reproduced in Rust. 

[Authorization in Golang Projects using Casbin](https://medium.com/wesionary-team/authorization-in-golang-projects-using-casbin-f8fad744dae5)

I will aim to make a example capable of doing simple CRUD operations on backend. 

1. Backend: `Postgres`
2. `diesel` to access backend.

**What will app do:**  I aim to have a basic application based on RBAC model. I will group users into two groups: `doctor` and `patient`. As obvious it sounds, doctor will have priveledged permissions than patient. 

The `rbac.conf` looks like: 
```conf
[request_definition]
 r = sub, obj, act

 [policy_definition]
 p = sub, obj, act

 [role_definition]
 g = _, _

 [policy_effect]
 e = some(where (p.eft == allow))

 [matchers]
 m = g(r.sub, p.sub) && r.obj == p.obj && r.act == p.act 
```

The application will match the permissions from frontend to backend and will respond with appropriate response. The identification would be done via email and password.

The user model looks like:
```rust
#[derive(Debug, Serialize, Deserialize, Queryable)]
 #[diesel(table_name = users)]
 pub struct User {
     pub id: i32,
     pub username: String,
     pub email: String,
     pub password: String,
     pub role: String,
 }
```
In this week I aimed at creating a Database, a basic filestructure, and add code templates. Following are few examples: 

bcrypt.rs:

```rust
use crate::constants;
 use bcrypt::{hash, verify, DEFAULT_COST};
 use std::env;

 pub fn hash_password() {}

 pub fn compare_password() {}

 pub fn generate_token() {}

 pub fn validate_token() {}
```
auth.rs:

```rust
impl<S, ReqBody, ResBody> Service<Request<ReqBody>> for AuthMiddleware<S>
 where
     S: Service<Request<ReqBody>, Response = Response<ResBody>, Error = Infallible>
         + Clone
         + Send
         + 'static,
     S::Future: Send + 'static,
     ReqBody: Send + 'static,
     Infallible: From<<S as Service<Request<ReqBody>>>::Error>,
     ResBody: HttpBody<Data = Bytes> + Send + 'static,
     ResBody::Error: Into<BoxError>,
 {
     type Response = S::Response;
     type Error = S::Error;
     // `BoxFuture` is a type alias for `Pin<Box<dyn Future + Send + 'a>>`
     type Future = BoxFuture<'static, Result<Self::Response, Self::Error>>;

     fn poll_ready(&mut self, cx: &mut Context<'_>) -> Poll<Result<(), Self::Error>> {
         self.inner.poll_ready(cx)
     }

     fn call(&mut self, mut req: Request<ReqBody>) -> Self::Future {
         let not_ready_inner = self.inner.clone();
         let mut inner = std::mem::replace(&mut self.inner, not_ready_inner);

         // IMPLEMENT LOGIC HERE
         Box::pin(async move {
             inner.call(req).await
         })
     }
 }
```
After setting up the user model run the `diesel migration generate users` command to generate the `migrations` file for our database. Setup the database structure: 

```sql
CREATE TABLE users
 (
     id         SERIAL PRIMARY KEY,
     username   VARCHAR(32)  NOT NULL,
     email      VARCHAR(100) NOT NULL,
     password   VARCHAR(200) NOT NULL,
     role       VARCHAR(32)  NOT NULL
 );

 INSERT INTO users
 VALUES (0, 'John', 'john@john.com', 'imjohn', 'doctor');
 INSERT INTO users
 VALUES (1, 'Sam', 'sam@sam.com', 'imsam', 'patient');
```
`diesel migration run` to generate the schema.rs file:

```rust
table! {
     users (id) {
         id -> Int4,
         username -> Varchar,
         email -> Varchar,
         password -> Varchar,
         role -> Varchar,
     }
 }
```
Next we will connect database with out project, for this add following `.env` file:

```env
APP_HOST=127.0.0.1
APP_PORT=8080
DATABASE_URL=postgres://postgres:postgresAdmin@127.0.0.1:5432/casbintest
POOL_SIZE=8
HASH_ROUNDS=12
```
Connect database with project:

```rust
fn establish_connection() -> PgConnection {
     dotenv().ok();

     let database_url = env::var("DATABASE_URL").expect("DATABASE_URL must be set");
     PgConnection::establish(&database_url)
         .expect(&format!("Error connecting to {}", database_url))
 }
```

Now load the data from the project to database. Will do this in main.rs. After successful setup of the database, we get the following output: 

![Image alt](/img/week5/database.png)