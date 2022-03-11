<style>
    .present p {
        text-align: left;

    }
</style>

# Learn rust with NEAR (test-token)

contract: [test-token](https://github.com/ref-finance/ref-contracts/blob/main/test-token/src/lib.rs) 

---

## Variables

Rust has variables like any other language such as primatives and non-primatives and there are some special features we will disscus them one by one.

----

### [primative types](https://doc.rust-lang.org/book/ch03-02-data-types.html)

```rs=290
 transfers_enabled: bool,
```

here we are defining a variable as a parameter of type 'bool'.

----

### [struct](https://doc.rust-lang.org/book/ch05-01-defining-structs.html)

```rs=13
pub struct Contract {
    token: FungibleToken,
}
```

Rust doesn't have classes but instead struct to define a container for data like in these line of code we define a struct with variable 'token'.

---

## impl section, and static and instance functions

Rust is not object oriented language but you can define structs as data container and attach function to operate on this data, also you can do the same for enums.

----

### [impl](https://doc.rust-lang.org/std/keyword.impl.html)

```rs=18
impl Contract {
```

here we start to define functions to manipulate the derived type.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=20
 pub fn new() -> Self {
     Self {
         token: FungibleToken::new(b"t".to_vec()),
     }
 }
```

inside impl block we can define a static function like this so it can be called from the type itself, here we are returning [Self](https://stackoverflow.com/questions/32304595/whats-the-difference-between-self-and-self) from the function so here we refere to the current type.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/) call

```rs=57
 let mut context = VMContextBuilder::new();
```

here we call a static function 'new' in the type 'VMContextBuilder' and this is widely used method to create a new instance because Rust doesn't have constractor.

----

### [instance function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=32
 pub fn burn(&mut self, account_id: ValidAccountId, amount: U128) {
     self.token
         .internal_withdraw(account_id.as_ref(), amount.into());
 }
```

inside impl block we can define instance function like this code defines function that takes parameters in addition to '& mut [self](https://doc.rust-lang.org/std/keyword.self.html)', rust introduce new concept called [mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html) so here we borrow mutable refrence for the current object so that the function can edit on it.

----

### instance function and immutable reference

```rs=43
 fn ft_metadata(&self) -> FungibleTokenMetadata {
     unimplemented!()
 }
```

here we define an instance function with '&self' this means the function [borrows](https://doc.rust-lang.org/rust-by-example/scope/borrow.html) the current object for readonly like in first line we use it to get data from the current object.

----

### instance function calling

```rs=27
 self.token.internal_register_account(account_id.as_ref());
```

here we are calling an instance function to get data on 'token' variable.

---

## [ownership](https://doc.rust-lang.org/rust-by-example/scope/move.html) and [borrowing](https://doc.rust-lang.org/rust-by-example/scope/borrow.html)

rust has unique concept called ownership and borrowing so when you assign variables to anther so most of the time instead of copying, the ownership taken by the second variable, so we can use borrowing and other things so keep the origin variable.

----

### passing variable to function and ownership

```rs=33
 self.token
 .internal_withdraw(account_id.as_ref(), amount.into());
```

as we mention when we use a variable in passing to function or to assign anther variable so the ownership is transfered and the origin variable move and this exist with non-primative types unless they implement [Copy trait](https://doc.rust-lang.org/std/marker/trait.Copy.html), so instead of a copy of the variable passed, the variable will be moved.

---

## [Generic types](https://doc.rust-lang.org/book/ch10-00-generics.html)

Every programming language has tools for effectively handling the duplication of concepts and write extensible agnostic.

----

### [trait](https://doc.rust-lang.org/book/ch10-02-traits.html) impl

```rs=12
#[derive(BorshSerialize, BorshDeserialize, PanicOnDefault)]
```

here we are making automotic implementation for these traits using macro derive and they all we will check them next

---

## [testing in rust](https://doc.rust-lang.org/book/ch11-01-writing-tests.html)

tests are rust functions that verify that the non-test code is functioning in the expected manner.

----

### unit test

```rs=55
 #[test]
 fn test_basics() {
```

so here we are writing a function and anntotate it as test like first line

----

### unit test function

```rs=64
 assert_eq!(contract.ft_balance_of(accounts(0)), 1_000_000.into());
```

here we are using macro called 'assert_eq' to assert that the right equals left other wise the test will fail

---

## [modules](https://doc.rust-lang.org/rust-by-example/mod.html) and [imports](https://doc.rust-lang.org/reference/items/use-declarations.html)

Rust structure it's code in modules so you can have mutliple files in your projects.

----

### importing from other crates

```rs=4
use near_contract_standards::fungible_token::FungibleToken;
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::json_types::{ValidAccountId, U128};
use near_sdk::{env, near_bindgen, AccountId, PanicOnDefault, PromiseOrValue};
```

these lines import struct types from other crates that must be mentioned as a dependence in [Cargo.toml](https://doc.rust-lang.org/cargo/reference/manifest.html).

----

### modules

```rs=48
#[cfg(test)]
mod tests {
```

we can define new module in rust to structure our code, here we declaring a module at second line ,and at second line we annotate that it is a test module.

---

## [macros](https://doc.rust-lang.org/book/ch19-06-macros.html)

Macros are widely used in metaprogramming for generating code at compile time.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=11
#[near_bindgen]
#[derive(BorshSerialize, BorshDeserialize, PanicOnDefault)]
```

procedure macros allow creating syntax extentions like at first line we use [attribute](https://www.near-sdk.io/contract-structure/near-bindgen) for the struct to generate some code in order to validate near contract, at second line [derive-macro](https://doc.rust-lang.org/reference/attributes/derive.html) to generate implementation for these traits instead of making custom implementation for them.[BorshDeserialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshDeserialize.html), [BorshSerialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshSerialize.html) are used to convert to and from object and binary value but Serialize and Deserialize are used to convert to and from object and json value.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=19
 #[init]
```

here we use attribute the function to define it as an [initialization](https://docs.near.org/docs/develop/contracts/rust/near-sdk-rs) function.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=48
#[cfg(test)]
```

it a macro to make a compile time switch according a condition and here we make it for testing.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=58
 testing_env!(context.build());
```

macro rules it is a macro like function but evaluated at compile time in addition to have ability to generate codes, this line call macro and as we see it is like function call but have '!' so here we call macro called [testing_env](https://www.near-sdk.io/testing/unit-tests)  macro will initialize the blockchain interface with the VMContext which is either initialized through VMContextBuilder or manually through itself.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=64
 assert_eq!(contract.ft_balance_of(accounts(0)), 1_000_000.into());
```

we are using [assert_eq](https://doc.rust-lang.org/std/macro.assert_eq.html) in unit test to assert right equals left other wise the test will fail.

