I've seen a lot of complicated tutorials, so let's try to keep this one simple and organized. :) I hope you enjoy it!

# The Balances Pallet

[Youtube](https://www.youtube.com/watch?v=9wWHRZfwahc)

In this section, we will build the very first logic for our state machine: a Balances Pallet.

This Pallet will manage the balances of users and allow them to transfer tokens to one another.

Along the way, you will learn about safe math, options, error handling, and more.

By the end of this section, you will have designed the logic of a simple cryptocurrency.

# Creating a Balances Pallet

As mentioned earlier, at the heart of a blockchain is a state machine.

We can create a very naive state machine using simple Rust abstractions, and through this help learn about Rust in the context of blockchains.

We want keep our code organized, so we will not really start building in the `main.rs` file, but actually in separate Rust modules. We can think of the `main.rs` file as glue which brings everything together, and we will see that over the course of this workshop.

"Pallet" is a term specific to the Polkadot SDK, which refers to Rust modules which contain logic specific for your [blockchain runtime](https://). We are going to start using this term here because what we build here will closely mirror what you will see with the Polkadot SDK.

## Balances

Pretty much every blockchain has logic handles the balances of users on that blockchain.

This Pallet will tell you: how much balance each user has, provide functions which allow users to transfer those balances, and even some low level functions to allow your blockchain system to manipulate those balances if needed. Think for example if you want to mint new tokens which don't already exist.

This is a great starting point, and the very first Pallet we will build.

## Creating a Struct

1. Create a new file in your `src` folder named `balances.rs`

	```
	touch src/balances.rs
	```

2. In this file, create a `struct`, which will act as the state and entry point for this module:

	```rust
	pub struct Pallet {}
	```

3. Now go back to `src/main.rs`, and import this new module, which will include all the logic inside of it:

	```rust
	mod balances;
	```

4. If we run your program now, you will see it still compiles and runs, but might show you some warnings like:

	```
	warning: struct `Pallet` is never constructed
	--> src/balances.rs:1:12
	|
	1 | pub struct Pallet {    }
	|              ^^^^^^
	|
	= note: `#[warn(dead_code)]` on by default

	warning: `pr` (bin "pr") generated 1 warning
	```

	That's fine! We haven't started using our `Pallet` yet, but you can see that the Rust compiler is detecting our new code, and bringing that logic into our main program. This is the start of building our first state machine module.

## Exercises:

On `balances.rs`:
```rust
/* TODO: create a new public struct named `Pallet`. */
```

On `main.rs`:
```rust
/* TODO: use your new module `balances` */

fn main() {
	println!("Hello, world!");
}
```

If you have a question you're curious about, feel free to ask it in [🆘・section-1](https://discord.com/channels/898706705779687435/980904325763186788) on Discord.

Normally, I start to really understand things when I start programming. So, let's start building some code. :)