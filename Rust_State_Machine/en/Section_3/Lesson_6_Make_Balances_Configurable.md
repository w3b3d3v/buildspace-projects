You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/e3330a586bff7e01cc4ab920cab026be).

# Make Balances Configurable

[Youtube](https://www.youtube.com/watch?v=3CRrFloyaRU)

There is nothing new to learn in this step, just repeating the same process we did for our System Pallet for the Balances pallet.

In this case, our `Config` trait will only have two associated types: `AccountId` and `Balance`.

For this step, try to avoid looking at the solution, and instead refer to the changes you made to get the System Pallet configurable.

# Exercises:

1. Define the `Config` trait which will have your associated types.
2. Make sure these types have their trait constraints defined in `Config`.
3. Update your `struct Pallet` to use `T: Config` and reference your types using the `T::` syntax.
4. Update all of your functions to use the `T::` syntax.
5. Update your test, creating a struct `TestConfig`, and implementing `Config` for it, and using it to instantiate your `Pallet` struct.
6. Go to your `main.rs` file, and implement `balances::Config` for the `Runtime` struct.
7. Update your `Runtime` definition to instantiate `balances::Pallet` with `Self`.

If you have made it this far, I think it is fair to say you have made it over the hardest part of this tutorial, and the hardest part of using Rust in Substrate.

It is important to take a step back and remember that while these abstractions make your code a bit more complicated to fully understand, it also makes your code extremely flexible, at zero cost to performance and safety thanks to the Rust compiler.

On `main.rs`:

```rust
mod balances;
mod system;

// These are the concrete types we will use in our simple state machine.
// Modules are configured for these types directly, and they satisfy all of our
// trait requirements.
mod types {
	pub type AccountId = String;
	pub type Balance = u128;
	pub type BlockNumber = u32;
	pub type Nonce = u32;
}

// This is our main Runtime.
// It accumulates all of the different pallets we want to use.
#[derive(Debug)]
pub struct Runtime {
	system: system::Pallet<Self>,
	balances: balances::Pallet<types::AccountId, types::Balance>,
}

impl system::Config for Runtime {
	type AccountId = types::AccountId;
	type BlockNumber = types::BlockNumber;
	type Nonce = types::Nonce;
}

/*
	TODO:
	Implement the `balances::Config` trait you created on your `Runtime`.
	Use `Self` to satisfy the generic parameter required for `balances::Pallet`.
*/

impl Runtime {
	// Create a new instance of the main Runtime, by creating a new instance of each pallet.
	fn new() -> Self {
		Self { system: system::Pallet::new(), balances: balances::Pallet::new() }
	}
}
```

On `balances.rs`:

```rust
use num::traits::{CheckedAdd, CheckedSub, Zero};
use std::collections::BTreeMap;

/*
	TODO: Combine all generic types and their trait bounds into a single `pub trait Config`.
	When you are done, your `Pallet` can simply be defined with `Pallet<T: Config>`.
*/

/// This is the Balances Module.
/// It is a simple module which keeps track of how much balance each account has in this state
/// machine.
#[derive(Debug)]
pub struct Pallet<AccountId, Balance> {
	// A simple storage mapping from accounts to their balances.
	balances: BTreeMap<AccountId, Balance>,
}

/*
	TODO: Update all of these functions to use your new configuration trait.
*/

/// ... previous code.

#[cfg(test)]
mod tests {
	/*
		TODO: Create a `struct TestConfig`, and implement `super::Config` on it with concrete types.
		Use this struct to instantiate your `Pallet`.
	*/

    /// ... previous code.
}
```