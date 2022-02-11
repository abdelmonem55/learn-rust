# Learn rust with NEAR
Smart Contracts are the back-end of your application that runs code and stores data on the blockchain.
			   All smart contracts on NEAR must be compiled to [WebAssemble](https://webassembly.org/) or simply WASM. Currently,
			   we support two languages [AssembleScript](https://www.assemblyscript.org/) and [Rust](https://www.rust-lang.org/) with custom software development kits (SDKs) to assist in their creation 
			   but you can use any programming language and compile it to wasm.
			   But here we will use Rust as it is a powerful language with a great developer experience.

----

 ## Your first contract
[status-message](https://github.com/near/near-sdk-rs/tree/master/examples/status-message):
records the status messages of the accounts that call this contract.
## variables and functions
```rust= 
    pub fn set_status(&mut self, message: String) {
        let account_id = env::signer_account_id();
        log!("{} set_status with message {}", account_id, message);
        self.records.insert(account_id, message);
    }
```
This is a defination for [function](https://doc.rust-lang.org/book/ch03-03-how-functions-work.html) in rust, so here specifing a function name and signatures, set_status is a function with [String](https://doc.rust-lang.org/std/string/struct.String.html) type as input and no return type .
First line in the [function](https://doc.rust-lang.org/book/ch03-03-how-functions-work.html) here calls function and creates [variable](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html) finally assigning the result to this variable, creating a varible in rust like any other language except the [mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html).

----

```rust=
pub fn get_status(&self, account_id: AccountId) -> Option::<String> {
        log!("get_status for account_id {}", account_id);
        self.records.get(&account_id).cloned()
    }
```
at line 3 we omtted ';' because in rust you can return value implictly like this or using [return](https://doc.rust-lang.org/std/keyword.return.html) keyword like any statement.

----
## struct
```rust=
pub struct StatusMessage {
    records: HashMap<AccountId, String>,
}
```
Rust is like any other other language has primative types like bool,i32,u32, [more types](https://doc.rust-lang.org/book/ch03-02-data-types.html), in addition to [user defined types](https://doc.rust-lang.org/rust-by-example/custom_types.html) like [struct](https://doc.rust-lang.org/rust-by-example/custom_types/structs.html).
these lines define structure with name 'StatusMessage' with records as a [hashmap](https://doc.rust-lang.org/std/collections/struct.HashMap.html) member variable, hashmap is a custom variable that is built in the standard library of rust.

----

## impl,ownership and borrowing
```rust=
impl StatusMessage {
    #[payable]
    pub fn set_status(&mut self, message: String) {
        let account_id = env::signer_account_id();
        log!("{} set_status with message {}", account_id, message);
        self.records.insert(account_id, message);
    }

    pub fn get_status(&self, account_id: AccountId) -> Option::<String> {
        log!("get_status for account_id {}", account_id);
        self.records.get(&account_id).cloned()
    }
}
```
in rust we can attach functions to the defined struct using [impl](https://doc.rust-lang.org/rust-by-example/generics/impl.html) so the [function](https://doc.rust-lang.org/rus-by-example/fn/methods.html) might be called from the instance or from the type itself, to make instance function you must use [self](https://doc.rust-lang.org/std/keyword.self.html), &mut self or &self to refer to the current object.
Rust introduces new concept of [ownership](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html) and [borrowing](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html) for example when you make the function take 'self' as a prameter so here you give the function the ownership for the current object instead of take a copy of it, but when you pass '&self' or '&mut self' here you make the function borrow the current object for lifetime of the function, as we mentioned before declarations of the variable determines the [mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html) of the variable  so '&self' borrows the current object immutable so that it can't be changed and in this case called view method in smart contract code but '&mut self' borrows the current object so that it can't be changed so it is called change in the smart contract code.
for example at line 6 we call 'insert' function in 'records' member of the current StatusMessage object so we understand that 'insert' function is member function for type of 'records', also we see that at this line we pass 'message' and 'account_id' to the function so here we give this function the ownership of these variable so you can't use them after this line but in other hand at line 11 we pass '&account_id' so it is called borrowing the variable to the function. 

anther user define type here is the [enum](https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html),enum in rust is like in other languages which represents named values except it can hold data, at line 9 we return '[Option](https://doc.rust-lang.org/std/option/)<[String](https://doc.rust-lang.org/std/string/struct.String.html)>' from the function so the function might return 'None' and it is equivalent to 'null' or return 'Some(String)' that has data.

----
## importing
```rust=
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::{env, log, metadata, near_bindgen, AccountId};

use std::collections::HashMap;
```

here,we are [importing](https://doc.rust-lang.org/reference/items/use-declarations.html) some structs,[traits](https://doc.rust-lang.org/book/ch10-02-traits.html) and other memebers from other libraries,also they are cold crates, but to import crate you must mention it in [Cargo.toml](https://doc.rust-lang.org/cargo/guide/cargo-toml-vs-cargo-lock.html) file.

----

```toml=
[package]
name = "status-message"
version = "0.1.0"
authors = ["Near Inc <hello@nearprotocol.com>"]
edition = "2018"

[lib]
crate-type = ["cdylib"]

[dependencies]
near-sdk = { path = "../../near-sdk" }
```
[Cargo.toml](https://doc.rust-lang.org/cargo/reference/config.html) has configuration for the rust project,
 package section has fields that define metadata about the project.
 [depenedencies](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html) section starting from line 10 has the external crates that used in the project so in line 10 we import 'near-sdk' from this relative path.
lib section has configuration for the current lib crate, and "cdylib" means dynamic system library will be produced.

----
[mission-control](https://github.com/near/near-sdk-rs/tree/master/examples/mission-control):implements simulation of a distributed network of drones interacting with the mission control system.
## if condtion and loops
```rust=
if lifetime_after <= lifetime_before {
            self.is_alive = false;
}
```
Rust has [if condition](https://doc.rust-lang.org/rust-by-example/flow_control/if_else.html) like other languages but the `()` is optional, but the strange in rust is if is and expression not statement as we well see.

```rust=
if success {
    Tranx::Approved(buyer, seller)
} else {
    Tranx::Denied(deficit)
}
```
as we see at 2th and 4th we didn't put ';' and as we mention previously it's explicit return data from the function so we could understand that 'if expression' returns value so it can be expression.

```rust=
 for key in keys {
    let q = lhs.entry(key.clone()).or_insert(Quantity(0));
    let Quantity(lhs_quantity) = *q;
    *q = Quantity(lhs_quantity * rhs_quantity);
}
```
here we use [for](https://doc.rust-lang.org/rust-by-example/flow_control/for.html) to loop over keys, in rust we can loop over [iterators](https://doc.rust-lang.org/std/iter/trait.Iterator.html)

----
## enums
```rust=
 pub enum Tranx {
    Approved(Account, Account),
    Denied(HashMap<Asset, Quantity>),
}
```
here we define [enum](https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html) with two options.
[enum](https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html) in rust is like in other languages is custome type which represents named values except it can hold data.
[Result](https://doc.rust-lang.org/std/result/) and [Option](https://doc.rust-lang.org/std/option/) are the most important examples for enums and they are builtin the standard library,
```rust=
match buyer.get(asset) {
    Some(Quantity(quantity)) if *quantity < 0 => {
    success = false;
    deficit.insert(asset.clone(), Quantity(*quantity));
    }
    _ => (),
}
```
[match](https://doc.rust-lang.org/rust-by-example/flow_control/match.html) [expression](https://doc.rust-lang.org/reference/statements-and-expressions.html) in rust is used for pattern matching and here we use it to match againts options (Some('with data') or None).

```rust=
if let Some(Tranx::Approved(buyer, _)) = exs.iter().find_map(|ex| {
        match Account::exchange(rates.get(ex).unwrap(), Quantity(1), &self.account, mission) {
            Tranx::Denied(_) => None,
            tranx => Some(tranx),
        }
    }) {
        self.account = buyer;
    }

```
[if let](https://doc.rust-lang.org/rust-by-example/flow_control/if_let.html) used in matching againest enums like 'match' expression but it's best practice to use it in case of match againest only one member other wise use 'match', and here is an example of how to uses it to match againest 'Option' enum type. line 3 learn us how to access enum type variabe and it's data.

----
## modules

```rust=
//lib rs
mod account;
mod agent;
mod asset;
#[macro_use]
mod macros;
mod mission_control;
mod rate;
```
This smart contract code is designed in multi-<a href="https://doc.rust-lang.org/rust-by-example/mod.html">modules</a>
                  and the root of the crate is lib.rs combine them all.

----                  
## traits and generics
rust is not object oriented language but has [traits](https://doc.rust-lang.org/book/ch10-02-traits.html) that can be used to define shared behavior.
```rust=
impl PartialEq for Account {
    fn eq(&self, rhs: &Account) -> bool {
        let mut lhs = self.clone();
        let mut rhs = rhs.clone();
        lhs.prime(&rhs);
        rhs.prime(&lhs);
        let Account(lhs) = lhs;
        let Account(rhs) = rhs;
        lhs == rhs
    }
}
```
[PartialEq](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html) is builtin trait for equality and it called operator overload in other languages and here we implement it for Account type by implemting all it's types, we can implment this trait and other operator overload automatically.

```rust=
let credit = &Account(rate.credit.clone()) * quantity;
let debit = &Account(rate.debit.clone()) * quantity;
let (buyer, seller) = (&(buyer - &debit) + &credit, &(seller - &credit) + &debit);
```
here we are making arithmatic operations on non primative types, but we must implement [operator overloads](https://doc.rust-lang.org/rust-by-example/trait/ops.html) traits for them.

```rust=
fn op<F>(lhs: &Account, rhs: &Account, op: F) -> Account
    where
        F: Fn(&Quantity, &Quantity) -> Quantity,
```
here we define function with parameters which one of them is 'op:F' and it is a [generic](https://doc.rust-lang.org/rust-by-example/generics.html) type and here we bounded it with trait type which is [Fn](https://doc.rust-lang.org/std/ops/trait.Fn.html).

## macros
[macros](https://doc.rust-lang.org/rust-by-example/macros.html) in rust allows metaprogramming, there are macro rules and procudure macro and it is an advanced topic with alot of details and you  can read more from [here](http://web.mit.edu/rust-lang_v1.25/arch/amd64_ubuntu1404/share/doc/rust/html/book/first-edition/macros.html). 

```rust=
#[derive(
    PartialEq,
    Eq,
    PartialOrd,
    Hash,
    Clone,
    Copy,
    Serialize,
    Deserialize,
    Debug,
    BorshDeserialize,
    BorshSerialize,
)]
#[serde(crate = "near_sdk::serde")]
pub struct Quantity(pub i32);
```
first line use [derive-macro](https://doc.rust-lang.org/reference/attributes/derive.html) to generate implementation for these traits instead of making custom implementation for them.
[BorshDeserialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshDeserialize.html), [BorshSerialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshSerialize.html) are used to convert to and from object and binary value but Serialize and Deserialize are used to convert to and from object and json value. 

```rust=
lhs.insert(rhs_key.clone(), Quantity(0));

```
here we use the function 'clone' on the non-primitive variable to get a deplicate of it but this requires that the type of this variable implementing [Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html) trait other wise to get the data you only can move it's ownership if it's not implemnting [Copy](https://doc.rust-lang.org/std/marker/trait.Copy.html) trait, and in the previous code we see that we implemented these traits using derive-macro.

```rust=
fn mission_default() -> Account {
    Account(hashmap![
        Asset::MissionTime => Quantity(1000000),
    ])
}
```
It is a defintion for a private function, hashmap! it is [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html) call with key=>value as input, this macro take pramaters and generate code to make a hashmap with this data.

----

## Learning with fixing errors and warnnings
we will investigate smart contracts that has warrnings and errors appear when using [cargo check](https://doc.rust-lang.org/cargo/commands/cargo-check.html) then fixing them one by one.

we will investigate [status message collection](https://github.com/near/near-sdk-rs/tree/master/examples/status-message-collections):
               records the status messages of the accounts that call this contract.
 
 ---
 when you compile the following code you will get following errors
 ```rust
use near_sdk::collections::{LookupMap, LookupSet};
use near_sdk::{env, near_bindgen, BorshStorageKey, AccountId};

#[derive(BorshSerialize, BorshStorageKey)]
enum StorageKey {
    Records,
    UniqueValues,
}

#[near_bindgen]
#[derive(BorshDeserialize, BorshSerialize)]
pub struct StatusMessage {
    pub records: LookupMap<AccountId, String>,
    pub unique_values: LookupSet<String>,
}

impl Default for StatusMessage {
    fn default() -> self {
        self {
            records: LookupMap::new(StorageKey::Records),
            unique_values: LookupSet::new(StorageKey::UniqueValues),
        }
    }
}

#[near_bindgen]
impl StatusMessage {
    /// Returns true if the message is unique
    pub fn set_status(&self, message: String) -> bool {
        let account_id = env::signer_account_id();
        self.records.insert(&account_id, message);
        self.unique_values.insert(&message)
    }

    pub fn get_status(&self, account_id: AccountId) -> Option&ltString&gt {
        self.records.get(&account_id);
    }
}

-first error

```
error: cannot find derive macro `BorshSerialize` in this scope
      --> src\lib.rs:5:10
      |
    5 | #[derive(BorshSerialize, BorshStorageKey)]
      |          ^^^^^^^^^^^^^^

    error: cannot find derive macro `BorshDeserialize` in this scope
      --> src\lib.rs:12:10
       |
    12 | #[derive(BorshDeserialize, BorshSerialize)]
       |          ^^^^^^^^^^^^^^^^
```
