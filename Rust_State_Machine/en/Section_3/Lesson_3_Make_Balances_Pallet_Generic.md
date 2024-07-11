# Make Balances Pallet Generic

Our goal over the next few steps will be to continually make our runtime more generic and configurable over the types we use in our Pallets.

## Why Generic?

The flexibility of generic runtime means that we can write code which works for multiple different configurations and types.

For example, up until now, we have been using `&'static str` to represent the accounts of users. This is obviously not the right thing to do, but is easy to implement for a basic blockchain tutorial like this.

What would you need to change in order to use more traditional cryptographic public keys?

Well, currently there are definitions of the account type in both the Balances Pallet and the System Pallet. Imagine if you had many more Pallets too! Such refactoring could be very difficult, but also totally avoided if we used generic types to begin with.

Truthfully, the advantage of generic types will not be super obvious in this tutorial, but when building a blockchain SDK like the Substrate, this kind of flexibility will allow ecosystem developers to reach their full potential.

For example, teams have used Substrate to build fully compatible Ethereum blockchains, while other teams have experimented with cutting edge cryptographic primitives. This generic framework allows both teams to be successful.

## Generic Types

You have already been lightly exposed to generic types with the `Result` type. Remember that this type is flexible to allow for you to configure what type is returned when there is `Ok` or `Err`.

If we wanted to make our `Pallet` generic, it would look something like:

```rust
pub struct Pallet<AccountId, Balance> {
 	balances: BTreeMap<AccountId, Balance>,
}
```

And implementing functions on `Pallet` would look like:

```rust
impl<AccountId, Balance> Pallet<AccountId, Balance> {
	// functions which use these types
}
```

In this case, we have not defined what the `AccountId` and `Balance` type are concretely, just that we will be storing a `BTreeMap` where the `AccountId` type is a key and `Balance` type is a value.

### Trait Constraints

The `Result` generic type is extremely flexible because there are no constraints on what the `Ok` or `Err` type has to be. Every type will work for this situation.

However, our Pallets are not that flexible. The `Balance` type cannot literally be any type. Because we have functions like `fn transfer`, we must require that the `Balance` type at least has access to the function `checked_sub`, `checked_add`, and has some representation of `zero`.

This is where the `num` crate will come in hand. From the `num` crate, you can import traits which define types which expose these functions:

```rust
use num::traits::{CheckedAdd, CheckedSub, Zero};
```

Then, where applicable, you need to constrain your generic types to have these traits.

That will look like:

```rust
impl<AccountId, Balance> Pallet<AccountId, Balance>
where
	AccountId: Ord,
	Balance: Zero + CheckedSub + CheckedAdd + Copy,
{
	// functions which use these types and have access to the traits specified
}
```

You will notice other types like `Copy` and `Ord` that have been added. These constrains come from using structures like the `BTreeMap`, which requires that the key type is "orderable".

You can actually try compiling your code without these type constraints, and the compiler will tell you which traits you are missing, and what you need to include.

### Instantiating a Generic Type

The final piece of the puzzle is instantiating our generic types.

Previously we could simply write:

```rust
let mut balances = super::Pallet::new();
```

But now that `Pallet` is generic, we need to concretely define those types when we instantiate it.

That syntax looks like:

```rust
let mut balances = super::Pallet::<&'static str, u128>::new();
```

You will notice that now the types are defined wherever the generic `struct Pallet` is being instantiated. This means that you can extract the types out of your Pallets, and move them into the Runtime.

# Exercises:

### Get Generic!

Its time to turn your balances pallet generic.

1. Follow the `TODO`s in the `balances.rs` file to make `Pallet` generic.
2. Move the type definitions for `AccountId` and `Balance` to your `main.rs`.
3. Update your `struct Runtime` to use these types when defining the `balances::Pallet`.

To be honest, this is one of the places that developers most frequently have problems when learning Rust, which is why there is such an emphasis on teaching you and having you learn by doing these steps yourself.

Don't be afraid in this step to peek at the solution if you get stuck, but do try and learn the patterns of using generic types, and what all the syntax means in terms of what the compiler is trying to guarantee about type safety.

On `balances.rs`:

```rust
/* TODO: You might need to import some stuff for this step. */
use std::collections::BTreeMap;

type AccountId = String;
type Balance = u128;

/*
	TODO:
	Update the `Pallet` struct to be generic over the `AccountId` and `Balance` type.

	You won't need the type definitions above after you are done.
	Types will now be defined in `main.rs`. See the TODOs there.
*/

/// This is the Balances Module.
/// It is a simple module which keeps track of how much balance each account has in this state
/// machine.
#[derive(Debug)]
pub struct Pallet {
	// A simple storage mapping from accounts to their balances.
	balances: BTreeMap<AccountId, Balance>,
}

/*
	TODO:
	The generic types need to satisfy certain traits in order to be used in the functions below.
		- AccountId: Ord
		- Balance: Zero + CheckedSub + CheckedAdd + Copy

	You could figure these traits out yourself by letting the compiler tell you what you're missing.

	NOTE: You might need to adjust some of the functions below to satisfy the borrow checker.
*/

/// ... previous code.

#[cfg(test)]
mod tests {
	#[test]
	fn init_balances() {
		/*
			TODO:
			When creating an instance of `Pallet`, you should explicitly define the types you use.
		*/
		let mut balances = super::Pallet::new();

		/// ... previous code.
	}

	#[test]
	fn transfer_balance() {
		/*
			TODO:
			When creating an instance of `Pallet`, you should explicitly define the types you use.
		*/
		let mut balances = super::Pallet::new();

		/// ... previous code.
	}
}
```

On `main.rs`:
```rust
mod balances;
mod system;

// These are the concrete types we will use in our simple state machine.
// Modules are configured for these types directly, and they satisfy all of our
// trait requirements.
mod types {
	/*
		TODO: Move your type definitions for `AccountId` and `Balance` here.
	*/
}

// This is our main Runtime.
// It accumulates all of the different pallets we want to use.
#[derive(Debug)]
pub struct Runtime {
	system: system::Pallet,
	/* TODO: Use your type definitions for your new generic `balances::Pallet`. */
	balances: balances::Pallet,
}
```

Making the Balances Pallet generic is a crucial skill for creating flexible and scalable blockchain solutions. Great work on completing this lesson! 🌟

Post a screenshot in [#progress](https://discord.com/channels/898706705779687435/980906289968345128) showing your runtime with the new generic Balances Pallet in action. It better not still be using &'static str!