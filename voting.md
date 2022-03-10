<style>
    .present p {
        text-align: left;

    }
</style>

# Learn rust with NEAR (voting)

[voting](https://github.com/near/core-contracts/tree/master/voting): The purpose of this contract is solely for validators to vote on whether to unlock token transfer. Validators can call vote to vote for yes with the amount of stake they wish to put on the vote. If there are more than 2/3 of the stake at any given moment voting for yes, the voting is done. After

---

 the voting is finished, no one can further modify the contract.

---

## Variables and constant

Rust has variables like any other language such as primatives and non-primatives and there are some special features we will disscus them one by one.

----

### [primative types](https://doc.rust-lang.org/book/ch03-02-data-types.html)

```rs=236
 assert_eq!(contract.votes.len() as u128, i + 1);
```

here we are getting length as usize and cast it to u128 so we use keyword 'as'for casting.

----

### [static](https://doc.rust-lang.org/reference/items/static-items.html)

```rs=7
static ALLOC: near_sdk::wee_alloc::WeeAlloc = near_sdk::wee_alloc::WeeAlloc::INIT;
```

Rust has run time const that recommended to be used with values that used many times in addition to it gives readablity for the code,also note that using static favour const when we have data with big size.

----

### [struct](https://doc.rust-lang.org/book/ch05-01-defining-structs.html)

```rs=15
pub struct VotingContract {
    /// How much each validator votes
    votes: HashMap<AccountId, Balance>,
    /// Total voted balance so far.
    total_voted_stake: Balance,
    /// When the voting ended. `None` means the poll is still open.
    result: Option<WrappedTimestamp>,
    /// Epoch height when the contract is touched last time.
    last_epoch_height: EpochHeight,
}
```

Rust doesn't have classes but instead struct to define a container for data like in these line of code we define a struct it's memebers.

----

### [vector](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=151
 signer_account_pk: vec![0, 1, 2],
```

vector is a dynamic list of homogeneous data stored in the heap, so here we are creating vector with elements with the help of macro `vec![]`

----

### [hashmap](https://doc.rust-lang.org/std/collections/struct.HashMap.html)

```rs=17
 votes: HashMap<AccountId, Balance>,
```

hashmap is non-primative buildin type in the standard library used to store key and value pairs values.

----

### [iterators](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=125
 .iter()
 .map(|(account_id, stake)| (account_id.clone(), (*stake).into()))
 .collect()
```

it is a trait used to loop over lists like lists, here we are using function [iter](https://doc.rust-lang.org/stable/std/iter/) to get iterable with borrowed data list then we use [map](https://doc.rust-lang.org/nightly/std/iter/struct.Map.html) to map every element in the list and finally we use [collect](https://doc.rust-lang.org/nightly/std/iter/trait.Iterator.html#method.collect) to get an a collection.

----

### [tuple](https://doc.rust-lang.org/rust-by-example/primitives/tuples.html)

```rs=113
 pub fn get_total_voted_stake(&self) -> (U128, U128) {
```

it is a collection of variables with different types, here we are returning a tuple of u128 and u128  from function 

---

## [strings](https://doc.rust-lang.org/rust-by-example/std/str.html)

rust has many types that represents sequence of characters

----

### [String](https://doc.rust-lang.org/std/string/index.html)

```rs=149
 current_account_id: "alice_near".to_string(),
```

String is a UTF-8 dynamic groupable list used to store sequence of chars, and we here we call [to_string](https://doc.rust-lang.org/std/string/trait.ToString.html) to get String from the variable of type '&str' we will check it next page.

----

### [&str](https://doc.rust-lang.org/std/primitive.str.html)

```rs=171
 let context = get_context("bob.near".to_string());
```

it is slice version of String so it reference to other type of String ,here we are haveing a string literal of type '&'static str' and this mean it reference to a string with static [lifetime](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html) which will live all program lifetime.

---

## functions

like any other language you can define some reusable code and it is called function.

----

### function definition and calling

```rs=140
 fn get_context(predecessor_account_id: AccountId) -> VMContext {
     get_context_with_epoch_height(predecessor_account_id, 0)
 }
```

here we are defining a function with name 'get_context', and not that last statement we omit the ';' to return data from the function implicitly.

---

## impl section, and static and instance functions

Rust is not object oriented language but you can define structs as data container and attach function to operate on this data, also you can do the same for enums.

----

### [impl](https://doc.rust-lang.org/std/keyword.impl.html)

```rs=33
impl VotingContract {
```

here we start to define functions to manipulate the derived type.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=35
 pub fn new() -> Self {
     assert!(!env::state_exists(), "The contract is already initialized");
     VotingContract {
         votes: HashMap::new(),
         total_voted_stake: 0,
         result: None,
         last_epoch_height: 0,
     }
 }
```

inside impl block we can define a static function like this so it can be called from the type itself, here we are returning [Self](https://stackoverflow.com/questions/32304595/whats-the-difference-between-self-and-self) from the function so here we refere to the current type.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/) call

```rs=190
 let mut contract = VotingContract::new();
```

here we call a static function 'new' in the type 'VotingContract' and this is widely used method to create a new instance because Rust doesn't have constractor.

----

### [instance function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=46
 pub fn ping(&mut self) {
```

inside impl block we can define instance function like this code defines function that takes parameters in addition to '& mut [self](https://doc.rust-lang.org/std/keyword.self.html)', rust introduce new concept called [mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html) so here we borrow mutable refrence for the current object so that the function can edit on it.

----

### instance function and immutable reference

```rs=106
 pub fn get_result(&self) -> Option<WrappedTimestamp> {
```

here we define an instance function with '&self' this means the function [borrows](https://doc.rust-lang.org/rust-by-example/scope/borrow.html) the current object for readonly like in first line we use it to get data from the current object.

---

## [ownership](https://doc.rust-lang.org/rust-by-example/scope/move.html) and [borrowing](https://doc.rust-lang.org/rust-by-example/scope/borrow.html)

rust has unique concept called ownership and borrowing so when you assign variables to anther so most of the time instead of copying, the ownership taken by the second variable, so we can use borrowing and other things so keep the origin variable.

----

### [Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html) and ownership

```rs=206
 validators.clone()
```

as we mention when we use a variable in passing to function or to assign anther variable so the ownership is transfered and the origin variable move and this exist with non-primative types unless they implement [Copy trait](https://doc.rust-lang.org/std/marker/trait.Copy.html), so here we use clone function to make a copy of data and so as not to be moved and reused after that.

---

## [Generic types](https://doc.rust-lang.org/book/ch10-00-generics.html)

Every programming language has tools for effectively handling the duplication of concepts and write extensible agnostic code.

----

### [trait](https://doc.rust-lang.org/book/ch10-02-traits.html) impl

```rs=26
impl Default for VotingContract {
    fn default() -> Self {
        env::panic(b"Voting contract should be initialized before usage")
    }
}
```

here we impl [Default](https://doc.rust-lang.org/std/default/trait.Default.html) trait for 'LockupContract' type.

---

## [testing in rust](https://doc.rust-lang.org/book/ch11-01-writing-tests.html)

Tests are Rust functions that verify that the non-test code is functioning in the expected manner.

----

### unit test

```rs=196
 #[test]
 fn test_voting_simple() {
```

so here we are writing a function and anntotate it as test.

----

### unit test

```rs=192
 assert!(contract.result.is_some());
```

here we use assert built in macro to assure that condtion is true.

----

### unit test

```rs=189
 testing_env!(context, Default::default(), Default::default(), validators);
```

this [test macro](https://www.near-sdk.io/testing/unit-tests) provided by near and used to test in non-WASM target.

---

## [modules](https://doc.rust-lang.org/rust-by-example/mod.html) and [imports](https://doc.rust-lang.org/reference/items/use-declarations.html)

Rust structure it's code in modules so you can have mutliple files in your projects.

----

### importing from other crates

```rs=1
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::json_types::{U128, U64};
use near_sdk::{env, near_bindgen, AccountId, Balance, EpochHeight};
use std::collections::HashMap;
```

these lines import struct types from other crates that must be mentioned as a dependence in [Cargo.toml](https://doc.rust-lang.org/cargo/reference/manifest.html).

----

### modules

```rs=133
mod tests {
```

we can define new module in rust to structure our code, here we declaring a module.

---

## [macros](https://doc.rust-lang.org/book/ch19-06-macros.html)

Macros are widely used in metaprogramming for generating code at compile time.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=13
#[near_bindgen]
#[derive(BorshDeserialize, BorshSerialize)]
pub struct VotingContract {
```

procedure macros allow creating syntax extentions like at first line we use [attribute](https://www.near-sdk.io/contract-structure/near-bindgen) for the struct to generate some code in order to validate near contract, at line second [derive-macro](https://doc.rust-lang.org/reference/attributes/derive.html) to generate implementation for these traits instead of making custom implementation for them.[BorshDeserialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshDeserialize.html), [BorshSerialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshSerialize.html) are used to convert to and from object and binary value but Serialize and Deserialize are used to convert to and from object and json value.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=34
 #[init]
 pub fn new() -> Self {
```

here we use attribute the function to define it as an [initialization](https://docs.near.org/docs/develop/contracts/rust/near-sdk-rs) function.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=131
#[cfg(not(target_arch = "wasm32"))]
#[cfg(test)]
```

here we are using macro with cfg to make compile time switch and make this code only work with test and with target other than WASM.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=169
 #[should_panic(expected = "is not a validator")]
```

we attribute the test function might panic with this message.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=6
#[global_allocator]
static ALLOC: near_sdk::wee_alloc::WeeAlloc = near_sdk::wee_alloc::WeeAlloc::INIT;
```

in a given program, the standard library has one 'global' [memory](https://doc.rust-lang.org/std/alloc/index.html) allocator that is used for example by Box<T> and Vec<T>, and here this attribute allows configuring the choice of global allocator.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=236
 assert_eq!(contract.votes.len() as u128, i + 1);
```

macro rules it is a macro like function but evaluated at compile time in addition to have ability to generate codes, this line call macro and as we see it is like function call but have '!' so here we call macro called 'assert_eq' to assure right equals left.

