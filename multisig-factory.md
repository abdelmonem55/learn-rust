<style>
    .present p {
        text-align: left;

    }
</style>

# Learn rust with NEAR (Multisig Factory)

[Multisig Factory](https://github.com/near/core-contracts/tree/master/multisig-factory): Allows to create new Multisig contracts just by sending a transactions with the required configuration and funds. E.g. Removes need for using near repl and having wasm file available.

---

## Variables and constant

Rust has variables like any other language such as primatives and non-primatives and there are some special features we will disscus them one by one.

----

### [primative types](https://doc.rust-lang.org/book/ch03-02-data-types.html)

```rs=33
 num_confirmations: u64,
```

here we are defining a variable as a parameter of type 'u64'.

----

### [const](https://doc.rust-lang.org/std/keyword.const.html)

```rs=13
const CREATE_CALL_GAS: u64 = 50_000_000_000_000;
```

Rust has compile time const that recommended to be used with values that used many times in addition to it gives readablity for the code, for example here we define u64 type const.

----

### [static](https://doc.rust-lang.org/reference/items/static-items.html)

```rs=8
static ALLOC: near_sdk::wee_alloc::WeeAlloc<'_> = near_sdk::wee_alloc::WeeAlloc::INIT;
```

Rust has run time nearly const that recommended to be used with values that used many times in addition to it gives readablity for the code,also note that using static favour const when we have data with big size and want to allocate memory for it.

----

### [struct](https://doc.rust-lang.org/book/ch05-01-defining-structs.html)

```rs=24
pub struct MultisigFactory {}
```

Rust doesn't have classes but instead struct to define a container for data like in these line of code we define an empty struct.

----

### [enum](https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html)

```rs=17
pub enum MultisigMember {
    AccessKey { public_key: Base58PublicKey },
    Account { account_id: AccountId },
}
```

Rust has enums to give names to const variables in additions other featurs we will explore them in next lessons like in this code we define new type called 'MultisigMember ' with two possibilities.

----

### [vector](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=32
 members: Vec<MultisigMember>,
```

vector is a dynamic list of homogeneous data stored in the heap, so here we define a vector.

----

### [slice](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=10
const CODE: &[u8] = include_bytes!("../../multisig2/res/multisig2.wasm");
```

slice lets you reference contiguous sequence of elements in a collection, here we call [macro](https://doc.rust-lang.org/rust-by-example/macros.html) that gets the bytes in slice with [static lifetime](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html).

---

## [strings](https://doc.rust-lang.org/rust-by-example/std/str.html)

rust has many types that represents sequence of characters.

----

### [String](https://doc.rust-lang.org/std/string/index.html)

```rs=43
 .to_string()
```

String is a UTF-8 dynamic groupable list used to store sequence of chars, and we here we call [to_string](https://doc.rust-lang.org/std/string/trait.ToString.html) to get String from the variable anther type.

----

### [&str](https://doc.rust-lang.org/std/primitive.str.html)

```rs=10
const CODE: &[u8] = include_bytes!("../../multisig2/res/multisig2.wasm");
```

it is slice version of String so it reference to other type of String ,here we are haveing a string literal of type '&'static str' and this mean it reference to a string with static [lifetime](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html) which will live all program lifetime.

---

## impl section, and static and instance functions

Rust is not object oriented language but you can define structs as data container and attach function to operate on this data, also you can do the same for enums.

----

### [impl](https://doc.rust-lang.org/std/keyword.impl.html)

here we start to define functions to manipulate the derived type.

----

### [instance function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=29
 pub fn create(
     &mut self,
     name: AccountId,
     members: Vec<MultisigMember>,
     num_confirmations: u64,
 ) -> Promise {
```

inside impl block we can define instance function like this code defines function that takes parameters in addition to '& mut [self](https://doc.rust-lang.org/std/keyword.self.html)', rust introduce new concept called [mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html) so here we borrow mutable refrence for the current object so that the function can edit on it.

----

### instance function calling

```rs=37
 .create_account()
 .deploy_contract(CODE.to_vec())
 .transfer(env::attached_deposit())
 .function_call(
```

here we are creating a variable and call chain of instance functions on it and this is because here we are returning same object from the function.

---

## [modules](https://doc.rust-lang.org/rust-by-example/mod.html) and [imports](https://doc.rust-lang.org/reference/items/use-declarations.html)

Rust structure it's code in modules so you can have mutliple files in your projects.

----

### importing from other crates

```rs=1
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::json_types::Base58PublicKey;
use near_sdk::serde::{Deserialize, Serialize};
use near_sdk::serde_json::json;
```

these lines import struct types from other crates that must be mentioned as a dependence in [Cargo.toml](https://doc.rust-lang.org/cargo/reference/manifest.html).

---

## [macros](https://doc.rust-lang.org/book/ch19-06-macros.html)

Macros are widely used in metaprogramming for generating code at compile time.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=26
#[near_bindgen]
```

procedure macros allow creating syntax extentions like at first line we use [attribute](https://www.near-sdk.io/contract-structure/near-bindgen) for the struct to generate some code in order to validate near contract.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=15
#[derive(Serialize, Deserialize)]
```

[derive-macro](https://doc.rust-lang.org/reference/attributes/derive.html) to generate implementation for these traits instead of making custom implementation for them.[BorshDeserialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshDeserialize.html), [BorshSerialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshSerialize.html) are used to convert to and from object and binary value but Serialize and Deserialize are used to convert to and from object and json value.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=28
 #[payable]
```

[payable](https://www.near-sdk.io/contract-interface/payable-methods) allows the method to accept token transfer together with the function call.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=16
#[serde(crate = "near_sdk::serde", untagged)]
```

used to select [serde](serde.rs) crate and so we give it a path to it here.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=10
const CODE: &[u8] = include_bytes!("../../multisig2/res/multisig2.wasm");
```

macro rules it is a macro like function but evaluated at compile time in addition to have ability to generate codes, this line call macro and as we see it is like function call but have '!' so here we call macro called 'include_bytes' to read data from file in the compilation time.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=42
 json!({ "members": members, "num_confirmations": num_confirmations })
```

here we get json format with this data.

