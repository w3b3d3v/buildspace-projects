file: `src/balances.rs`
```rust
use std::collections::BTreeMap;

/// This is the Balances Module.
/// It is a simple module which keeps track of how much balance each account has in this state
/// machine.
pub struct BalancesModule {
	// A simple storage mapping from accounts (`&'static str`) to their balances (`u128`).
	balances: BTreeMap<String, u128>,
}

impl BalancesModule {
	/// Create a new instance of the balances module.
	pub fn new() -> Self {
		Self { balances: BTreeMap::new() }
	}
}
```