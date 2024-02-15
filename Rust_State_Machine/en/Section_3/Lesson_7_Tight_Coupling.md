You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/0c6e73f0bdd65cac4eea182985930d7a).

# Tight Coupling

You might have noticed some redundancy when making our pallets generic and configurable. Both pallets defined an `AccountId` type, and technically we could define their concrete type differently!

We wouldn't want this on a real production blockchain. Instead, we would want to define common types in a single spot, and use that everywhere.

## Trait Inheritance

Rust has the ability for traits to inherit from one another. That is, that for you to implement some trait, you also need to implement all traits that it inherits.

Let's look at some examples.

### Trait Functions

We can extend our previous example to show what trait inheritance does with functions

```rust
pub trait GetName {
	// returns a string representing the object's name
	fn name() -> String;
}

pub trait SayName: GetName {
	// will print the name from `name()` to console
	fn say_name() {
		println!("{}", Self::name());
	}
}
```

Note how in the definition of `trait SayName`, we reference `GetName` after a colon. This `SayName`, your object must also implement `GetName`. Note that we could even program a "default" implementation of `get_name` by using the `Self::name()` function.

So when we implement these traits, it looks like:

```rust
struct Shawn;
impl GetName for Shawn {
	fn name() -> String {
		return "shawn".to_string();
	}
}

impl SayName for Shawn {}
```

We could choose to implement our own version of the `SayName` function, for example like:

```rust
impl SayName for Shawn {
	fn say_name() {
		println!("My name is {}!", Self::name());
	}
}
```

But we don't have to do this. What we do have to do is make sure that `GetName` is implemented for `Shawn` or you wont be able to use the `SayName` trait. Again, we won't be using this in our tutorial, but it is nice to see examples of how this can be used.

### Associated Types

Rather than redefining `type AccountId` in each Pallet that needs it, what if we just defined it in `system::Config`, and inherit that type in other Pallet configs?

Let's see what that would look like:

```rust
pub trait Config: crate::system::Config {
	type Balance: Zero + CheckedSub + CheckedAdd + Copy;
}
```

Here you can see our `balances::Config` trait is inheriting from our `crate::system::Config` trait. This means that all types defined by `system::Config`, including the `AccountId`, is accessible through the `balances::Config` trait. Because of this, we do not need to redefine the `AccountId` type in `balances::Config`.

In the Polkadot SDK ecosystem, we call this "tight coupling" because a runtime which contains the Balances Pallet must also contain the System Pallet. In a sense these two pallets are tightly coupled to one another. In fact, with Substrate, all pallets are tightly coupled to the System Pallet, because the System Pallet provides all the meta-types for your blockchain system.

# Exercises:

### Tightly Couple Balances To System

Let's remove the redundant `AccountId` definition from the Balances Pallet `Config`.

1. Inherit the `crate::system::Config` trait in the `balances::Config` trait.
2. Remove the `AccountId` type from your `balances::Config` definition.
3. Implement `crate::system::Config` for `TestConfig`.
4. In `main.rs`, simply remove `type AccountId` from `balances::Config`.

On `main.rs`:

```rust
/// ... previous code.

impl balances::Config for Runtime {
	/* TODO: After inheriting from the `system::Config` trait, you won't need `AccountId` here. */
	type AccountId = types::AccountId;
	type Balance = types::Balance;
}

/// ... previous code.
```

On `balances.rs`:

```rust
use num::traits::{CheckedAdd, CheckedSub, Zero};
use std::collections::BTreeMap;

/// The configuration trait for the Balances Module.
/// Contains the basic types needed for handling balances.
/*
	TODO:
	Tightly couple balances to the system pallet by inheriting the `system::Config` trait.
	After this, you won't need the `AccountId` type redefined here.
*/
pub trait Config {
	/// A type which can identify an account in our state machine.
	/// On a real blockchain, you would want this to be a cryptographic public key.
	type AccountId: Ord + Clone;
	/// A type which can represent the balance of an account.
	/// Usually this is a large unsigned integer.
	type Balance: Zero + CheckedSub + CheckedAdd + Copy;
}

/// ... previous code.

#[cfg(test)]
mod tests {
	struct TestConfig;

	/* TODO: Implement `crate::system::Config` for `TestConfig` to make your tests work again. */

	impl super::Config for TestConfig {
		type AccountId = String;
		type Balance = u128;
	}

	/// ... previous code.
    
}
```