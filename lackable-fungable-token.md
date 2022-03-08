<style>
    .present p {
        text-align: left;

    }
</style>

# Learn rust with NEAR

[lockable fungable token](https://github.com/near/near-sdk-rs/blob/master/examples/lockable-fungible-token): Lockable Fungible token but designed for composability in the async runtime like NEAR.It's an extension of a Fungible Token Standard (NEP#21) with locks. Locks allow composability of the contracts, but require careful GAS management, because the

---

 token contract itself doesn't guarantee the automatic unlocking call. That's why it shouldn't be used in production until Safes are implemented from (NEP#26)

---

## Variables and constant

Rust has variables like any other language such as primatives and non-primatives and there are some special features we will disscus them one by one, we just want to show that when you assigment like defining a variable the ownership transfered from the right handside to left handside hence the variable moved unless it define the [Copy](https://doc.rust-lang.org/std/marker/trait.Copy.html) trait

----

### primative types

```rs=81
 let mut i = 0usize;
 let mut j = 0usize;
```

here we are defining a variable with value and cast it to type [u128](https://doc.rust-lang.org/std/primitive.u128.html) so the compiler bind the type to u128

----

### [struct](https://doc.rust-lang.org/book/ch05-01-defining-structs.html)

```rs=8
pub struct Account {
    /// Current unlocked balance.
    pub balance: Balance,
    /// Allowed account to the allowance amount.
    pub allowances: HashMap<AccountId, Balance>,
    /// Allowed account to locked balance.
    pub locked_balances: HashMap<AccountId, Balance>,
}
```

Rust doesn't have classes but instead struct to define a container for data like in these line of code we define a struct with it's variables.

----

### [vector](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=41
 let pivot = arr.len() / 2;
 let arr0 = arr[..pivot].to_vec();
 let arr1 = arr[pivot..].to_vec();
```

vector is a dynamic list of homogeneous data stored in the heap, for example at first line we get the size of the list, second and third lines we are taking [range](https://doc.rust-lang.org/std/ops/struct.Range.html) of element from the vector and generate new vector with help of 'to_vec' function

----

### [slice](https://doc.rust-lang.org/std/vec/struct.Vec.html)

```rs=32
 let code: &[u8] = include_bytes!("../../status-message/res/status_message.wasm");
```

slice lets you reference contiguous sequence of elements in a collection, here we call [macro](https://doc.rust-lang.org/rust-by-example/macros.html) that gets the bytes in slice with [static lifetime](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html)

----

### [hashmap](https://doc.rust-lang.org/std/collections/struct.HashMap.html)

```rs=8
pub struct Account {
    /// Current unlocked balance.
    pub balance: Balance,
    /// Allowed account to the allowance amount.
    pub allowances: HashMap<AccountId, Balance>,
    /// Allowed account to locked balance.
    pub locked_balances: HashMap<AccountId, Balance>,
}
```

hashmap is non-primative buildin type in the standard library used to store key and value pairs values, so here at line 12 and 14 we declaring two variables with hashmap types for storing data to be searchable efficiently using hashing

---

## [strings](https://doc.rust-lang.org/rust-by-example/std/str.html)

rust has many types that represents sequence of characters

----

### [String](https://doc.rust-lang.org/std/string/index.html)

```rs=49
 json!({ "arr": arr0 }).to_string().as_bytes(),
```

String is a UTF-8 dynamic groupable list used to store sequence of chars, and we here we call [to_string](https://doc.rust-lang.org/std/string/trait.ToString.html) on the variable to get String

----

### [&str](https://doc.rust-lang.org/std/primitive.str.html)

```rs=48
 "merge_sort",
```

it is slice version of String so it reference to other type of String ,here we are passing a string literal of type '&'static str' and this mean it reference to a string with static [lifetime](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html) which will live all program lifetime

---

## functions

like any other language you can define some reusable code and it is called function.

----

### function definition

```rs=216
 /// Returns total supply of tokens.
 pub fn get_total_supply(&self) -> String {
     self.total_supply.to_string()
 }
```

here we are defining a function with name 'get_total_supply', parameter and return type

---

## impl section, and static and instance functions

Rust is not object oriented language but you can define structs as data container and attach function to operate on this data, also you can do the same for enums.

----

### [impl](https://doc.rust-lang.org/std/keyword.impl.html)

```rs=58
impl FunToken {
```

here we start to define functions to manipulate the derived type

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=60
 pub fn new(owner_id: AccountId, total_supply: String) -> Self {
     let total_supply = u128::from_str(&total_supply).expect("Failed to parse total supply");
     let mut ft = Self { accounts: UnorderedMap::new(b"a"), total_supply };
     let mut account = ft.get_account(&owner_id);
     account.balance = total_supply;
     ft.accounts.insert(&owner_id, &account);
     ft
 }
```

inside impl block we can define a static function like this so it can be called from the type itself, here we are returning [Self](https://stackoverflow.com/questions/32304595/whats-the-difference-between-self-and-self) from the function so here we refere to the current type.

----

### [static function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/) call

```rs=337
 let mut contract = FunToken::new(carol(), total_supply.to_string());
```

here we call a static function 'new' in the type 'FunToken' and this is widely used method to create a new instance because Rust doesn't have constractor.

----

### [instance function](https://turreta.com/2019/10/14/static-and-instance-methods-in-struct/)

```rs=212
 pub fn transfer(&mut self, new_owner_id: AccountId, amount: String) {
     self.transfer_from(env::predecessor_account_id(), new_owner_id, amount);
 }
```

inside impl block we can define instance function like this code defines function that takes parameters in addition to '& mut [self](https://doc.rust-lang.org/std/keyword.self.html)', rust introduce new concept called [mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html) so here we borrow mutable refrence for the current object so that the function can edit on it.

----

### instance function and immutable reference

```rs=242
impl FunToken {
    /// Helper method to get the account details for `owner_id`.
    fn get_account(&self, owner_id: &AccountId) -> Account {
        self.accounts.get(owner_id).unwrap_or_default()
    }
}
```

here we define an instance function with '&self' this means the function [borrows](https://doc.rust-lang.org/rust-by-example/scope/borrow.html) the current object for readonly like in first line we use it to get data from the current object, also note that at last line in the function we omit the semicolon and this means implicit return from the function

----

### instance function calling

```rs=63
 let mut account = ft.get_account(&owner_id);
```

we call an instance function on variable of type 'FunToken' 

---

## [ownership](https://doc.rust-lang.org/rust-by-example/scope/move.html) and [borrowing](https://doc.rust-lang.org/rust-by-example/scope/borrow.html)

rust has unique concept called ownership and borrowing so when you assign variables to anther so most of the time instead of copying, the ownership taken by the second variable, so we can use borrowing and other things so keep the origin variable

----

### [Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html) and ownership

```rs=20
 self.allowances.insert(escrow_account_id.clone(), allowance);
```

as we mention when we use a variable in passing to function or to assign anther variable so the ownership is transfered and the origin variable move and this exist with non-primative types unless they implement [Copy trait](https://doc.rust-lang.org/std/marker/trait.Copy.html), so here we use clone function to make a copy of data and so as not to be moved and reused after that

---

## [testing in rust](https://doc.rust-lang.org/book/ch11-01-writing-tests.html)

Tests are Rust functions that verify that the non-test code is functioning in the expected manner.

----

### unit test

```rs=262
 #[test]
 fn test_new() {
     let context = get_context(carol());
     testing_env!(context);
     let total_supply = 1_000_000_000_000_000u128;
     let contract = FunToken::new(bob(), total_supply.to_string());
     assert_eq!(contract.get_total_supply(), total_supply.to_string());
     assert_eq!(contract.get_unlocked_balance(bob()), total_supply.to_string());
     assert_eq!(contract.get_total_balance(bob()), total_supply.to_string());
 }
```

so here we writing a function and anntotate it as test like in first line, and we use assertion funtions like at last 3 lines of code so they must succeed other wise the test will fail

---

## [modules](https://doc.rust-lang.org/rust-by-example/mod.html) and [imports](https://doc.rust-lang.org/reference/items/use-declarations.html)

Rust structure it's code in modules so you can have mutliple files in your projects.

----

### importing from other crates

```rs=1
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::collections::UnorderedMap;
use near_sdk::{env, near_bindgen, AccountId, Balance, PanicOnDefault};
use std::collections::HashMap;
use std::str::FromStr;
```

these lines import struct types from other crates that must be mentioned as a dependence in [Cargo.toml](https://doc.rust-lang.org/cargo/reference/manifest.html).

----

### modules

```rs=49
pub struct FunToken {
    /// AccountID -> Account details.
    pub accounts: UnorderedMap<AccountId, Account>,

    /// Total supply of the all token.
    pub total_supply: Balance,
}

#[near_bindgen]
impl FunToken {
    #[init]
    pub fn new(owner_id: AccountId, total_supply: String) -> Self {
        let total_supply = u128::from_str(&total_supply).expect("Failed to parse total supply");
        let mut ft = Self { accounts: UnorderedMap::new(b"a"), total_supply };
        let mut account = ft.get_account(&owner_id);
        account.balance = total_supply;
        ft.accounts.insert(&owner_id, &account);
        ft
    }

    /// Sets amount allowed to spent by `escrow_account_id` on behalf of the caller of the function
    /// (`predecessor_id`) who is considered the balance owner to the new `allowance`.
    /// If some amount of tokens is currently locked by the `escrow_account_id` the new allowance is
    /// decreased by the amount of locked tokens.
    pub fn set_allowance(&mut self, escrow_account_id: AccountId, allowance: String) {
        let allowance = u128::from_str(&allowance).expect("Failed to parse allowance");
        let owner_id = env::predecessor_account_id();
        if escrow_account_id == owner_id {
            env::panic_str("Can't set allowance for yourself");
        }
        let mut account = self.get_account(&owner_id);
        let locked_balance = account.get_locked_balance(&escrow_account_id);
        if locked_balance > allowance {
            env::panic_str("The new allowance can't be less than the amount of locked tokens");
        }

        account.set_allowance(&escrow_account_id, allowance - locked_balance);
        self.accounts.insert(&owner_id, &account);
    }

    /// Locks an additional `lock_amount` to the caller of the function (`predecessor_id`) from
    /// the `owner_id`.
    /// Requirements:
    /// * The (`predecessor_id`) should have enough allowance or be the owner.
    /// * The owner should have enough unlocked balance.
    pub fn lock(&mut self, owner_id: AccountId, lock_amount: String) {
        let lock_amount = u128::from_str(&lock_amount).expect("Failed to parse allow lock_amount");
        if lock_amount == 0 {
            env::panic_str("Can't lock 0 tokens");
        }
        let escrow_account_id = env::predecessor_account_id();
        let mut account = self.get_account(&owner_id);

        // Checking and updating unlocked balance
        if account.balance < lock_amount {
            env::panic_str("Not enough unlocked balance");
        }
        account.balance -= lock_amount;

        // If locking by escrow, need to check and update the allowance.
        if escrow_account_id != owner_id {
            let allowance = account.get_allowance(&escrow_account_id);
            if allowance < lock_amount {
                env::panic_str("Not enough allowance");
            }
            account.set_allowance(&escrow_account_id, allowance - lock_amount);
        }

        // Updating total lock balance
        let locked_balance = account.get_locked_balance(&escrow_account_id);
        account.set_locked_balance(&escrow_account_id, locked_balance + lock_amount);

        self.accounts.insert(&owner_id, &account);
    }

    /// Unlocks the `unlock_amount` from the caller of the function (`predecessor_id`) back to
    /// the `owner_id`.
    /// If called not by the `owner_id` then the `unlock_amount` will be converted to the allowance.
    /// Requirements:
    /// * The (`predecessor_id`) should have at least `unlock_amount` locked tokens from `owner_id`.
    pub fn unlock(&mut self, owner_id: AccountId, unlock_amount: String) {
        let unlock_amount =
            u128::from_str(&unlock_amount).expect("Failed to parse allow unlock_amount");
        if unlock_amount == 0 {
            env::panic_str("Can't unlock 0 tokens");
        }
        let escrow_account_id = env::predecessor_account_id();
        let mut account = self.get_account(&owner_id);

        // Checking and updating locked balance
        let locked_balance = account.get_locked_balance(&escrow_account_id);
        if locked_balance < unlock_amount {
            env::panic_str("Not enough locked tokens");
        }
        account.set_locked_balance(&escrow_account_id, locked_balance - unlock_amount);

        // If unlocking by escrow, need to update allowance.
        if escrow_account_id != owner_id {
            let allowance = account.get_allowance(&escrow_account_id);
            account.set_allowance(&escrow_account_id, allowance + unlock_amount);
        }

        // Updating unlocked balance
        account.balance += unlock_amount;

        self.accounts.insert(&owner_id, &account);
    }

    /// Transfers the `amount` of tokens from `owner_id` to the `new_owner_id`.
    /// First uses locked tokens by the caller of the function (`predecessor_id`). If the amount
    /// of locked tokens is not enough to cover the full amount, then uses unlocked tokens
    /// for the remaining balance.
    /// Requirements:
    /// * The caller of the function (`predecessor_id`) should have at least `amount` of locked plus
    /// allowance tokens.
    /// * The balance owner should have at least `amount` of locked (by `predecessor_id`) plus
    /// unlocked tokens.
    pub fn transfer_from(&mut self, owner_id: AccountId, new_owner_id: AccountId, amount: String) {
        let amount = u128::from_str(&amount).expect("Failed to parse allow amount");
        if amount == 0 {
            env::panic_str("Can't transfer 0 tokens");
        }
        let escrow_account_id = env::predecessor_account_id();
        let mut account = self.get_account(&owner_id);

        // Checking and updating locked balance
        let locked_balance = account.get_locked_balance(&escrow_account_id);
        let remaining_amount = if locked_balance >= amount {
            account.set_locked_balance(&escrow_account_id, locked_balance - amount);
            0
        } else {
            account.set_locked_balance(&escrow_account_id, 0);
            amount - locked_balance
        };

        // If there is remaining balance after the locked balance, we try to use unlocked tokens.
        if remaining_amount > 0 {
            // Checking and updating unlocked balance
            if account.balance < remaining_amount {
                env::panic_str("Not enough unlocked balance");
            }
            account.balance -= remaining_amount;

            // If transferring by escrow, need to check and update allowance.
            if escrow_account_id != owner_id {
                let allowance = account.get_allowance(&escrow_account_id);
                // Checking and updating unlocked balance
                if allowance < remaining_amount {
                    env::panic_str("Not enough allowance");
                }
                account.set_allowance(&escrow_account_id, allowance - remaining_amount);
            }
        }

        self.accounts.insert(&owner_id, &account);

        // Deposit amount to the new owner
        let mut new_account = self.get_account(&new_owner_id);
        new_account.balance += amount;
        self.accounts.insert(&new_owner_id, &new_account);
    }

    /// Same as `transfer_from` with `owner_id` `predecessor_id`.
    pub fn transfer(&mut self, new_owner_id: AccountId, amount: String) {
        self.transfer_from(env::predecessor_account_id(), new_owner_id, amount);
    }

    /// Returns total supply of tokens.
    pub fn get_total_supply(&self) -> String {
        self.total_supply.to_string()
    }

    /// Returns total balance for the `owner_id` account. Including all locked and unlocked tokens.
    pub fn get_total_balance(&self, owner_id: AccountId) -> String {
        self.get_account(&owner_id).total_balance().to_string()
    }

    /// Returns unlocked token balance for the `owner_id`.
    pub fn get_unlocked_balance(&self, owner_id: AccountId) -> String {
        self.get_account(&owner_id).balance.to_string()
    }

    /// Returns current allowance for the `owner_id` to be able to use by `escrow_account_id`.
    pub fn get_allowance(&self, owner_id: AccountId, escrow_account_id: AccountId) -> String {
        self.get_account(&owner_id).get_allowance(&escrow_account_id).to_string()
    }

    /// Returns current locked balance for the `owner_id` locked by `escrow_account_id`.
    pub fn get_locked_balance(&self, owner_id: AccountId, escrow_account_id: AccountId) -> String {
        self.get_account(&owner_id).get_locked_balance(&escrow_account_id).to_string()
    }
}

impl FunToken {
    /// Helper method to get the account details for `owner_id`.
    fn get_account(&self, owner_id: &AccountId) -> Account {
        self.accounts.get(owner_id).unwrap_or_default()
    }
}

#[cfg(not(target_arch = "wasm32"))]
#[cfg(test)]
mod tests {
```

we can define new module in rust to structure our code, here we declaring a module at third line ,and at second line we annotate that it is a test module and at first line use a [compile time switch](https://doc.rust-lang.org/reference/conditional-compilation.html) to compile this module only at non-WASM platform.

---

## [macros](https://doc.rust-lang.org/book/ch19-06-macros.html)

Macros are widely used in metaprogramming for generating code at compile time.

----

### [procedure macro](https://doc.rust-lang.org/reference/procedural-macros.html)

```rs=7
#[derive(Default, BorshDeserialize, BorshSerialize)]
```

procedure macros allow creating syntax extentions like at first line we attribute the that this struct should generate some code for near, second line [derive-macro](https://doc.rust-lang.org/reference/attributes/derive.html) to generate implementation for these traits instead of making custom implementation for them.[BorshDeserialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshDeserialize.html), [BorshSerialize](https://docs.rs/borsh/latest/borsh/de/trait.BorshSerialize.html) are used to convert to and from object and binary value but Serialize and Deserialize are used to convert to and from object and json value and 'Default' to implement default trait for this type.

----

### [macro rules](https://doc.rust-lang.org/rust-by-example/macros.html)

```rs=285
 assert_eq!(contract.get_unlocked_balance(bob()), transfer_amount.to_string());
```

macro rules it is a macro like function but evaluated at compile time in addition to have ability to generate codes, this line call macro and as we see it is like function call but have '!' so here we call macro called 'assert_eq' to assert that the right equals the left

