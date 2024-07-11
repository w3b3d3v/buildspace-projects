You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/e5f04fe5925df501f7760f18b45b78cb).

# Using Our Runtime

Until now, we have just been scaffolding parts of our blockchain. Tests have ensured that the code we have written so far make sense, but we haven't actually USED any of the logic we have written for our `main` program.

Let's change that by using our Runtime and actually executing logic on our blockchain.

## Simulating a Block

The input to any blockchain state transition function is a block of transactions.

Later in the tutorial we will actually spend more time to build proper blocks and execute them, but for now, we can "simulate" all the basics of what a block would do by individually calling the functions our Pallets expose.

### Genesis State

The state of your blockchain will propagate from block to block. This means if Alice received 100 tokens on block 4, that she can transfer at least 100 tokens on block 5, and so on.

But how do users get any balance to begin with?

The answer to this question can be different for different blockchains, but in general most modern blockchains start with a Genesis State. This is the starting state of your blockchain on "block 0".

This means anything set in the genesis state can be used on block 1, and can bootstrap your blockchain to being functional.

In our situation, you can simply call low level functions like `set_balance` before we simulate our first block to establish our genesis state.

### Steps of a Basic Block

Let's quickly break down the steps of executing a basic block:

1. First we increment the blocknumber, since each new block will have a new blocknumber.
2. Then we go through an execute each transaction in that block:
	1. Each transaction for our blockchain will come from a user, thus we will increment the users nonce as we process their transaction.
	2. Then we will attempt to execute the function they want to call, for example `transfer`.
	3. Repeat this process for every transaction.

### Handling Errors

The `main()` function in Rust cannot propagate or handle errors itself. Either everything inside of it is handled, or you will have to trigger a `panic`.

As you have already learned, triggering a `panic` is generally not good, but may be the only thing you can do if something is seriously wrong. For our blockchain, the only thing which can really cause a panic is importing a block which does not match the expected blocknumber. There is nothing in this case we can do to "handle" this error. If someone is telling us to execute the wrong block, then we have some larger problem with our overall system that needs to be fixed.

However, users can also submit transactions which result in an error. For example, Alice trying to send more funds than she has in her account.

Should we panic?

Absolutely not! This is the kind of error that our runtime should be able to handle since it is expected that such errors would occur. **A block can be valid even if transactions in the block are invalid!**

When a transaction returns an error we should show that error to the user, and then "swallow" the result. For example:

```rust
let _res = i_can_return_error().map_err(|e| eprintln!("{}", e));
```

In this case, you can see that any error that `i_can_return_error` would return gets printed to the console, but otherwise, the `Result` of that function gets placed in an unused variable `_res`.

You should be VERY CAREFUL when you do this. Swallowing an error is exactly the opposite of proper error handling that Rust provides to developers. However, we really do not have a choice here in our `main` function, and we fully understand what we are doing here.

On real blockchain systems, users are still charged a transaction fee, even when their transaction results in an `Err`. This ensures that users are still paying a cost for triggering logic on the blockchain, even when the function fails. This is an important part of keeping our blockchain resilient to DDOS and sybil attacks.

## Simulate Your First Block

Do you think you understand everything it takes to simulate your first block?

Follow the instructions provided by the template to turn your `main` function from "Hello, World!" to actually executing your blockchain's runtime.

## Exercises

On `main.rs`:

```rust
mod balances;
mod system;

// This is our main Runtime.
// It accumulates all of the different pallets we want to use.
pub struct Runtime {
	system: system::Pallet,
	balances: balances::Pallet,
}

impl Runtime {
	// Create a new instance of the main Runtime, by creating a new instance of each pallet.
	fn new() -> Self {
		Self { system: system::Pallet::new(), balances: balances::Pallet::new() }
	}
}

fn main() {
	/* TODO: Create a mutable variable `runtime`, which is a new instance of `Runtime`. */
	/* TODO: Set the balance of `alice` to 100, allowing us to execute other transactions. */

	// start emulating a block
	/* TODO: Increment the block number in system. */
	/* TODO: Assert the block number is what we expect. */

	// first transaction
	/* TODO: Increment the nonce of `alice`. */
	/* TODO: Execute a transfer from `alice` to `bob` for 30 tokens.
		- The transfer _could_ return an error. We should use `map_err` to print
		  the error if there is one.
		- We should capture the result of the transfer in an unused variable like `_res`.
	*/

	// second transaction
	/* TODO: Increment the nonce of `alice` again. */
	/* TODO: Execute another balance transfer, this time from `alice` to `charlie` for 20. */
}
```

Now that you've mastered simulating your first block, you're on your way to blockchain wizardry! Get ready for the exciting adventures ahead!