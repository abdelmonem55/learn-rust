<style>
    .present p {
        text-align: left;

    }
</style>

# Learn rust with NEAR

[stacking pool factory](https://github.com/near/core-contracts/tree/master/staking-pool-factory): This contract deploys and automatically whitelists new staking pool contracts. It allows any user to create a new whitelisted staking pool.The staking pool factory contract packages the binary of the staking pool contract within its own binary. To create a new stakin

---

g pool a user should issue a function call transaction and attach the required minimum deposit. The entire deposit will be transferred to the newly created staking pool contract in order to cover the required storage.

---

## Variables and constant

Rust has variables like any other language such as primatives and non-primatives and there are some special features we will disscus them one by one.

----

### [primative types](https://doc.rust-lang.org/book/ch03-02-data-types.html)

```rs=59
 pub denominator: u32,
```

here we are defining a variable as a parameter of type 'u32'

----

### [const](https://doc.rust-lang.org/std/keyword.const.html)

```rs=11
const MIN_ATTACHED_BALANCE: Balance = 30_000_000_000_000_000_000_000_000;
```

Rust has compile time const that recommended to be used with values that used many times in addition to it gives readablity for the code, for example here we define a const as 'Balance'.

----

### [struct](https://doc.rust-lang.org/book/ch05-01-defining-structs.html)

```rs=40
pub struct StakingPoolFactory {
    /// Account ID of the staking pool whitelist contract.
    staking_pool_whitelist_account_id: AccountId,

    /// The account ID of the staking pools created.
    staking_pool_account_ids: UnorderedSet<AccountId>,
```

Rust doesn't have classes but instead struct to define a container for data like in these line of code we define a struct with it's variable

----

### [vector](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=284
 testing_env_with_promise_results(context.clone(), PromiseResult::Successful(vec![]));
```

vector is a dynamic list of homogeneous data stored in the heap, so here we are creating vector with no element with the help of macro `vec![]`

---

## [strings](https://doc.rust-lang.org/rust-by-example/std/str.html)

rust has many types that represents sequence of characters

----

### [String](https://doc.rust-lang.org/std/string/index.html)

```rs=139
 staking_pool_id: String,
```

String is a UTF-8 dynamic groupable list used to store sequence of chars, and we here we defining a type of String

----

### [&str](https://doc.rust-lang.org/std/primitive.str.html)

```rs=177
 b"new".to_vec(),
```

it is slice version of String so it reference to other type of String ,here we are haveing a string literal of type '&'static str' and this mean it reference to a string with static [lifetime](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html) which will live all program lifetime and call [to_string](https://doc.rust-lang.org/std/string/trait.ToString.html) function on it to convert it to owned string

---

## functions

like any other language you can define some reusable code and it is called function.

----

### function definition and calling

```rs=137
 pub fn create_staking_pool(
```

here we are defining a function with name 'create_staking_pool',with parameters and return type

---

## impl section, and static and instance functions

Rust is not object oriented language but you can define structs as data container and attach function to operate on this data, also you can do the same for enums.

----

### [impl](https://doc.rust-lang.org/std/keyword.impl.html)

```rs=62
impl RewardFeeFraction {
    pub fn assert_valid(&self) {
        assert_ne!(self.denominator, 0, "Denominator must be a positive number");
        assert!(
            self.numerator <= self.denominator,
            "The reward fee must be less or equal to 1"
        );
    }
}
```

here we start to define functions to manipulate the derived type

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=105
 pub fn new(staking_pool_whitelist_account_id: AccountId) -> Self {
```

inside impl block we can define a static function like this so it can be called from the type itself, here we are returning [Self](https://stackoverflow.com/questions/32304595/whats-the-difference-between-self-and-self) from the function so here we refere to the current type.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/) call

```rs=113
 staking_pool_account_ids: UnorderedSet::new(b"s".to_vec()),
```

here we call a static function 'new' in the type 'UnorderedSet' and this is widely used method to create a new instance because Rust doesn't have constractor.

----

### [instance function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=137
 pub fn create_staking_pool(
     &mut self,
     staking_pool_id: String,
     owner_id: AccountId,
     stake_public_key: Base58PublicKey,
     reward_fee_fraction: RewardFeeFraction,
 ) -> Promise {
```

inside impl block we can define instance function like this code defines function that takes parameters in addition to '& mut [self](https://doc.rust-lang.org/std/keyword.self.html)', rust introduce new concept called [mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html) so here we borrow mutable refrence for the current object so that the function can edit on it.

----

### instance function and immutable reference

```rs=119
 pub fn get_min_attached_balance(&self) -> U128 {
     MIN_ATTACHED_BALANCE.into()
 }
```

here we define an instance function with '&self' this means the function [borrows](https://doc.rust-lang.org/rust-by-example/scope/borrow.html) the current object for readonly like in first line we use it to get data from the current object

----

### instance function calling

```rs=253
 let mut context = VMContextBuilder::new()
     .current_account_id(account_factory())
     .predecessor_account_id(account_near())
```

here we are creating a variable and call chain of instance functions on it and this is because here we are returning same object from the function

----

### instance function calling

```rs=342
 .expect("No such request")
```

this function implemented for [Option](https://doc.rust-lang.org/std/option/) and [Result](https://doc.rust-lang.org/std/result/) type in rust in order to [unwrap](https://learning-rust.github.io/docs/e4.unwrap_and_expect.html) the inner value

---

## [ownership](https://doc.rust-lang.org/rust-by-example/scope/move.html) and [borrowing](https://doc.rust-lang.org/rust-by-example/scope/borrow.html)

rust has unique concept called ownership and borrowing so when you assign variables to anther so most of the time instead of copying, the ownership taken by the second variable, so we can use borrowing and other things so keep the origin variable

----

### [Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html) and ownership

```rs=172
 Promise::new(staking_pool_account_id.clone())
```

as we mention when we use a variable in passing to function or to assign anther variable so the ownership is transfered and the origin variable move and this exist with non-primative types unless they implement [Copy trait](https://doc.rust-lang.org/std/marker/trait.Copy.html), so here we use clone function to make a copy of data and so as not to be moved and reused after that

---

## [Generic types](https://doc.rust-lang.org/book/ch10-00-generics.html)

Every programming language has tools for effectively handling the duplication of concepts and write extensible agnostic

----

### [trait](https://doc.rust-lang.org/book/ch10-02-traits.html)

```rs=96
pub trait ExtWhitelist {
    fn add_staking_pool(&mut self, staking_pool_account_id: AccountId) -> bool;
}
```

trait define shared behaviour between implementors and it is consider as a contract, here we define a trait 'ExtSelf ' with it's abstracted functionality

----

### [trait](https://doc.rust-lang.org/book/ch10-02-traits.html) impl

```rs=48
impl Default for StakingPoolFactory {
    fn default() -> Self {
        env::panic(b"The contract should be initialized before usage")
    }
}
```

here we impl [Default](https://doc.rust-lang.org/std/default/trait.Default.html) trait for 'StakingPoolFactory ' type

---

## [testing in rust](https://doc.rust-lang.org/book/ch11-01-writing-tests.html)

Tests are Rust functions that verify that the non-test code is functioning in the expected manner.

----

### unit test

```rs=251
 #[test]
 fn test_create_staking_pool_success() {
```

so here we are writing a function and anntotate it as test like at first line

---

## [modules](https://doc.rust-lang.org/rust-by-example/mod.html) and [imports](https://doc.rust-lang.org/reference/items/use-declarations.html)

Rust structure it's code in modules so you can have mutliple files in your projects.

----

### importing from other crates

```rs=1
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::collections::UnorderedSet;
use near_sdk::json_types::{Base58PublicKey, U128};
use near_sdk::serde::{Deserialize, Serialize};
```

these lines import struct types from other crates that must be mentioned as a dependence in [Cargo.toml](https://doc.rust-lang.org/cargo/reference/manifest.html).

----

### modules

```rs=13
pub mod gas {
```

we can define new module in rust to structure our code, here we declaring a module

---

## [macros](https://doc.rust-lang.org/book/ch19-06-macros.html)

Macros are widely used in metaprogramming for generating code at compile time.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=38
#[near_bindgen]
#[derive(BorshDeserialize, BorshSerialize)]
pub struct StakingPoolFactory {
```

procedure macros allow creating syntax extentions like at first line we use [attribute](https://www.near-sdk.io/contract-structure/near-bindgen) for the struct to generate some code in order to validate near contract, at second line [derive-macro](https://doc.rust-lang.org/reference/attributes/derive.html) to generate implementation for these traits instead of making custom implementation for them.[BorshDeserialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshDeserialize.html), [BorshSerialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshSerialize.html) are used to convert to and from object and binary value but Serialize and Deserialize are used to convert to and from object and json value.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=136
 #[payable]
```

[payable](https://www.near-sdk.io/contract-interface/payable-methods) allows the method to accept token transfer together with the function call

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=55
#[derive(Serialize, Deserialize, Clone)]
#[serde(crate = "near_sdk::serde")]
```

used to select [serde](serde.rs) crate and so we give it a path to it here

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=293
 #[should_panic(expected = "Not enough attached deposit to complete staking pool creation")]
 fn test_create_staking_pool_not_enough_deposit() {
```

we attribute a [test function](https://doc.rust-lang.org/reference/attributes/testing.html) with this macro to say that it might panic with this message

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=35
#[global_allocator]
```

In a given program, the standard library has one 'global' [memory](https://doc.rust-lang.org/std/alloc/index.html) allocator that is used for example by Box<T>

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=84
#[ext_contract(ext_self)]
```

[helper macro](https://www.near-sdk.io/cross-contract/callbacks) that allows you to make cross-contract calls with the syntax

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=65
 assert!(
     self.numerator <= self.denominator,
     "The reward fee must be less or equal to 1"
 );
```

macro rules it is a macro like function but evaluated at compile time in addition to have ability to generate codes, this line call macro and as we see it is like function call but have '!' so here we call macro called [assert](https://doc.rust-lang.org/std/macro.assert.html) to assert some thing is true

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=175
 .deploy_contract(include_bytes!("../../staking-pool/res/staking_pool.wasm").to_vec())
```

here we are calling a macro called [include_bytes](https://doc.rust-lang.org/std/macro.include_bytes.html) to get data from specific path

