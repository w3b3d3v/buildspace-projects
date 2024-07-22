You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/e818e17f1833fc1a2fe96c224c0bf2d4).

# Using Execute Block

[Youtube](https://www.youtube.com/watch?v=TOJXnXNuLFM)

We have now successfully implemented the `execute_block` and `dispatch` logic needed to build and execute real `Blocks`.

Let's bring that logic into our `main` function.

## Creating a Block

You can create a new `Block` by filling out all the fields of the struct and assigning it to a variable.

For example:

```rust
let block_1 = types::Block {
	header: support::Header { block_number: 1 },
	extrinsics: vec![
		support::Extrinsic {
			caller: &"alice",
			call: RuntimeCall::BalancesTransfer { to: &"bob", amount: 69 },
		},
	],
};
```

It is important that you set the block number correctly since we verify this in our `execute_block` function. The first block in our state machine will have the number `1`.

Also remember that you can add multiple extrinsics in a single block by extending the vector.

## Executing a Block

Once you have constructed your `Block`, you can pass it to the `execute_block` function implemented on your `runtime`.

```rust
runtime.execute_block(block_1).expect("invalid block");
```

Note how we panic with the message `"invalid block"` if the `execute_block` function returns an error. This should only happen when something is seriously wrong with your block, for example the block number is incorrect for what we expect.

This panic will NOT be triggered if there is an error in an extrinsic, as we "swallow" those errors in the `execute_block` function. This is the behavior we want.

# Exercises:

### Update Your Main Function

Go ahead and use the `Block` type and `execute_block` function to update the logic of your `main` function.

Follow the `TODO`s provided in the template to complete this step

On `main.rs`:

```rust
/// ... previous code.

fn main() {
	// Create a new instance of the Runtime.
	// It will instantiate with it all the modules it uses.
	let mut runtime = Runtime::new();
	let alice = "alice".to_string();
	let bob = "bob".to_string();
	let charlie = "charlie".to_string();

	// Initialize the system with some initial balance.
	runtime.balances.set_balance(&alice, 100);

	// start emulating a block
	runtime.system.inc_block_number();
	assert_eq!(runtime.system.block_number(), 1);

	// first transaction
	runtime.system.inc_nonce(&alice);
	let _res = runtime
		.balances
		.transfer(alice.clone(), bob, 30)
		.map_err(|e| eprintln!("{}", e));

	// second transaction
	runtime.system.inc_nonce(&alice);
	let _res = runtime.balances.transfer(alice, charlie, 20).map_err(|e| eprintln!("{}", e));

	/*
		TODO: Replace the logic above with a new `Block`.
			- Set the block number to 1 in the `Header`.
			- Move your existing transactions into extrinsic format, using the
			  `Extrinsic` and `RuntimeCall`.
	*/

	/*
		TODO:
		Use your `runtime` to call the `execute_block` function with your new block.
		If the `execute_block` function returns an error, you should panic!
		We `expect` that all the blocks being executed must be valid.
	*/

	// Simply print the debug format of our runtime state.
	println!("{:#?}", runtime);
}
```