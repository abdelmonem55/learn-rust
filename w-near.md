<style>
    .present p {
        text-align: left;

    }
</style>

# Learn rust with NEAR (w-near)

[w-near](https://github.com/near/core-contracts/tree/master/w-near)

---

## Variables and constant

Rust has variables like any other language such as primatives and non-primatives and there are some special features we will disscus them one by one.

----

### [primative types](https://doc.rust-lang.org/book/ch03-02-data-types.html)

```rs=56
 decimals: 24,
```

here we assigning value to integer variable.

----

### [struct](https://doc.rust-lang.org/book/ch05-01-defining-structs.html)

```rs=29
pub struct Contract {
    pub ft: FungibleToken,
}
```

Rust doesn't have classes but instead struct to define a container for data like in these line of code we define a struct with a variable 'ft'.

----

### [enum](https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html)

```rs=53
 icon: None,
```

Rust has enums to give names to const variables in additions it can hold data and here we use 'None' as a value for 'icon' which is an enum of type [Option](https://doc.rust-lang.org/std/option/).

----

### [vector](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=38
 ft: FungibleToken::new(b"a".to_vec()),
```

vector is a dynamic list of homogeneous data stored in the heap, so here we are creating vector from string bytes.

---

## [control flow](https://doc.rust-lang.org/book/ch03-05-control-flow.html)

you can run some code repeatedly or according to a condition.

----

### if condition

```rs=17
 if !self.ft.accounts.contains_key(&account_id) {
     // Not registered, register if enough $NEAR has been attached.
     // Subtract registration amount from the account balance.
     assert!(
         amount >= self.ft.storage_balance_bounds().min.0,
         "ERR_DEPOSIT_TOO_SMALL"
     );
     self.ft.internal_register_account(&account_id);
     amount -= self.ft.storage_balance_bounds().min.0;
 }
```

we comparing the data and accordingly we decide to execute code inside 'if' or 'else'.

---

## [strings](https://doc.rust-lang.org/rust-by-example/std/str.html)

rust has many types that represents sequence of characters.

----

### [String](https://doc.rust-lang.org/std/string/index.html)

```rs=50
 spec: FT_METADATA_SPEC.to_string(),
```

String is a UTF-8 dynamic groupable list used to store sequence of chars, and we here we call [to_string](https://doc.rust-lang.org/std/string/trait.ToString.html) to get String from anther type.

----

### [&str](https://doc.rust-lang.org/std/primitive.str.html)

```rs=52
 symbol: String::from("wNEAR"),
```

it is slice version of String so it reference to other type of String ,here we are haveing a string literal of type '&'static str' and this mean it reference to a string with static [lifetime](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html) which will live all program lifetime and here we are use it to get 'String' type.

---

## impl section, and static and instance functions

Rust is not object oriented language but you can define structs as data container and attach function to operate on this data, also you can do the same for enums.

----

### [impl](https://doc.rust-lang.org/std/keyword.impl.html)

```rs=34
impl Contract {
```

here we start to define functions to manipulate the derived type.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=36
 pub fn new() -> Self {
     Self {
         ft: FungibleToken::new(b"a".to_vec()),
     }
 }
```

inside impl block we can define a static function like this so it can be called from the type itself, here we are returning [Self](https://stackoverflow.com/questions/32304595/whats-the-difference-between-self-and-self) from the function so here we refere to the current type.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/) call

```rs=38
 ft: FungibleToken::new(b"a".to_vec()),
```

here we call a static function 'new' in the type 'FungibleToken' and this is widely used method to create a new instance because Rust doesn't have constractor.

----

### instance function and immutable reference

```rs=48
 fn ft_metadata(&self) -> FungibleTokenMetadata {
     FungibleTokenMetadata {
         spec: FT_METADATA_SPEC.to_string(),
         name: String::from("Wrapped NEAR fungible token"),
         symbol: String::from("wNEAR"),
         icon: None,
         reference: None,
         reference_hash: None,
         decimals: 24,
     }
 }
```

here we define an instance function with '&self' this means the function [borrows](https://doc.rust-lang.org/rust-by-example/scope/borrow.html) the current object for readonly like in first line we use it to get data from the current object.

----

### instance function calling

```rs=6
 self.ft.storage_balance_bounds().min
```

here in file 'legacy_storage.rs' we calling an instance function called 'storage_balance_bounds' to read some data.

---

## [Generic types](https://doc.rust-lang.org/book/ch10-00-generics.html)

Every programming language has tools for effectively handling the duplication of concepts and write extensible agnostic.

----

### [trait](https://doc.rust-lang.org/book/ch10-02-traits.html) impl

```rs=47
impl FungibleTokenMetadataProvider for Contract {
```

here we impl a trait called FungibleTokenMetadataProvider  for a struct called 'Contract'.

---

## [modules](https://doc.rust-lang.org/rust-by-example/mod.html) and [imports](https://doc.rust-lang.org/reference/items/use-declarations.html)

Rust structure it's code in modules so you can have mutliple files in your projects.

----

### importing from other crates

```rs=11
use near_contract_standards::fungible_token::metadata::{
    FungibleTokenMetadata, FungibleTokenMetadataProvider, FT_METADATA_SPEC,
};
use near_contract_standards::fungible_token::FungibleToken;
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::json_types::{ValidAccountId, U128};
```

these lines import struct types from other crates that must be mentioned as a dependence in [Cargo.toml](https://doc.rust-lang.org/cargo/reference/manifest.html).

---

## [macros](https://doc.rust-lang.org/book/ch19-06-macros.html)

Macros are widely used in metaprogramming for generating code at compile time.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=27
#[near_bindgen]
#[derive(BorshDeserialize, BorshSerialize, PanicOnDefault)]
pub struct Contract {
    pub ft: FungibleToken,
}
```

procedure macros allow creating syntax extentions like here we use [attribute](https://www.near-sdk.io/contract-structure/near-bindgen) for the struct to generate some code in order to validate near contract, at first line [derive-macro](https://doc.rust-lang.org/reference/attributes/derive.html) to generate implementation for these traits instead of making custom implementation for them.[BorshDeserialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshDeserialize.html), [BorshSerialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshSerialize.html) are used to convert to and from object and binary value.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=18
#[allow(unused_imports)]
```

here we use attibute to mute the compiler warning for unused imports.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=37
 #[payable]
```

By the default the methods are not [payable](https://www.near-sdk.io/contract-interface/payable-methods) and they will panic if someone will attempt to transfer tokens to them during the invocation so we use this attribute to allow this.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=35
 #[init]
```

here we use attribute the function to define it as an [initialization](https://docs.near.org/docs/develop/contracts/rust/near-sdk-rs) function.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=15
 assert!(amount > 0, "Requires positive attached deposit");
```

macro rules it is a macro like function but evaluated at compile time in addition to have ability to generate codes, this line at file 'w_near.rs' calling macro and as we see it is like function call but have '!' so here we call macro called 'assert' to assert condtion is true if not so it will panic.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=43
 log!("Withdraw {} yoctoNEAR from {}", amount, account_id);
```

this line call macro and as we see it is like function call but have '!' so here we call macro called [log](https://docs.rs/log/latest/log/) to log data for tracing.

