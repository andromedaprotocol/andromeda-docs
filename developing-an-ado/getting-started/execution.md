# Execution

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
