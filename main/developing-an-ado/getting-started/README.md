---
description: >-
  Getting started with your own ADO. This document will show you how to
  create/transform your smart contract into an ADO.
---

# Getting Started

## Template

If you're starting from scratch the best way to begin is to use the Andromeda smart contract template. This can be found on our Github [here](https://github.com/andromedaprotocol/andr-cw-template).

### Andromeda Standard Crate

Our crate can be found [here](https://crates.io/crates/andromeda-std).

## Contract Messages

The message definitions for an ADO can be added to any existing message definitions using our `andromeda-macros` crate (this is also available via the `andromeda-std` crate, so if you're using our full suite you don't need to install this crate explicitly). There are three macros for instantiation, execute and query messages.

{% code title="msg.rs" %}
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
{% endcode %}

These macros work by merging your existing message types with the available Andromeda message types. If you'd like to take a look at these messages you can see them [here](https://github.com/andromedaprotocol/andromeda-core/tree/main/packages/std/src/ado_base).

## ADOContract

All interactions with the Andromeda ecosystem can be done via the `ADOContract` interface. To use this interface you must first import it from the `andromeda-std` crate.

{% hint style="warning" %}
This is included in the template.
{% endhint %}

{% code title="contract.rs" %}
```rust
use andromeda_std::ado_contract::ADOContract;
```
{% endcode %}

Once imported you can create an instance of the contract in your code execution like so:

{% code title="contract.rs" %}
```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn instantiate(
    deps: DepsMut,
    env: Env,
    info: MessageInfo,
    msg: InstantiateMsg,
) -> Result<Response, ContractError> {

    let contract = ADOContract::default();
    ...
```
{% endcode %}

```rust
pub struct ADOContract<'a> {
    pub(crate) owner: Item<'a, Addr>,
    pub(crate) original_publisher: Item<'a, Addr>,
    pub(crate) block_height: Item<'a, u64>,
    pub(crate) ado_type: Item<'a, String>,
    pub(crate) app_contract: Item<'a, Addr>,
    pub(crate) kernel_address: Item<'a, Addr>,
    pub(crate) permissioned_actions: Map<'a, String, bool>,
    #[cfg(feature = "rates")]
    /// Mapping of action to rate
    pub rates: Map<'a, &'a str, Rate>,
}
```

From this struct we can access all of the state variables and various cross-contract calls that are needed by an ADO.
