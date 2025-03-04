# Execution

### Execution

Executing messages in an ADO is a fairly simple process. We expose a new struct called `ExecuteContext` which has your regular dependencies, info and environment alongside a new [`AMPPkt`](https://github.com/andromedaprotocol/andromeda-core/blob/amp/packages/std/src/amp/messages.rs#L240) struct (`amp_ctx`) that includes info about the current AMP packet if the message was received via the `AMPReceive` message type. \
\
An AMP Packet includes some useful information such as the `origin` field which includes the original sender of the packet.

{% hint style="warning" %}
&#x20;**If you are using this for authorisation purposes please verify that the sender is someone you can trust.**&#x20;
{% endhint %}

```rust
pub struct ExecuteContext<'a> {
    pub deps: DepsMut<'a>,
    pub info: MessageInfo,
    pub env: Env,
    pub amp_ctx: Option<AMPPkt>,
    pub contract: ADOContract<'a>,
    pub raw_info: MessageInfo,
}
```

In order to expose this data we must first call the method for handling `AMPReceive` messages and provide it your standard execution handler. This is done by our  `andr_execute_fn` macro:

{% code title="contract.rs" %}
```rust
#[andromeda_std::andr_execute_fn]
pub fn execute(ctx: ExecuteContext, msg: ExecuteMsg) -> Result<Response, ContractError> {
    match msg {
        // .. Your execute message handlers,
        _ => ADOContract::default().execute(ctx, msg)
    }
}


pub fn my_handler(ctx: ExecuteContext, some_param: SomeVariableType) -> Result<Response, ContractError> {
  let ExecuteContext {deps, info, env, ..} = ctx;
    // .. Your code
}
```
{% endcode %}

As a catchall we provide the `.execute(ctx, msg)` call to handle any Andromeda specific messages. If you are looking to use another execute message handler such as cw721-base check out our CW721 contract [here](https://github.com/andromedaprotocol/andromeda-core/blob/5bef681b13fcffadfec06b3e98c55c1ca5551d71/contracts/non-fungible-tokens/andromeda-cw721/src/contract.rs#L110).

### Attributes

There are a lot of cases where we want messages to have specific validation checks, these validation checks are usually extremely common and add a bit of overhead to implement and validate. To reduce this some of these redundant checks have been moved to "**attributes**". These are field attributes on the `ExecuteMsg` variants that define what validation should occur when the message is called (even when it is called via AMP). These include:

| Atrribute    | Description                                                 |
| ------------ | ----------------------------------------------------------- |
| `nonpayable` | <p></p><p>This message should not accept funds.</p><p></p>  |
| `restricted` | This message should only be callable by the contract owner. |
| `direct`     | This messae should not be callable via AMP.                 |

{% code title="msg.rs" %}
```rust
pub enum ExecuteMsg {
  #[attrs(restricted, nonpayable)]
  MyRestrictedMsg { //.. }.
  #[attrs(direct)]
  MyDirectMsg { //.. },
}
```
{% endcode %}

These can be applied in any particular order or combination. The checks for these are then performed using the `andr_execute_fn` macro so they do not require any extra code, however they do add methods to the `ExecuteMsg` enum that may be used as necessary.
