You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/9171430e3a44c42513b5c1104ef972e2).

# Basic Balance Test

[Youtube](https://www.youtube.com/watch?v=2-OWpKw8CzU)

Now that we have the basics of our `Pallet` set up, let's actually interact with it.

For that, we will go back to the `main.rs` file, and create our first `#[test]` which will play with the code we have written so far.

1. In your `src/balances.rs` file, add a new `#[test]` named `fn init_balances()`:

	```rust
	#[test]
	fn init_balances() { }
	```

2. To begin our test, we need to initialize a new instance of our `Pallet`:

	```rust
	#[test]
	fn init_balances() {
		let mut balances = balances::Pallet::new();
	}
	```

	Note that we make this variable `mut` since we plan to mutate our state using our newly created API.

3. Finally, let's check that our read and write APIs are working as expected:

	```rust
	#[test]
	fn init_balances() {
		let mut balances = balances::Pallet::new();

		assert_eq!(balances.balance(&"alice".to_string()), 0);
		balances.set_balance(&"alice".to_string(), 100);
		assert_eq!(balances.balance(&"alice".to_string()), 100);
		assert_eq!(balances.balance(&"bob".to_string()), 0);
	}
	```

4. We can run our tests using `cargo test`, where hopefully you should see that it passes.

I hope at this point you can start to see the beginnings of your simple blockchain state machine.

# Exercise:
On `balances.rs`:
```rust
#[cfg(test)]
mod tests {
	#[test]
	fn init_balances() {
		/* TODO: Create a mutable variable `balances`, which is a new instance of `Pallet`. */

		/* TODO: Assert that the balance of `alice` starts at zero. */
		/* TODO: Set the balance of `alice` to 100. */
		/* TODO: Assert the balance of `alice` is now 100. */
		/* TODO: Assert the balance of `bob` has not changed and is 0. */
	}
}
```

Share your [#progress](https://discord.com/channels/898706705779687435/980906289968345128) on Discord and make Yan's day!