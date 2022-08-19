---
title: "GSoC: Work Summary till now"
date: 2022-08-19T19:41:19+05:30
---

## Work Summary till Now ##
Last Updated: 19th August 2022


### Task Accomplishment till now ###

| Task        | Description   |  Task Status  | Time Devoted |
| :---:        |    :----:     |          :---: |   :---:|
| casbin-grpc | The casbin-grpc repository was not maintained in a while and had incomplete code. So I was supposed to maintain the repository.      | Given to another contributor, I took on to develop axum-casbin-auth. Devoted some time to understand the repository and what is required to do  | 1 week  |
| axum-casbin-auth   | Implement axum web framework(middleware) with casbin. Its a access control middleware for axum framework       |   Completed     | 2.5 Weeks |
| casbin-rs/examples   | The examples repository was not updated since, I had to update the dependencies and resolve the upcoming errors. Also, there is reimport of tokio which I need to resolve        | Completed      | 1.5 week |
| axum-middleware-example   | Develop a real-world example using axum-casbin-auth  | Completed      | 2.5 weeks |
| casbin-grpc   | Make casbin-grpc compile and run  | The another contributor withdrawn from the GSoC program and hence this task was reassigned to me. ONGOING      | 1.5 weeks(still on going) |

### PR count ###
#### casbin-rs/axum-casbin-auth #### 
1. [feat: Axum Middleware Integration](https://github.com/casbin-rs/axum-casbin-auth/pull/1): \
Task accomplished by this PR: \
[X] Axum middleware \
[X] Write tests for Axum middleware example \


Total Lines of code modified: **+792 -1** \
Total Commits: **9** \
Status: **MERGED** 

#### casbin-rs/examples #### 
1. [fix: Update dependencies, ensure build success and add axum-middleware-example](https://github.com/casbin-rs/examples/pull/72)
Task accomplished by this PR: \
[X] Update dependencies to their latest version. solved [#71](https://github.com/casbin-rs/examples/pull/71) \
[X] Both tokio and async-std features of casbin are enabled, disable one to ensure casbin compile successfully\
[X] Make `actix-fileadapter-rbac` compile successfully after updating the dependencies \
[X] Remove the actix component version conflict and successfully compile `actix-middleware-example` \
[X] Remove the deprecated calls after updating dependencies and successfully compile `ntex-fileadapter-acl` \
[X] Add `axum-middleware-example`
[X] Make clippy happy \

Total Lines of code modified: **+1,204 −76** \
Total Commits: **19** \
Status: **UNDER REVIEW** 

2. [fix: Update tokio](https://github.com/casbin-rs/actix-casbin/pull/25)


Total Lines of code modified: **+3 -3** \
Total Commits: **2** \
Status: **MERGED** 

3. [fix: corrected the readme file](https://github.com/casbin-rs/actix-casbin-auth/pull/36)

Total Lines of code modified: **+2 -4** \
Total Commits: **3** \
Status: **MERGED**

#### casbin-rs/grpc #### 
1. [fix: Make casbin-grpc run](https://github.com/casbin-rs/casbin-grpc/pull/9)
Task accomplished by this PR: \
[X] Update prost, tonic, make new proto.rs \
[X] Management apis, functions under `rpc_calls.rs` were not implemented \
[ ] Resolve enforcer(m, a) \
[ ] Resolve abac policy input logic and enforce(params) error \
[ ] Reference and borrow errors in rpc_calls.rs \

Total Lines of code modified: **+2,056 −3,797** \
Total Commits: **5** \
Status: **DRAFT**(Ongoing)

### Summary ###

Total PRs: **5** \
Total Commits: **38** \
Total Weeks: **9+** \
Total Lines of Code changed: **+4057 -3881**