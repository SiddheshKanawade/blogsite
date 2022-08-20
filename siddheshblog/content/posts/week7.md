---
title: "GSoC: Week 7[During midterm Evaluation]"
date: 2022-07-30T01:08:39+05:30
draft: false
---
## Duration ##
**25th July - 31st July 2022**

## Tasks: ##
🟩 Axum real-world example:
1. ✅ Define routes and api calls.
2. ✅ Draft logic for auth.rs(middleware)
3. ✅ Define structure of main.rs and enforce policies in backend

## Outcomes ##
**axum-middleware-example**\
[api, middleware, routes](https://github.com/casbin-rs/examples/pull/72/commits/f9bfe55fe38cbfc1f1583f18141c622811dedf68) \
[csv utils, main.rs, server setup](https://github.com/casbin-rs/examples/pull/72/commits/25ef1cdec4efb2fed7e60254c27dd9d4ea84830e)

## Workflow ##
### Define routes and api calls ###
Following functions were to be implemented to make the api calls:
1. signin() {}
2. register() {}
3. delete_user() {}
4. get_user() {}
5. get_all_user() {}

In this week, I was able to draft signin, get_user and get_all_user functions. In register and delete_user function I have to update the changes to both `casbin-rules` and `users` table in psql database. 

Following is the code snippet for `POST(api/auth/signin)` request:

api/user.rs

```rust
 pub async fn signin(
     Json(login_form): Json<LoginForm>,
     pool: Extension<Pool>,
 ) -> Response {
     // output should be a json response
     match user::signin(login_form, &pool) {
         Ok(token_res) => Json(ResponseBody::new(
             constants::MESSAGE_SIGNIN_SUCCESS,
             token_res,
         ))
         .into_response(),
         Err(_) => (
             StatusCode::INTERNAL_SERVER_ERROR,
             constants::MESSAGE_INTERNAL_SERVER_ERROR.to_string(),
         )
             .into_response(),
     }
 }
```
This matches the response with `signin` functions defined under `service/user.rs`. On failure, it returns the Error response as shown above. Unlike actix-web, where we get `web::Json<Data>`, we have to use `Json` extractor as shown above in axum.

service/user.rs

```rust
pub fn signin(
     login: LoginForm,
     Extension(pool): &Extension<Pool>,
 ) -> Result<TokenBodyResponse, ServiceError> {
     match User::signin(login, &pool.get().unwrap()) {
         Some(logged_user) => {
             match serde_json::from_value(
                 json!({ "token": UserToken::generate_token(logged_user), "token_type": "bearer" }),
             ) {
                 Ok(token_res) => Ok(token_res),
                 Err(_) => Err(ServiceError::new(
                     StatusCode::INTERNAL_SERVER_ERROR,
                     constants::MESSAGE_INTERNAL_SERVER_ERROR.to_string(),
                 )),
             }
         }
         None => Err(ServiceError::new(
             StatusCode::INTERNAL_SERVER_ERROR,
             constants::MESSAGE_SIGNIN_FAILED.to_string(),
         )),
     }
 }
```
`logged_user` is the LoginForm of the user, which contains user name, role, and login_session. For some logged user, we generate the token and in case there is no logged user, we simply return an error. In axum we need to extract the user data using `Extension(T): Extension<T>` unlike actix, where it provides the `web::Data(T)`.   

model/user.rs
```rust
pub fn signin(login: LoginForm, conn: &Connection) -> Option<LoginInfo> {
         if let Ok(user_to_verify) = users
             .filter(email.eq(&login.email))
             .get_result::<User>(conn)
         {
             if !user_to_verify.password.is_empty()
                 && compare_password(&login.password, &user_to_verify.password).unwrap()
             {
                 let login_session_str = User::generate_login_session();
                 if User::update_login_session_to_db(
                     &user_to_verify.email,
                     &login_session_str,
                     conn,
                 ) {
                     return Some(LoginInfo {
                         username: user_to_verify.username,
                         role: user_to_verify.role,
                         login_session: login_session_str,
                     });
                 }
             }
         }
         None
     }
```

Above function implements the main logic to signin the user, it calls helper function from utils to compare the password from database and updates the login_session to the database. 

Similar to the above, we have following api calls: \
POST(api/auth/register) \
DELETE(api/admin/{:id}) \
GET(api/user/{:id}) \
GET(api/users) \
PUT(api/admin/{:id})

### Draft logic for auth.rs(middleware) ###

```rust
         let mut authenticate_pass: bool = false;
         let mut authenticate_username: String = String::from("");

         // Bypass account routes
         let headers = req.headers_mut();
         headers.append(
             HeaderName::from_static("content-length"),
             HeaderValue::from_static("true"),
         );

         if Method::OPTIONS == *req.method() {
             authenticate_pass = true;
         } else {
             for ignore_route in constants::IGNORE_ROUTES.iter() {
                 if req.uri().path().starts_with(ignore_route) {
                     authenticate_pass = true;
                     break;
                 }
             }
             if !authenticate_pass {
                 if let Some(pool) = req.extensions().get::<Extension<Pool>>() {
                     info!("Connecting to database...");
                     if let Some(auth_header) =
                         req.headers().get(constants::AUTHORIZATION)
                     {
                         info!("Parsing authorization header...");
                         if let Ok(auth_str) = auth_header.to_str() {
                             if auth_str.starts_with("bearer")
                                 || auth_str.starts_with("Bearer")
                             {
                                 info!("Parsing token...");
                                 let token = auth_str[6..auth_str.len()].trim();
                                 if let Ok(token_data) =
                                     token_utils::decode_token(token.to_string())
                                 {
                                     info!("Decoding token...");
                                     if token_utils::validate_token(&token_data, pool)
                                         .is_ok()
                                     {
                                         info!("Valid token");
                                         authenticate_username = token_data.claims.user_name;
                                         authenticate_pass = true;
                                     } else {
                                         error!("Invalid token");
                                     }
                                 }
                             }
                         }
                     }
                 }
             }
         }
         if authenticate_pass {
             let vals = CasbinVals {
                 subject: authenticate_username,
                 domain: None,
             };
             req.extensions_mut().insert(vals);
             Box::pin(async move { 
                 Ok(inner.call(req).await?.map(body::boxed))
              })
         } else{
             Box::pin(async move{
                 Ok(Json(ResponseBody::new(constants::MESSAGE_TOKEN_MISSING, constants::EMPTY)).into_response())
             })

         }
```

Above is the logic implemented for the auth.rs, which is supposed to respond to the request in this case. It basically bypass some account routes which are present in Constants.rs and does token validation(token is extracted from request headers) using `validate_token` for the routes which couldn't be bypassed.

Ignore routes: 
```rust
pub const IGNORE_ROUTES: [&str; 4] = [
     "api/auth/register",
     "api/auth/signin",
     "api/users",
     "api/user",
 ];
```

### Define structure of main.rs and enforce policies in backend ###
The basic skeleton of the main.rs should look like:
```rust
fn main() {
    // EXTRACT DATA FROM .env FILE TO GET app_host, app_url, pool_size, app_port, app_database

    // READ .csv FILE AND ADD/ENFORCE THE POLICIES IN casbin-rules TABLE

    // IMPLEMENT CASBIN MIDDLEWARE

    // SET ROUTES USING axum::Router

    // HOST THE SERVER
} 
```

**Extract data from .env file**
```rust
 dotenv::dotenv().expect("Failed to read .env file, please add it");
     std::env::set_var("RUST_LOG", "actix_web=debug");
     env_logger::init();

     let app_host = env::var("APP_HOST").expect("APP_HOST must be set.");
     let app_port = env::var("APP_PORT").expect("APP_PORT must be set.");
     let app_url = format!("{}:{}", &app_host, &app_port);
     let database_url = std::env::var("DATABASE_URL").expect("DATABASE_URL must be set");
     let pool_size: u32 = std::env::var("POOL_SIZE")
         .ok()
         .and_then(|s| s.parse().ok())
         .unwrap_or(8);
```

`.env` file looks like
```.env
APP_HOST=127.0.0.1
APP_PORT=8080
DATABASE_URL=postgres://postgres:postgresAdmin@127.0.0.1:5432/casbintest
POOL_SIZE=8
HASH_ROUNDS=12 
```

**Enforce Policies**

There are preset policies defined in `.csv` file and they are needed to be added to the backend(using diesel adapter) before we start the operation of the application. We read them using the `csv utils` as vector and add them to casbin-rules using `enforcer.add_policy()`. It is important to classify the read vector into `p`(policy) and `g`(named_grouping_policy), hence a conditional is implemented which separates them.

Following code helps in understanding how policies are added to casbin-rules table

```rust
let preset_rules = load_csv(walk_csv("."));
     for mut policy in preset_rules {
         let ptype = policy.remove(0);
         if ptype.starts_with('p') {
             match clone_enforcer.write().await.add_policy(policy).await {
                 Ok(_) => info!("Present policies(p) added successfully"),
                 Err(err) => error!("Present policies(p) add error: {}", err.to_string()),
             };
             continue;
         } else if ptype.starts_with('g') {
             match clone_enforcer
                 .write()
                 .await
                 .add_named_grouping_policy(&ptype, policy)
                 .await
             {
                 Ok(_) => info!("Preset policies(g) added successfully"),
                 Err(err) => error!("Preset policies(g) add error: {}", err.to_string()),
             }
             continue;
         } else {
             unreachable!()
         }
     }
```
**Set Routes and host server**
```rust
let app = Router::new()
         .layer(Extension(pool.clone()))
         .layer(Extension(clone_enforcer))
         .route("/api/auth/register", post(user_api::register))
         .route("/api/auth/signin", post(user_api::signin))
         .route("/api/users", get(user_api::get_all_user))
         .route("/api/user/:id", get(user_api::get_user))
         .route("/api/admin/:id", put(user_api::update_user))
         .route("/api/admin/:id", delete(user_api::delete_user))
         .layer(casbin_middleware.clone())
         .layer(middleware::auth::AuthLayer);

     axum::Server::bind(&app_url.parse().unwrap())
         .serve(app.into_make_service())
         .await
         .unwrap();
```
Server is hosted at `127.0.0.1/8080`

## Next Week target ##
1. Add remaining api commands, make clippy happy, resolve any present error.
2. Start with casbin-openraft