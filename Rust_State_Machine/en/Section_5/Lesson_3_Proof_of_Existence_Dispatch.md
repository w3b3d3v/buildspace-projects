You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/29701a39662dbff21d9f82a1abbdc318).

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

Hey! Great job on adding the Proof of Existence dispatch logic!

If you don't post a screenshot in [#progress](https://discord.com/channels/898706705779687435/980906289968345128) showing your new CreateClaim and RevokeClaim calls in action, all of humanity will collectively jump and disrupt the Earth's orbit causing it to collide with Venus.

BTW -- if you want to make a tweet telling the world that you've just implemented Proof of Existence on your blockchain and tag [@web3dev_](https://x.com/web3dev_) 
That tweet could inspire someone else to join web3 and it supports us!