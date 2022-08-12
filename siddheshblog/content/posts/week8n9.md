---
title: "GSoC: Week 8 and 9"
date: 2022-08-05T01:14:22+05:30
draft: false
---
## Duration ##
**1st August - 14th August 2022**

## Tasks: ##
✅ Axum real-world example:
1. ✅ add register, delete and update api services
2. ✅ make clippy happy
3. ✅ Merge poem-todo
4. ✅ Ensure all checks pass in Github Actions

🟩 casbin-grpc:
1. ✅ Management apis
2. 🟩 Resolve error in enforcer.rs, adapter.rs
3. 🟩 Ensure all checks pass in Github Actions

## Outcomes ##
**axum-middleware-example**\
[register, update, delete services added](https://github.com/casbin-rs/examples/pull/72/commits/02200c50187a5be76eadde72ef38d4fe0ee4f3a6) \
[make clippy happy, remove unnecessary commands](https://github.com/casbin-rs/examples/pull/72/commits/dcdd94fee90a727417c54cb86704b655dc4b9e6c) \
[formatting, clippy happy in poem-todo](https://github.com/casbin-rs/examples/pull/72/commits/657b826311a6b92acbe47bfad1cb0ba50c9d115e) \
[normalize::TrailingSlash->TrailingSlash](https://github.com/casbin-rs/examples/pull/72/commits/90c6c75d6f60f823412ea500e1df28c67545f019)

**casbin-grpc**\
[management apis](https://github.com/casbin-rs/casbin-grpc/pull/7/commits/835e3236061b645a7dfe35966fdca0a700488d73)

## Workflow ##
### axum-middleware-example ###
1. **Add register, delete and update user api services:**

api/user.rs
```rust
pub async fn register(
     Json(reg_form): Json<AddUser>,
     pool: Extension<Pool>,
     Extension(enforcer): Extension<Arc<RwLock<CachedEnforcer>>>,
 ) -> Response {
     let user = reg_form;
     match user::register(user, &pool, enforcer).await {
         Ok(message) => {
             Json(ResponseBody::new(&message, constants::EMPTY)).into_response()
         }
         Err(_) => (
             StatusCode::INTERNAL_SERVER_ERROR,
             constants::MESSAGE_NEW_USER_ADD_PERMISSION_ERROR.to_string(),
         )
             .into_response(),
     }
 }
```

Above is the simple function which outputs the Json response if user::register executes without error. Here we wrap CachedEnforcer into Rwlock, since enforcer is not thread-safe.

service/user.rs
```rust
pub async fn register(
     user: AddUser,
     Extension(pool): &Extension<Pool>,
     enforcer: Arc<RwLock<CachedEnforcer>>,
 ) -> Result<String, ServiceError> {
     let username = user.clone().username;
     let email = user.clone().email;
     let clone_role = user.role.clone();
     let g_policies = vec![username.clone(), clone_role.to_string()];

     // CHECK IF USER IS ALREADY PRESENT
     if User::get_user_by_email(&email, &pool.get().unwrap()).is_err() {
         match enforcer
             .write()
             .await
             .add_named_grouping_policy(&"g", g_policies)
             .await
         {
             Ok(_) => info!("Preset policies(p) add successfully"),
             Err(err) => error!("Preset policies(g) add error: {}", err.to_string()),
         };

         match User::register(user, &pool.get().unwrap()) {
             Ok(message) => Ok(message),
             Err(message) => Err(ServiceError::new(
                 StatusCode::INTERNAL_SERVER_ERROR,
                 message,
             )),
         }
     } else {
         Err(ServiceError::new(
             StatusCode::INTERNAL_SERVER_ERROR,
             constants::MESSAGE_NEW_USER_ADD_PERMISSION_ERROR.to_string(),
         ))
     }
 }
```
Here, first its checked whether the user is already present or not. If user isn't already present, then update the grouping policy in casbin-rules and then update the user in users table. Here grouping policy is present as vector and enforced using CachedEnforcer wrapped inside Rwlock.

model/user.rs
```rust
pub fn register(user: AddUser, conn: &Connection) -> Result<String, String> {
         if Self::get_user_by_email(&user.email, conn).is_err() {
             let hashed_pwd = hash_password(&user.password).unwrap();
             let user_upd = AddUser {
                 password: hashed_pwd,
                 ..user
             };
             diesel::insert_into(users)
                 .values(&user_upd)
                 .execute(conn)
                 .map_err(|e| e.to_string())?;

             Ok(constants::MESSAGE_SIGNUP_SUCCESS.to_string())
         } else {
             Err(format!("User {} is already registered", &user.username))
         }
     }
```
In order to update user information to users table, above function is called in register at service/user.rs. If user is not present, then hash the password using `hash_password()` and pass this parameter along with others to users table using `diesel`.

2. **Make clippy happy, formatting**
There were multiple unused lifetimes in the `actix-middleware-example` like Insertable, AsChangeset, etc. These were necessary to insert values to tables located at database and hence removing them won't be a choice. Hence used `#![allow(clippy::extra_unused_lifetimes)]` to ignore them. 

`cargo clippy --fix` => Automatically applied clippy suggestions to some extent. 
`cargo fmt ` => To check formatting of the code.

Till now `poem-todo` was merged in master branch of the repository and hence I had to pull the changes, resolve the minor conflict and make clippy happy in it.

3. **Ensure all checks pass in Github Actions:**
```rust
 use actix_web::middleware::normalize::TrailingSlash;
   |                            ^^^^^^^^^ private module
   |
note: the module `normalize` is defined here
  --> /home/runner/.cargo/registry/src/github.com-1ecc6299db9ec823/actix-web-4.1.0/src/middleware/mod.rs:10:1
   |
10 | mod normalize;
   | ^^^^^^^^^^^^^^


For more information about this error, try `rustc --explain E0603`.
error: could not compile `actix-middleware-example` due to previous error
```

After the successfull completion of the real life example using axum-middleware, I was yet to figure out how to resolve this error. Also, this error couldn't be reproduced in ubuntu 20.04(due to compiler, not sure though). After some research I figured out that this was due to the upgrade in actix-web version and was removed by updating `normalize::TrailingSlash->TrailingSlash`.

Output: 
![Image alt](/img/week8/examples.png)

### casbin-grpc ###

### casbin-openraft ###

## PLEASE TAKE A NOTE ##
From 2nd August 2022, my college has started and hence most of my contributions would be in last five days of the week since I have more free time there. I can still devote around 25-30 hours per week.

## Next Week target ##