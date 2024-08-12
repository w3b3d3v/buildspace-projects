Before moving on, you can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/afd212bee56108f0cd87c712ad5d3fdf).

Congratulations on reaching section 2! ;)

# The System and Runtime

In this section, you will create the System Pallet, a low level Pallet for managing basic blockchain state.

Then you will integrate both the Balances Pallet and System Pallet into your state transition function, called the Runtime.

# Introduce the System Pallet

[Youtube](https://www.youtube.com/watch?v=8Tyhf6jtHpI)

We have basically completed the creation of a basic Balances Pallet. This is the pallet that most users will interact with.

However, your blockchain usually needs to keep track of many other pieces of data to function properly.

For this, we will create a new pallet called the System Pallet.

## What is the System Pallet?

The System Pallet is a "meta"-pallet which stores all the metadata needed for your blockchain to function. For example, the current blocknumber or the nonce of users on your blockchain.

This pallet does not need to expose any functions to end users, but can still play an important role in our overall state transition function.

We will see the importance of the System Pallet evolve as you walk through the steps of building it.

## Create the System Pallet

1. Create a new file `src/system.rs` in your project.
2. Copy the starting template provided, then complete the steps outlined by the template code.
3. Import the `system` module into your `main.rs` file.

You will notice that the instructions here are quite brief. You have already done all of these steps before, so you should already be familiar with everything you need to complete this step.


## Exercises:

Create file `src/system.rs`:
```rust
/* TODO: You might need to update your imports. */

/// This is the System Pallet.
/// It handles low level state needed for your blockchain.
pub struct Pallet {
	/// The current block number.
	/* TODO: Create a field `block_number` that stores a `u32`. */
	/// A map from an account to their nonce.
	/* TODO: Create a field `nonce` that is a `BTreeMap` from `String` to `u32`. */
}

impl Pallet {
	/// Create a new instance of the System Pallet.
	pub fn new() -> Self {
		/* TODO: Return a new instance of the `Pallet` struct. */
	}
}
```
On `main.rs`:
```rust
mod balances;
/* TODO: Import your new `system` module. */

fn main() {
	println!("Hello, world!");
}
```

It's going to be epic. :) Let's do this!