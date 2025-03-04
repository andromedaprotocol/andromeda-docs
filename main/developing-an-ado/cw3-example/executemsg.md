# ExecuteMsg

## ExecuteMsg

Next, let’s look at the ExecuteMsg, which defines all the actions users can perform with the contract.  The structure remains almost the same, but we add an attribute as we did for our instantiation message:

{% code title="msg.rs" %}
```rust
#[andr_exec]
#[cw_serde]
pub enum ExecuteMsg {
    ...
}
```
{% endcode %}

Just like with **#\[andr\_instantiate]** for initialization, the **#\[andr\_exec]** attribute is a custom Andromeda macro that adds all the base execute messages that are available for all ADOs and is handled here:

{% code title="contract.rs" %}
```rust
 _ => ADOContract::default().execute(ctx, msg),
```
{% endcode %}

{% hint style="warning" %}
This inclusion is already found in the template by default.&#x20;
{% endhint %}

As explained  in the [Getting Started](../getting-started/) section, we use an **ExecuteContext** struct to be able to handle AMP messages. This information is passed to a **ctx** variable that we use. For example let us take a look   at our **Propose** message from the cw3 version and compare it to the ADO version:

{% tabs %}
{% tab title="Multisig ADO" %}
{% code title="contract.rs" %}
```rust
use andromeda_std::{common::context::ExecuteContext};

pub fn execute(ctx: ExecuteContext, msg: ExecuteMsg) -> Result<Response, ContractError> {
ExecuteMsg::Propose {
            title,
            description,
            msgs,
            latest,
        } => execute_propose(ctx, title, description, msgs, latest),
  }
```
{% endcode %}
{% endtab %}

{% tab title="Multisig CW3" %}
{% code title="contract.rs" %}
```rust
pub fn execute(
    deps: DepsMut,
    env: Env,
    info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response<Empty>, ContractError> {
    match msg {
        ExecuteMsg::Propose {
            title,
            description,
            msgs,
            latest,
        } => execute_propose(deps, env, info, title, description, msgs, latest),
    }
```
{% endcode %}
{% endtab %}
{% endtabs %}

The ctx contains the deps,env, and info and can be used as a parameter instead. In our **excecute\_propose** function we include:

{% code title="contract.rs" %}
```rust
fn execute_propose(
    ctx: ExecuteContext,
    title: String,
    description: String,
    msgs: Vec<CosmosMsg>,
    latest: Option<Expiration>,
) -> Result<Response<Empty>, ContractError> {
    let ExecuteContext {
        deps, info, env, ..
    } = ctx;
```
{% endcode %}

This will assign the values back to deps, info, and env from our ctx and we can work with them normally. As you can see from the ADO version, the rest of the logic remained the same as the CW3 version.

Alternatively, we could access these variables through our `ctx` like so:

```rust
let cfg = CONFIG.load(ctx.deps.storage)?;
```

Other than that, the rest of the logic for execute messages remains the same as any other CosmWasm smart contract.
