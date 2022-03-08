<style>
    .present p {
        text-align: left;

    }
</style>

# Learn rust with NEAR

[cross contract low](https://github.com/near/near-sdk-rs/tree/master/examples/cross-contract-low-level)

---

## Variables and constant

Rust has variables like any other language such as [primatives and non-primatives](https://doc.rust-lang.org/book/ch03-02-data-types.html) and there are some special features we will disscus them one by one.

----

### [primative types](https://doc.rust-lang.org/book/ch03-02-data-types.html)

```rs=81
 let mut i = 0usize;
 let mut j = 0usize;
```

here we are defining two variables of type [usize](https://doc.rust-lang.org/std/primitive.usize.html) and declare them as 'mut' to make the variable mutable and we will learn about the mutablity in the next section.

----

### [struct](https://doc.rust-lang.org/book/ch05-01-defining-structs.html) as non-primative

```rs=11
pub struct CrossContract {
    checked_promise: bool,
}
```

Rust doesn't have classes but instead struct to define a container for data like in these line of code we define a struct with one variables 'checked_promise', in the declaration we use [pub](https://doc.rust-lang.org/std/keyword.pub.html) in order to make this type visable from outside and we in declare 'checked_promise' we omit it to make it private

----

### [vector](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=36
 pub fn merge_sort(&self, arr: Vec<u8>) {
     if arr.len() <= 1 {
         env::value_return(&serde_json::to_vec(&arr).unwrap());
         return;
     }
     let pivot = arr.len() / 2;
     let arr0 = arr[..pivot].to_vec();
     let arr1 = arr[pivot..].to_vec();
```

vector is a dynamic list of homogeneous data stored in the heap, for example here we define a vector as a parameter to the function at second line we get the size of the list, last two lines we are taking [range](https://doc.rust-lang.org/std/ops/struct.Range.html) of element from the vector and generate new vector with help of 'to_vec' function

----

### [slice](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=32
 let code: &[u8] = include_bytes!("../../status-message/res/status_message.wasm");
```

slice lets you reference contiguous sequence of elements in a collection, here we call [macro](https://doc.rust-lang.org/rust-by-example/macros.html) that gets the bytes in slice with [static lifetime](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html)

----

### [const](https://doc.rust-lang.org/std/keyword.const.html)

```rs=7
const SINGLE_CALL_GAS: Gas = Gas(200000000000000);
```

Rust has compile time const that recommended to be used with values that used many times in addition to improving the readablity for the code, for example here we define a const for single call gas cost.

---

## [control flow](https://doc.rust-lang.org/book/ch03-05-control-flow.html)

you can run some code repeatedly or according to a condition

----

### if condition

```rs=93
 if data0[i] < data1[j] {
     result.push(data0[i]);
     i += 1;
 } else {
     result.push(data1[j]);
     j += 1;
 }
```

we comparing the data and accordingly we decide to execute code inside 'if' or 'else'.

----

### looping

```rs=84
 loop {
     if i == data0.len() {
         result.extend(&data1[j..]);
         break;
     }
     if j == data1.len() {
         result.extend(&data0[i..]);
         break;
     }
     if data0[i] < data1[j] {
         result.push(data0[i]);
         i += 1;
     } else {
         result.push(data1[j]);
         j += 1;
     }
 }
```

here executing the code inside the loop infinitly or until breaking explicitly

---

## impl section, and static and instance functions

Rust is not object oriented language but you can define structs as data container and attach function to operate on this data, also you can do the same for enums.

----

### [impl](https://doc.rust-lang.org/std/keyword.impl.html)

```rs=22
impl CrossContract {
```

here we start to define functions to manipulate the derived type

----

### impl [trait](https://doc.rust-lang.org/book/ch10-02-traits.html)

```rs=15
impl Default for CrossContract {
```

trait is an interface that forced a specific design, here we are implementing [Default](https://doc.rust-lang.org/std/default/trait.Default.html) trait for 'CrossContract' struct so we must implement all functions in this trait.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=16
 fn default() -> Self {
     CrossContract { checked_promise: false }
 }
```

inside impl block we can define a static function like this so it can be called from the type itself, here we are returning [Self](https://stackoverflow.com/questions/32304595/whats-the-difference-between-self-and-self) from the function so here we refere to the current type.

----

### [instance function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=155
 pub fn transfer_money(&mut self, account_id: AccountId, amount: u64) {
     let promise_idx = env::promise_batch_create(&account_id);
     env::promise_batch_action_transfer(promise_idx, amount as u128);
 }
```

inside impl block we can define instance function like this code defines function that takes parameters in addition to '& mut [self](https://doc.rust-lang.org/std/keyword.self.html)', rust introduce new concept called [mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html) so here we borrow mutable refrence for the current object so that the function can edit on it.

----

### instance function calling

```rs=97
 result.push(data1[j]);
```

here we are calling a function called 'push' in variable called 'result' to push data into this [vector](https://doc.rust-lang.org/std/vec/struct.Vec.html)

----

### instance function and immutable reference

```rs=160
 pub fn promise_checked(&self) -> bool {
     self.checked_promise
 }
```

here we define an instance function with '&self' this means the function [borrows](https://doc.rust-lang.org/rust-by-example/scope/borrow.html) the current object for readonly like in first line we use it to get data from the current object.

---

## [ownership](https://doc.rust-lang.org/rust-by-example/scope/move.html) and [borrowing](https://doc.rust-lang.org/rust-by-example/scope/borrow.html)

rust has unique concept called ownership and borrowing so when you assign variables to anther so most of the time instead of copying, the ownership taken by the second variable, so we can use borrowing and other things so keep the origin variable

----

### [Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html) and ownership

```rs=61
 let promise3 =
     env::promise_then(promise2, account_id.clone(), "merge", &[], 0, prepaid_gas / 4);
```

as we mention when we use a variable in passing to function or to assign anther variable so the ownership is transfered and the origin variable move and this exist with non-primative types unless they implement [Copy trait](https://doc.rust-lang.org/std/marker/trait.Copy.html), so here we use clone function to make a copy of data and so as not to be moved and reused after that.

---

## [imports](https://doc.rust-lang.org/reference/items/use-declarations.html)

You can import some code from external library

----

### importing from other crates

```rs=1
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::json_types::U128;
use near_sdk::serde_json::{self, json};
use near_sdk::{env, near_bindgen, require, AccountId, Gas, PromiseResult};
```

these lines import struct types from other crates that must be mentioned as a dependence in [Cargo.toml](https://doc.rust-lang.org/cargo/reference/manifest.html), [self](https://users.rust-lang.org/t/what-does-self-mean-in-use/15559/2) means import the previous member itself.

---

## [macros](https://doc.rust-lang.org/book/ch19-06-macros.html)

Macros are widely used in metaprogramming for generating code at compile time.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=9
#[near_bindgen]
#[derive(BorshDeserialize, BorshSerialize)]
```

procedure macros allow creating syntax extentions like at first line we use [attribute](https://www.near-sdk.io/contract-structure/near-bindgen) for the struct to generate code to validate near contract contract, second line [derive-macro](https://doc.rust-lang.org/reference/attributes/derive.html) to generate implementation for these traits instead of making custom implementation for them.[BorshDeserialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshDeserialize.html), [BorshSerialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshSerialize.html) are used to convert to and from object and binary value but Serialize and Deserialize are used to convert to and from object and json value.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=83
 let mut result = vec![];
```

macro rules it is a macro like function but evaluated at compile time in addition to have ability to generate codes, this line call macro to create an empty vector

