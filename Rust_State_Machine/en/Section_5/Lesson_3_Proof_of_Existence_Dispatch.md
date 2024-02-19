You can find the [solution for the previous step here]().

# Add Proof of Existence Dispatch

We have already established the nested dispatch pipeline for Pallets in the `Runtime`.

Let's build Pallet level dispatch logic for the Proof of Existence to take advantage of that.

# Exercises:

### Create Pallet Level Dispatch

There is nothing new here, but we have left more for you to fill out than before.

1. Create the variants for `CreateClaim` and `RevokeClaim` for your `Call` enum.
2. Implement the `Dispatch` trait for your `Pallet`.

If you get stuck, try not to look at the solution provided here, but instead look at what you did in the Balances Pallet. Everything we have done here, we have already done in the past. This is an opportunity to catch where you may have outstanding questions or misunderstandings.

On `proof_of_existence.rs`:

```rust
/// ... previous code.

// A public enum which describes the calls we want to expose to the dispatcher.
// We should expect that the caller of each call will be provided by the dispatcher,
// and not included as a parameter of the call.
pub enum Call<T: Config> {
	/*
		TODO:
		Create variants for:
		- `CreateClaim`
		- `RevokeClaim`

		Remember that you only need to pass in the `claim` data, as `caller` information is passed
		in through the `dispatch` logic.
	*/
	RemoveMe(core::marker::PhantomData<T>),
}

/// Implementation of the dispatch logic, mapping from `POECall` to the appropriate underlying
/// function we want to execute.
impl<T: Config> crate::support::Dispatch for Pallet<T> {
	/*
		TODO:
		Implement `crate::support::Dispatch` for `Pallet<T>`.

		In your `dispatch` logic, match on `call` and forward the `caller` and `claim` data to the
		appropriate function.
	*/
}

/// ... previous code.
```