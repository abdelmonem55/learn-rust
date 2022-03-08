<style>
    .present p {
        text-align: left;

    }
</style>

# Learn rust with NEAR

[Lockup / Vesting contract](https://github.com/near/core-contracts/tree/master/lockup): This contract acts as an escrow that locks and holds an owner's tokens for a lockup period. The contract consists of lockup and vesting processes that go simultaneously. A high-level overview could be found in NEAR documentation. A lockup period starts from the specified times

---

tamp and lasts for the specified duration. Tokens will be unlocked linearly. [read more](https://github.com/near/core-contracts/tree/master/lockup)

---

## Variables and constant

Rust has variables like any other language such as primatives and non-primatives and there are some special features we will disscus them one by one, we just want to show that when you assigment like defining a variable the ownership transfered from the right handside to left handside hence the variable moved unless it define the [Copy](https://doc.rust-lang.org/std/marker/trait.Copy.html) trait

----

### primative types

```rs=290
 transfers_enabled: bool,
```

here we are defining a variable as a parameter of type 'bool'

----

### [const](https://doc.rust-lang.org/std/keyword.const.html)

```rs=29
const NO_DEPOSIT: u128 = 0;
```

Rust has compile time const that recommended to be used with values that used many times in addition to it gives readablity for the code, for example here we define a const for image csv value that might be used many times in the program.

----

### [static](https://doc.rust-lang.org/reference/items/static-items.html)

```rs=26
static ALLOC: near_sdk::wee_alloc::WeeAlloc = near_sdk::wee_alloc::WeeAlloc::INIT;
```

Rust has runtime const ,it is static variable which all reference to this variable has static lifetime because it lives as long as program runs

----

### [struct](https://doc.rust-lang.org/book/ch05-01-defining-structs.html)

```rs=121
pub struct LockupContract {
    /// The account ID of the owner.
    pub owner_account_id: AccountId,

    /// Information about lockup schedule and the amount.
    pub lockup_information: LockupInformation,
```

Rust doesn't have classes but instead struct to define a container for data like in these line of code we define a struct with it's variables.

----

### [vector](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=704
 testing_env_with_promise_results(context.clone(), PromiseResult::Successful(vec![]));
```

vector is a dynamic list of homogeneous data stored in the heap, so here we are creating vector with no element with the help of macro `vec![]`

----

### [array](https://doc.rust-lang.org/rust-by-example/primitives/array.html)

```rs=261
 const SALT: [u8; 3] = [1, 2, 3];
```

array is a collection of contiguous data with same type and it states as fixed size, and here we define array of 3 element of type 'u8'

---

## [strings](https://doc.rust-lang.org/rust-by-example/std/str.html)

rust has many types that represents sequence of characters

----

### [String](https://doc.rust-lang.org/std/string/index.html)

```rs=665
 let staking_pool = "staking_pool".to_string();
```

String is a UTF-8 dynamic groupable list used to store sequence of chars, and we here we call [to_string](https://doc.rust-lang.org/std/string/trait.ToString.html) to get String from the variable of type '&str' we will check it next page

----

### [&str](https://doc.rust-lang.org/std/primitive.str.html)

```rs=746
 let staking_pool = "staking_pool".to_string();
```

it is slice version of String so it reference to other type of String ,here we are haveing a string literal of type '&'static str' and this mean it reference to a string with static [lifetime](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html) which will live all program lifetime

---

## functions

like any other language you can define some reusable code and it is called function.

----

### function definition and calling

```rs=273
 fn new_vesting_schedule(offset_in_days: u64) -> VestingSchedule {
     VestingSchedule {
         start_timestamp: to_ts(GENESIS_TIME_IN_DAYS - YEAR + offset_in_days).into(),
         cliff_timestamp: to_ts(GENESIS_TIME_IN_DAYS + offset_in_days).into(),
         end_timestamp: to_ts(GENESIS_TIME_IN_DAYS + YEAR * 3 + offset_in_days).into(),
     }
 }
```

here we are defining a function with name 'new_vesting_schedule', parameter and return type, line 275 we call function and pass expression to it an argument

---

## impl section, and static and instance functions

Rust is not object oriented language but you can define structs as data container and attach function to operate on this data, also you can do the same for enums.

----

### [impl](https://doc.rust-lang.org/std/keyword.impl.html)

```rs=150
impl LockupContract {
```

here we start to define functions to manipulate the derived type

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=181
 pub fn new(
     owner_account_id: AccountId,
     lockup_duration: WrappedDuration,
     lockup_timestamp: Option<WrappedTimestamp>,
     transfers_information: TransfersInformation,
     vesting_schedule: Option<VestingScheduleOrHash>,
     release_duration: Option<WrappedDuration>,
     staking_pool_whitelist_account_id: AccountId,
     foundation_account_id: Option<AccountId>,
 ) -> Self {
```

inside impl block we can define a static function like this so it can be called from the type itself, here we are returning [Self](https://stackoverflow.com/questions/32304595/whats-the-difference-between-self-and-self) from the function so here we refere to the current type.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/) call

```rs=320
 LockupContract::new(
     account_owner(),
     lockup_duration.into(),
     None,
     lockup_start_information,
     vesting_schedule,
     release_duration,
     AccountId::from("whitelist"),
     foundation_account_id,
 )
```

here we call a static function 'new' in the type 'LockupContract' and this is widely used method to create a new instance because Rust doesn't have constractor.

----

### instance function calling

```rs=219
 foundation_account_id.is_none(),
```

[is_none](https://doc.rust-lang.org/beta/core/option/enum.Option.html) is an instance function used to check the type of option is None or else

---

## [ownership](https://doc.rust-lang.org/rust-by-example/scope/move.html) and [borrowing](https://doc.rust-lang.org/rust-by-example/scope/borrow.html)

rust has unique concept called ownership and borrowing so when you assign variables to anther so most of the time instead of copying, the ownership taken by the second variable, so we can use borrowing and other things so keep the origin variable

----

### [Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html) and ownership

```rs=855
 contract.select_staking_pool(staking_pool.clone());
```

as we mention when we use a variable in passing to function or to assign anther variable so the ownership is transfered and the origin variable move and this exist with non-primative types unless they implement [Copy trait](https://doc.rust-lang.org/std/marker/trait.Copy.html), so here we use clone function to make a copy of data and so as not to be moved and reused after that

---

## [Generic types](https://doc.rust-lang.org/book/ch10-00-generics.html)

Every programming language has tools for effectively handling the duplication of concepts and write extensible agnostic

----

### [trait](https://doc.rust-lang.org/book/ch10-02-traits.html)

```rs=57
pub trait ExtStakingPoolWhitelist {
    fn is_whitelisted(&self, staking_pool_account_id: AccountId) -> bool;
}
```

trait define shared behaviour between implementors and it is consider as a contract, here we define a trait 'ExtStakingPoolWhitelist' with it's abstracted functionality

----

### [trait](https://doc.rust-lang.org/book/ch10-02-traits.html) impl

```rs=143
impl Default for LockupContract {
    fn default() -> Self {
        env::panic(b"The contract is not initialized.");
    }
}
```

here we impl [Default](https://doc.rust-lang.org/std/default/trait.Default.html) trait for 'LockupContract' type

---

## [testing in rust](https://doc.rust-lang.org/book/ch11-01-writing-tests.html)

Tests are Rust functions that verify that the non-test code is functioning in the expected manner.

----

### unit test

```rs=379
 #[test]
 fn test_add_full_access_key() {
     let (mut context, mut contract) = lockup_only_setup();
     context.block_timestamp = to_ts(GENESIS_TIME_IN_DAYS + YEAR);
     context.predecessor_account_id = account_owner();
     context.signer_account_id = account_owner();
     context.signer_account_pk = public_key(1).try_into().unwrap();
     testing_env!(context.clone());

     contract.add_full_access_key(public_key(4));
 }
```

so here we are writing a function and anntotate it as test like in first line, and we use assertion funtions like at line number 386

----

### unit test attribute

```rs=392
 #[should_panic(expected = "Tokens are still locked/unvested")]
```

here we attribute the test function and expect it fail with this messasge

---

## [modules](https://doc.rust-lang.org/rust-by-example/mod.html) and [imports](https://doc.rust-lang.org/reference/items/use-declarations.html)

Rust structure it's code in modules so you can have mutliple files in your projects.

----

### importing from other crates

```rs=3
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::json_types::Base58PublicKey;
use near_sdk::{env, ext_contract, near_bindgen, AccountId};
```

these lines import struct types from other crates that must be mentioned as a dependence in [Cargo.toml](https://doc.rust-lang.org/cargo/reference/manifest.html).

----

### modules

```rs=15
pub mod foundation;
pub mod foundation_callbacks;
pub mod gas;
pub mod owner_callbacks;
pub mod types;
```

we can define new module in a new rust file to structure our code but here but we must refer these module to the root of the crate and it is 'lib.rs' file in our code but in binary crate 'main.rs' is the root of the crate.

----

### shorten the modules members' path

```rs=7
pub use crate::foundation::*;
pub use crate::foundation_callbacks::*;
pub use crate::getters::*;
pub use crate::internal::*;
```

we can use codes defined in other modules as it defined in the root file by using [use](https://doc.rust-lang.org/reference/items/use-declarations.html)

---

## [macros](https://doc.rust-lang.org/book/ch19-06-macros.html)

Macros are widely used in metaprogramming for generating code at compile time.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=246
#[cfg(not(target_arch = "wasm32"))]
```

procedure macro used here to make a compile time switch and compile this code if the target is not WASM 

----

### [derive macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=119
#[near_bindgen]
#[derive(BorshDeserialize, BorshSerialize)]
```

procedure macros allow creating syntax extentions like at first line we attribute the that this struct should generate some code for near, second line [derive-macro](https://doc.rust-lang.org/reference/attributes/derive.html) to generate implementation for these traits instead of making custom implementation for them.[BorshDeserialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshDeserialize.html), [BorshSerialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshSerialize.html) are used to convert to and from object and binary value but Serialize and Deserialize are used to convert to and from object and json value.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=361
 assert_eq!(contract.get_owners_balance().0, 0);
```

macro rules it is a macro like function but evaluated at compile time in addition to have ability to generate codes, this line call macro and as we see it is like function call but have '!' so here we call macro called 'assert_eq' used to assert that right equals left

