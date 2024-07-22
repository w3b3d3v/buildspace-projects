You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/742dd51b323d9a12c6d8c598c7aaf3da).

# Exercises:

### Make System Pallet Generic

[Youtube](https://www.youtube.com/watch?v=uQFrV3OowTw)

Now that you have some practice with the Balances Pallet, let's do the same task for the System Pallet.

1. In this case we need to make System generic over `AccountId`, `BlockNumber`, and `Nonce`.

2. You will also need to figure out the trait constraints needed for these types to be compatible with the logic you have previously written. The compiler is your friend here to help you navigate everything.

3. Update your tests.

4. Finally move your type definitions to your `main.rs` file and update your `Runtime`.

Make sure everything compiles and all tests pass after this step.

If you need help, I recommend to look at your Balances Pallet rather than the solution for this step. All of the patterns are the same as before, so it is better that you start to connect the dots yourself rather than relying on the solution.

If you struggled here, it is a good opportunity to take a pause and re-review generic types from other examples across the Rust ecosystem.

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
	/* TODO: Move your type definitions for `BlockNumber` and `Nonce` here. */
}

// This is our main Runtime.
// It accumulates all of the different pallets we want to use.
#[derive(Debug)]
pub struct Runtime {
	/* TODO: Use your type definitions for your new generic `system::Pallet`. */
	system: system::Pallet,
	balances: balances::Pallet<types::AccountId, types::Balance>,
}
```

On `system.rs`:

```rust
use std::collections::BTreeMap;

type AccountId = String;
type BlockNumber = u32;
type Nonce = u32;

/*
	TODO:
	Update the `Pallet` struct to be generic over the `AccountId`, `BlockNumber`, and `Nonce` type.
	You won't need the type definitions above after you are done.
	Types will now be defined in `main.rs`. See the TODOs there.
*/

/// This is the System Pallet.
/// It handles low level state needed for your blockchain.
#[derive(Debug)]
pub struct Pallet {
	/// The current block number.
	block_number: BlockNumber,
	/// A map from an account to their nonce.
	nonce: BTreeMap<AccountId, Nonce>,
}

/*
	TODO:
	The generic types need to satisfy certain traits in order to be used in the functions below.
	See if you can figure them out yourself.

	NOTE: You might need to adjust some of the functions below to satisfy the borrow checker.
*/

/// ... previous code.

#[cfg(test)]
mod test {
	#[test]
	fn init_system() {
		/*
			TODO:
			When creating an instance of `Pallet`, you should explicitly define the types you use.
		*/
		let mut system = super::Pallet::new();
		
        /// ... previous code.
	}
}
```