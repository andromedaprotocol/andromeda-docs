---
description: >-
  Getting started with your own ADO. This document will show you how to
  create/transform your smart contract into an ADO.
---

# Getting Started

## Template

If you're starting from scratch the best way to begin is to use the fork of the CosmWasm smart contract template. This can be found on our Github [here](https://github.com/andromedaprotocol/andr-cw-template).

### Andromeda Standard Crate

Our crate can be found [here](https://crates.io/crates/andromeda-std).

## Contract Messages

The message definitions for an ADO can be added to any existing message definitions using our `andromeda-macros` crate (this is also available via the `andromeda-std` crate, so if you're using our full suite you don't need to install this crate explicitly). There are three macros for instantiation, execute and query messages.

```rust
use andromeda_std::{andr_exec, andr_instantiate, andr_query};

#[andr_instantiate]
#[cw_serde]
pub struct InstantiateMsg {
    // ... Your instantiation variables
}

#[andr_exec]
#[cw_serde]
pub enum ExecuteMsg {
    // ... Your execute messages
}

#[andr_query]
#[cw_serde]
#[derive(QueryResponses)]
pub enum QueryMsg {
    // ... Your query messages
}
```

These macros work by merging your existing message types with the available Andromeda message types. If you'd like to take a look at these messages you can see them [here](https://github.com/andromedaprotocol/andromeda-core/blob/amp/packages/std/src/ado\_base/mod.rs).

## ADOContract

All interactions with the Andromeda ecosystem can be done via the `ADOContract` interface. To use this interface you must first import it from the `andromeda-std` crate.

```rust
use andromeda_std::ado_contract::ADOContract;
```

Once imported you can create an instance of the contract in your code execution like so:

```rust
let ado_contract = ADOContract::default();
```

From this struct we can access all of the state variables and various cross-contract calls that are needed by an ADO.
