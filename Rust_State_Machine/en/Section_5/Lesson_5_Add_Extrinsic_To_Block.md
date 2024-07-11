You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/81c56b7ae67dd2f4524d999e4879c05f).

# Add PoE Extrinsics to Blocks

The Proof Of Existence Pallet is fully integrated into your runtime at this point, but we aren't really using it.

Create some new `Block`s in your `fn main()` to test out the functionality of the Proof of Existence Pallet.

Be creative, and even feel free to introduce some extrinsics which will trigger errors based on the logic of your pallets.

Don't forget to increment your block number and actually call `execute_block` for each of those blocks.

Take a look at the final output and check that the state of your machine makes sense!

On `main.rs``

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

	// Here are the extrinsics in our block.
	// You can add or remove these based on the modules and calls you have set up.
	let block_1 = types::Block {
		header: support::Header { block_number: 1 },
		extrinsics: vec![
			support::Extrinsic {
				caller: alice.clone(),
				call: RuntimeCall::Balances(balances::Call::Transfer { to: bob, amount: 20 }),
			},
			support::Extrinsic {
				caller: alice,
				call: RuntimeCall::Balances(balances::Call::Transfer { to: charlie, amount: 20 }),
			},
		],
	};

	/*
		TODO:
		Create new block(s) which execute extrinsics for the new `ProofOfExistence` pallet.
			- Make sure to set the block number correctly.
			- Feel free to allow some extrinsics to fail, and see the errors appear.
	*/

	// Execute the extrinsics which make up our block.
	// If there are any errors, our system panics, since we should not execute invalid blocks.
	runtime.execute_block(block_1).expect("invalid block");
	/* TODO: Execute your new block(s). */

	// Simply print the debug format of our runtime state.
	println!("{:#?}", runtime);
}
```

### 🔥 On Fire
YOU DID IT AGAIN.

Section 5 is complete! Your hard work is paying off. Keep going :).