You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/a86959a1c50628cb2af1bdde52d55199).

# Pallet Level Dispatch

We want to make our code more modular and extensible.

Currently, all dispatch happens through the `RuntimeCall`, which is hardcoding dispatch logic for each of the Pallets in our system.

What we would prefer is for Pallet level dispatch logic to live in the Pallet itself, and our Runtime taking advantage of that. We ave already seen end to end what it takes to set up call dispatch, so let's do it again at the Pallet level.

## Pallet Call

To make our system more extensible, we want to keep all the calls for a pallet defined at the pallet level.

For this, we define an `enum Call` in our Balances pallet, and just like before, we introduce a new enum variant representing the function that we want to call.

Note that this enum needs to be generic over `T: Config` because we need access to the types defined by our configuration trait!

## Pallet Dispatch

You will also notice in the template, we have included the shell for you to implement Pallet level dispatch.

Everything should look the same as the Runtime level dispatch, except the `type Call` is the Pallet level call we just created.

Just like before, you simply need to match the `Call` variant with the appropriate function, and pass the parameters needed by the function.

Exercises:

### Create Your Pallet Level Dispatch

Follow the `TODO`s in the template to complete the logic for Pallet level dispatch.

In the next step, we will use this logic to improve our dispatch logic in our Runtime.

On `balances.rs`:

```rust
/// ... previous code.

// A public enum which describes the calls we want to expose to the dispatcher.
// We should expect that the caller of each call will be provided by the dispatcher,
// and not included as a parameter of the call.
pub enum Call<T: Config> {
	/* TODO: Create an enum variant `Transfer` which contains named fields:
		- `to`: a `T::AccountId`
		- `amount`: a `T::Balance`
	*/
	/* TODO: Remove the `RemoveMe` placeholder. */
	RemoveMe(core::marker::PhantomData<T>),
}

/// Implementation of the dispatch logic, mapping from `BalancesCall` to the appropriate underlying
/// function we want to execute.
impl<T: Config> crate::support::Dispatch for Pallet<T> {
	type Caller = T::AccountId;
	type Call = Call<T>;

	fn dispatch(
		&mut self,
		caller: Self::Caller,
		call: Self::Call,
	) -> crate::support::DispatchResult {
		/* TODO: use a `match` statement to route the `Call` to the appropriate pallet function. */
		Ok(())
	}
}

/// ... previous code.
```