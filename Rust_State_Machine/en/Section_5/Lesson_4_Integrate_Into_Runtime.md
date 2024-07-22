You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/4a8f9dec88337d3b6f40e4d6840dc305).

# Integrate PoE Into Your Runtime

[Youtube](https://www.youtube.com/watch?v=Ma7MzDVqjXY)

The Proof of Existence pallet is done, but we still need to integrate it into your Runtime.

Let's take a look at that process.

## Integration Steps

1. The first place to start is adding the `proof_of_existence` field to your `struct Runtime`.
2. Next you need to update your `fn new()` to also initialize `proof_of_existence`.
3. After, create a new concrete `type Content` which is a `&'static str`. As mentioned, normally this would be a hash, but for simplicity we are once again a simple static string.

	> If you want to use a hash now or in the future, it would be as simple as updating this one line to change all the types in your Runtime and Pallet. That is the kind of flexibility we have been working toward!

4. Then, implement `proof_of_existence::Config` for `Runtime`, using your `types::Content`.
5. At this point, things should already compile successfully, so use this as a checkpoint.
6. Introduce a new variant `ProofOfExistence` for the `RuntimeCall`.
7. Finally, update your `fn dispatch` logic to handle re-dispatching `ProofOfExistence` calls to the `proof_of_existence::Pallet`.

Hopefully from this process, you can see how all of the abstractions we have introduced has made integrating new Pallets into your runtime quite easy.

We will make this process even easier in the near future using macros!

On `main.rs`:

```rust
/// ... previous code.

// These are the concrete types we will use in our simple state machine.
// Modules are configured for these types directly, and they satisfy all of our
// trait requirements.
mod types {
	pub type AccountId = String;
	pub type Balance = u128;
	pub type BlockNumber = u32;
	pub type Nonce = u32;
	pub type Extrinsic = crate::support::Extrinsic<AccountId, crate::RuntimeCall>;
	pub type Header = crate::support::Header<BlockNumber>;
	pub type Block = crate::support::Block<Header, Extrinsic>;
	/* TODO: Add the concrete `Content` type for your runtime. */
}

// These are all the calls which are exposed to the world.
// Note that it is just an accumulation of the calls exposed by each module.
pub enum RuntimeCall {
	Balances(balances::Call<Runtime>),
	/* TODO: Add a `ProofOfExistence` variant to access `proof_of_existence::Call`. */
}

// This is our main Runtime.
// It accumulates all of the different pallets we want to use.
#[derive(Debug)]
pub struct Runtime {
	system: system::Pallet<Self>,
	balances: balances::Pallet<Self>,
	/* TODO: Add `proof_of_existence` field to your `Runtime`. */
}

/// ... previous code.

/* TODO: Implement proof_of_existence::Config` for `Runtime`. */

impl Runtime {
	// Create a new instance of the main Runtime, by creating a new instance of each pallet.
	fn new() -> Self {
		Self {
			system: system::Pallet::new(),
			balances: balances::Pallet::new(),
			/* TODO: Initialize the `proof_of_existence` pallet. */
		}
	}

    /// ... previous code.
}

impl crate::support::Dispatch for Runtime {
	type Caller = <Runtime as system::Config>::AccountId;
	type Call = RuntimeCall;
	// Dispatch a call on behalf of a caller. Increments the caller's nonce.
	//
	// Dispatch allows us to identify which underlying module call we want to execute.
	// Note that we extract the `caller` from the extrinsic, and use that information
	// to determine who we are executing the call on behalf of.
	fn dispatch(
		&mut self,
		caller: Self::Caller,
		runtime_call: Self::Call,
	) -> support::DispatchResult {
		// This match statement will allow us to correctly route `RuntimeCall`s
		// to the appropriate pallet level function.
		match runtime_call {
			RuntimeCall::Balances(call) => {
				self.balances.dispatch(caller, call)?;
			},
			/* TODO: Dispatch `calls` to the `ProofOfExistence` pallet. */
		}
		Ok(())
	}
}

/// ... previous code.
```