You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/4d41571e1155cb262c116e88b185bcd3).

# Creating Our Runtime

We have now established two different Pallets for our blockchain: the System and Balances Pallet.

How do these pallets work together to create a unified blockchain system?

For that, we will need to create a `Runtime`.

## What is the Runtime?

Remember that there is a separation between the blockchain client and the state transition function of our blockchain.

You can think of the runtime as the accumulation of all logic which composes your state transition function. It will combine all of your pallets into a single object, and then expose that single object as the entry point for your users to interact with.

Certainly this sounds pretty abstract, but it will make more sense as we complete this tutorial.

## Create the Runtime

[Youtube](https://www.youtube.com/watch?v=KwyeSU0wl_w)

Just like our Pallets, our Runtime will be represented with a simple `struct`, however in this case, the fields of our `struct` will be our Pallets!

Complete the instructions for creating a new runtime which includes our System and Balances pallets. For this, you will need to take advantage of the `new()` functions we exposed for each of the Pallets.

## Exercises

On `main.rs`:

```rust
mod balances;
mod system;

// This is our main Runtime.
// It accumulates all of the different pallets we want to use.
pub struct Runtime {
	/* TODO: create a field `system` which is of type `system::Pallet`. */
	/* TODO: create a field `balances` which is of type `balances::Pallet`. */
}

impl Runtime {
	// Create a new instance of the main Runtime, by creating a new instance of each pallet.
	fn new() -> Self {
		/* TODO: Create a new `Runtime` by creating new instances of `system` and `balances`. */
		unimplemented!()
	}
}

fn main() {
	println!("Hello, world!");
}
```

That's it :). Let's move on! I'm amazed by your progress!

If you need assistance or have any questions about integrating your pallets into the Runtime, feel free to ask for help in the [#🆘・section-2](https://discord.com/channels/898706705779687435/980905562566967427) channel on Discord. We're here to support you on your journey! 