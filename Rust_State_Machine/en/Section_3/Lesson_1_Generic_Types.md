Before you go any further, if you want to check that your answer matches [solution for the previous step here](https://gist.github.com/nomadbitcoin/fe73ad648ad4cd64c782fdc85ceb5791).   

Fantastic! You now understand the importance of the Genesis State and are ready to take the first steps in your blockchain!

# Generic and Configurable Types

In this section, we will be harnessing the full power of Rust to create a generic and configurable Runtime.

There will be no real logical changes happening in the next steps.

Instead, we will be gradually abstracting away the concrete types defined in our Pallets, and instead structure our code to handle purely generic types.

At the end of the section, you will have a project whose structure exactly mirrors what is found in the Polkadot SDK and understand how all of it works.

# Using Named Types

Up till now, we have just been hardcoding raw types into our structs and function definitions.

There are already examples where this can be confusing, for example if you see a function accept a `u32` parameter, is it a `blocknumber` or a `nonce`?

To make our code more clear, let's extract all of our raw types and define custom named types for our structs and functions.

Across the Balances and System Pallet, we need to define the following types:

1. `type AccountId = String;`
2. `type Balance = u128;`
3. `type Nonce = u32;`
4. `type BlockNumber = u32;`

Note that extracting these types into common type definitions also allows us to update the types more easily if we choose to.

As we go further into this tutorial, we will show you how we can make these type definitions even more flexible and customizable in the context of building a blockchain SDK for developers like yourself.

# Exercise:

### Create Custom Types

Follow the `TODO`s in the template to add these type definitions to each of your Pallets, and update all of your structs and functions to use these types.

On `balances.rs`:
```rust
use std::collections::BTreeMap;

/*
	TODO: Define the common types used in this pallet:
		- `AccountID`
		- `Balance`

	Then update this pallet to use these common types.
*/

/// This is the Balances Module.
/// It is a simple module which keeps track of how much balance each account has in this state
/// machine.
#[derive(Debug)]
pub struct Pallet {
	// A simple storage mapping from accounts (`String`) to their balances (`u128`).
	balances: BTreeMap<String, u128>,
}
```
On `system.rs`:
```rust
use std::collections::BTreeMap;

/*
	TODO: Define the common types used in this pallet:
		- `AccountID`
		- `BlockNumber`
		- `Nonce`

	Then update this pallet to use these common types.
*/

/// This is the System Pallet.
/// It handles low level state needed for your blockchain.
#[derive(Debug)]
pub struct Pallet {
	/// The current block number.
	block_number: u32,
	/// A map from an account to their nonce.
	nonce: BTreeMap<String, u32>,
}
```