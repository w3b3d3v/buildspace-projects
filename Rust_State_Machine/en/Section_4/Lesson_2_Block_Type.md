You can find the [solution for the previous step here](https://gist.github.com/nomadbitcoin/a6964532490ce5fbda97a606c07e6f56).

# Create Your Block Type

[Youtube](https://www.youtube.com/watch?v=eIHxIr_zeq0)

The support module provided for us a bunch of generic types which can be customized for our simple state machine. To actually start using them, we need to define concrete versions of these types using our other concrete types.

## Runtime Call

You will see the template provides an empty `enum RuntimeCall` which we will expand later. This is an object which is supposed to represent all the various calls exposed by your blockchain to users and the outside world. We need to mock this enum at this step so that it can be used to build a concrete `Extrinsic` type.

For now, there is just the `transfer` function exposed by the Balances Pallet, but we will add more before this tutorial is complete, and figure out ways to automate the creation of our `RuntimeCall`.

You can access this type within `mod types` with `crate::RuntimeCall`.

# Exercises:

### Building the Block Type

It's time to define the concrete `Block` type that we will use to enhance our simple state machine.

1. Using the `RuntimeCall` enum and the `AccountId` type, you can define a concrete `Extrinsic` type.
2. Using the `BlockNumber` type, you can define a concrete `Header` type.
3. Using the concrete `Header` and `Extrinsic` types, you can define a concrete `Block` type.

As you can see, the `Block` is composed of layers of generic types, allowing the whole structure to be flexible and customizable to our needs.

Pay attention to the generic type definitions to ensure that you use all the correct generic parameters in all the right places.

On `main.rs`:

```rust
mod types {
	pub type AccountId = String;
	pub type Balance = u128;
	pub type BlockNumber = u32;
	pub type Nonce = u32;
	/* TODO: Define a concrete `Extrinsic` type using `AccountId` and `RuntimeCall`. */
	/* TODO: Define a concrete `Header` type using `BlockNumber`. */
	/* TODO: Define a concrete `Block` type using `Header` and `Extrinsic`. */
}

// These are all the calls which are exposed to the world.
// Note that it is just an accumulation of the calls exposed by each module.
pub enum RuntimeCall {
	// TODO: Not implemented yet.
}
```