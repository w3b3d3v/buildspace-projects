You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/d73d5173a7f5d40909bad87604b76270).

# Proof Of Existence Functions

[Youtube](https://www.youtube.com/watch?v=I8W3GQU3Tyw)

The Proof of Existence Pallet is quite simple, so let's build out the logic needed.

## Get Claim

Our Pallet has a simple storage map from some claim content to the owner of that claim.

The `get_claim` function should act as a simple read function returning the `T::AccountId` of the owner, if there is any. In the case we query a claim which has no owner, we should return `None`.

This is not a function that a user would call from an extrinsic, but is useful for other parts of your state machine to access the data in this Pallet.

## Create Claim

Any user can add a new claim to the Proof of Existence Pallet.

The only thing that is important is that we check that the claim has not already been made by another user.

Each claim should only have one owner, and whoever makes the claim first gets priority.

You can check if some claim is already in the `claims` storage using the `contains_key` api:

```rust
if self.claims.contains_key(&claim) {
	return Err(&"this content is already claimed");
}
```

## Revoke Claim

Data on the blockchain is not free, and in fact is very expensive to maintain. Giving users the ability to clean up their data is not only good, but encouraged. If a user no longer has a need to store their claim on chain, they should clean it up.

Furthermore, the history of the blockchain is immutable. Even if the data about a claim does not exist in the "current state", it can be shown to have existed in the past.

Keeping things in the current state just makes querying for information easier.

To revoke a claim, we need to check two things:

1. The the claim exists.
2. That the person who wants to revoke the claim is the owner of that claim.

You should be able to handle all of this logic by calling the `get_claim` function and using `ok_or` to return an error when the claim does not exist. If the claim does exist, you should be able to directly extract the owner from the state query.

# Exercises:

### Build Your Functions

Complete the `TODO`s outlined in the template.

Afterward, create a `basic_proof_of_existence` test to check that all your functions are working as expected.

This includes both the success and possible error conditions of your Pallet.

On `proof_of_existence.rs`:

```rust
use crate::support::DispatchResult;
use core::fmt::Debug;
use std::collections::BTreeMap;

pub trait Config: crate::system::Config {
	/// The type which represents the content that can be claimed using this pallet.
	/// Could be the content directly as bytes, or better yet the hash of that content.
	/// We leave that decision to the runtime developer.
	type Content: Debug + Ord;
}

/// This is the Proof of Existence Module.
/// It is a simple module that allows accounts to claim existence of some data.
#[derive(Debug)]
pub struct Pallet<T: Config> {
	/// A simple storage map from content to the owner of that content.
	/// Accounts can make multiple different claims, but each claim can only have one owner.
	claims: BTreeMap<T::Content, T::AccountId>,
}

impl<T: Config> Pallet<T> {
	/// Create a new instance of the Proof of Existence Module.
	pub fn new() -> Self {
		Self { claims: BTreeMap::new() }
	}

	/// Get the owner (if any) of a claim.
	pub fn get_claim(&self, claim: &T::Content) -> Option<&T::AccountId> {
		/* TODO: `get` the `claim` */
		unimplemented!()
	}

	/// Create a new claim on behalf of the `caller`.
	/// This function will return an error if someone already has claimed that content.
	pub fn create_claim(&mut self, caller: T::AccountId, claim: T::Content) -> DispatchResult {
		/* TODO: Check that a `claim` does not already exist. If so, return an error. */
		/* TODO: `insert` the claim on behalf of `caller`. */
		Ok(())
	}

	/// Revoke an existing claim on some content.
	/// This function should only succeed if the caller is the owner of an existing claim.
	/// It will return an error if the claim does not exist, or if the caller is not the owner.
	pub fn revoke_claim(&mut self, caller: T::AccountId, claim: T::Content) -> DispatchResult {
		/* TODO: Get the owner of the `claim` to be revoked. */
		/* TODO: Check that the `owner` matches the `caller`. */
		/* TODO: If all checks pass, then `remove` the `claim`. */
		Ok(())
	}
}

#[cfg(test)]
mod test {
	struct TestConfig;

	impl super::Config for TestConfig {
		type Content = &'static str;
	}

	impl crate::system::Config for TestConfig {
		type AccountId = &'static str;
		type BlockNumber = u32;
		type Nonce = u32;
	}

	#[test]
	fn basic_proof_of_existence() {
		/*
			TODO:
			Create an end to end test verifying the basic functionality of this pallet.
				- Check the initial state is as you expect.
				- Check that all functions work successfully.
				- Check that all error conditions error as expected.
		*/
	}
}
```

Implementing the Proof of Existence functions is a critical step in building a functional blockchain module. Great work on completing this lesson!

If you get stuck or feel lost with creating, getting, or revoking claims, don't be shy to ask for help in the [#🆘・section-5](https://discord.com/channels/898706705779687435/1261079360341934161) channel on Discord. Even superheroes need a hand sometimes! 🦸‍♂️🦸‍♀️