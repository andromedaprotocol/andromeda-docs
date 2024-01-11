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

### Instantiation

To initialize the data required by an ADO we must call the `instantiate` function from within our instantiation entry point for our contract.&#x20;

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn instantiate(
    deps: DepsMut,
    env: Env,
    info: MessageInfo,
    msg: InstantiateMsg,
) -> Result<Response, ContractError> {
    // ... Some previous instantiation code
    let contract = ADOContract::default();

    let resp = contract.instantiate(
        deps.storage,
        env,
        deps.api,
        info.clone(),
        BaseInstantiateMsg {
            ado_type: ADO_NAME.to_string(),
            ado_version: CONTRACT_VERSION.to_string(),
            operators: None,
            kernel_address: msg.kernel_address,
            owner: msg.owner,
        },
    )?;

    Ok(resp
        .add_attribute("method", "instantiate")
        .add_attribute("owner", info.sender))
}
```

This will create an ado with type `ADO_NAME` and a current version of `CONTRACT_VERSION`. **This is used by the ADO database on chain to verify that the current contract is an ADO so make sure this is correct**.&#x20;

### Execution

Executing messages in an ADO is a fairly simple process. We expose a new struct called `ExecuteContext` which has your regular dependencies, info and environment alongside a new [`AMPPkt`](https://github.com/andromedaprotocol/andromeda-core/blob/amp/packages/std/src/amp/messages.rs#L240) struct (`amp_ctx`) that includes info about the current AMP packet if the message was received via the `AMPReceive` message type. \
\
An AMP Packet includes some useful information such as the `origin` field which includes the original sender of the packet. **If you are using this for authorisation purposes please verify that the sender is someone you can trust.**&#x20;

```rust
pub struct ExecuteContext<'a> {
    pub deps: DepsMut<'a>,
    pub info: MessageInfo,
    pub env: Env,
    pub amp_ctx: Option<AMPPkt>,
}
```

In order to expose this data we must first call the method for handling `AMPReceive` messages and provide it your standard execution handler like so:

```rust
use andromeda_std::{ExecuteContext, ContractError};
// ..

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn execute(
    deps: DepsMut,
    env: Env,
    info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response, ContractError> {
    let ctx = ExecuteContext::new(deps, info, env);
    if let ExecuteMsg::AMPReceive(pkt) = msg {
        ADOContract::default().execute_amp_receive(ctx, pkt, handle_execute)
    } else {
        handle_execute(ctx, msg)
    }
}

pub fn handle_execute(ctx: ExecuteContext, msg: ExecuteMsg) -> Result<Response, ContractError> {
    match msg {
        // .. Your execute message handlers,
        ExecuteMsg::MyMsg { some_param } => my_handler(ctx, some_param),
        _ => ADOContract::default().execute(ctx, msg),
    }
}

pub fn my_handler(ctx: ExecuteContext, some_param: SomeVariableType) -> Result<Response, ContractError> {
    let ExecuteContext { amp_ctx, deps, info, env } = ctx;
    // .. Your code
}
```

Here we provide our `handle_execute` method to the `execute_amp_receive` method so that the handler can verify and create the `ExecuteContext` object appropriately before passing it to the handler.

As a catchall we provide the `.execute(ctx, msg)` call to handle any Andromeda specific messages. If you are looking to use another execute message handler such as cw721-base check out our CW721 contract [here](https://github.com/andromedaprotocol/andromeda-core/blob/amp/contracts/non-fungible-tokens/andromeda-cw721/src/contract.rs#L103).

### Queries

Handling queries in an ADO is a lot simpler, this is simply done by adding a catchall call to the `query` handler of the `ADOContract` interface.

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, env: Env, msg: QueryMsg) -> Result<Binary, ContractError> {
    match msg {
        QueryMsg::GetCount {} => Ok(to_binary(&query::count(deps)?)?),
        _ => ADOContract::default().query(deps, env, msg),
    }
}
```

### Testing

To aid in testing we provide a separate `mock_dependencies_custom` function that provides handlers for specific ADO contracts. This can be found [here](https://github.com/andromedaprotocol/andromeda-core/blob/amp/packages/std/src/testing/mock\_querier.rs). The following mock variables are used:

```rust
/// Mock CW20 Contract Address
pub const MOCK_CW20_CONTRACT: &str = "cw20_contract";
/// Mock App Contract Address
pub const MOCK_APP_CONTRACT: &str = "app_contract";
/// Mock Primitive Contract Address
pub const MOCK_PRIMITIVE_CONTRACT: &str = "primitive_contract";
/// Mock Kernel Contract Address
pub const MOCK_KERNEL_CONTRACT: &str = "kernel_contract";
/// Mock VFS Contract Address
pub const MOCK_VFS_CONTRACT: &str = "vfs_contract";
/// Mock ADODB Contract Address
pub const MOCK_ADODB_CONTRACT: &str = "adodb_contract";
// Mock ADO Publisher
pub const MOCK_ADO_PUBLISHER: &str = "ado_publisher";
// Mock Osmosis Router
pub const MOCK_OSMOSIS_ROUTER_CONTRACT: &str = "osmosis_router";
// Mock Economics Contract
pub const MOCK_ECONOMICS_CONTRACT: &str = "economics_contract";

/// Mock Rates Contract Address
pub const MOCK_RATES_CONTRACT: &str = "rates_contract";
/// Mock Address List Contract Address
pub const MOCK_ADDRESS_LIST_CONTRACT: &str = "address_list_contract";

/// An invalid contract address
pub const INVALID_CONTRACT: &str = "invalid_contract";
/// An invalid VFS Path
pub const FAKE_VFS_PATH: &str = "/f";
/// An invalid ADODB Key
pub const FAKE_ADODB_KEY: &str = "fake_adodb_key";
/// A valid action
pub const MOCK_ACTION: &str = "action";
```

To use the dependencies simply create your mock dependencies variable like so:

```rust
use andromeda_std::testing::mock_dependencies_custom;

fn test_something() {
 let mut deps = mock_dependencies_custom(&[]);
 // .. The rest of your test code
}
```


