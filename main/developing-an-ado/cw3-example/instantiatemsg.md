# InstantiateMsg

First, we will take a look at the InstantiateMsg. There are two adjustments we make:

1. &#x20;**Adding #\[andr\_instantiate]**

We added the **#\[andr\_instantiate]** attribute to the **InstantiateMsg** struct. This macro automatically includes fields common to all ADOs, such as ADO owner and kernel address.

{% hint style="warning" %}
This inclusion is already found in the template by default.
{% endhint %}

2. **Using AndrAddr Instead of String for Addresses**

We replaced the standard String type for voter addresses with [AndrAddr](../../platform-and-framework/common-types.md#andraddr). In ADOs, you will almost always see AndrAddr used instead of String addresses. This means addresses can point to both:

• **Human-readable addresses:** e.g., cosmos1...

• **VFS paths:** e.g., /home/user/app/component

{% hint style="warning" %}
You can find more on **AndrAddr** implementations and methods [here](https://github.com/andromedaprotocol/andromeda-core/blob/main/packages/std/src/amp/addresses.rs).&#x20;
{% endhint %}

{% tabs %}
{% tab title="Multisig ADO" %}
{% code title="msg.rs" %}
```rust
use andromeda_std::amp::AndrAddr;

#[andr_instantiate]
#[cw_serde]
pub struct InstantiateMsg {
    pub voters: Vec<Voter>,
    pub threshold: Threshold,
    pub max_voting_period: Duration,
}
#[cw_serde]
pub struct Voter {
    pub addr: AndrAddr,
    pub weight: u64,
}
```
{% endcode %}
{% endtab %}

{% tab title="Multisig CW3" %}
{% code title="msg.rs" %}
```rust
#[cw_serde]
pub struct InstantiateMsg {
    pub voters: Vec<Voter>,
    pub threshold: Threshold,
    pub max_voting_period: Duration,
}

#[cw_serde]
pub struct Voter {
    pub addr: String,
    pub weight: u64,
}
```
{% endcode %}


{% endtab %}
{% endtabs %}

The rest of the logic remains the same:

{% tabs %}
{% tab title="Multisig  ADO" %}
{% code title="contract.rs" %}
```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn instantiate(
    deps: DepsMut,
    env: Env,
    info: MessageInfo,
    msg: InstantiateMsg,
) -> Result<Response, ContractError> {
    let resp = ADOContract::default().instantiate(
        deps.storage,
        env.clone(),
        deps.api,
        &deps.querier,
        info,
        BaseInstantiateMsg {
            ado_type: CONTRACT_NAME.to_string(),
            ado_version: CONTRACT_VERSION.to_string(),
            kernel_address: msg.kernel_address,
            owner: msg.owner,
        },
    )?;
    // verify voters list is not empty
    if msg.voters.is_empty() {
        return Err(ContractError::CustomError {
            msg: "No voters".to_string(),
        });
    }
    let total_weight = msg.voters.iter().map(|v| v.weight).sum();

    msg.threshold.validate(total_weight)?;

    set_contract_version(deps.storage, CONTRACT_NAME, CONTRACT_VERSION)?;

    let cfg = Config {
        threshold: msg.threshold,
        total_weight,
        max_voting_period: msg.max_voting_period,
    };
    CONFIG.save(deps.storage, &cfg)?;

    //validate and add all voters
    for voter in msg.voters.iter() {
        let key = deps
            .api
            .addr_validate(voter.addr.get_raw_address(&deps.as_ref())?.as_str())?;
        VOTERS.save(deps.storage, &key, &voter.weight)?;
    }

    Ok(resp)
}
```
{% endcode %}
{% endtab %}

{% tab title="Multisig CW3" %}
{% code title="contract.rs" %}
```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn instantiate(
    deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: InstantiateMsg,
) -> Result<Response, ContractError> {
    if msg.voters.is_empty() {
        return Err(ContractError::NoVoters {});
    }
    let total_weight = msg.voters.iter().map(|v| v.weight).sum();

    msg.threshold.validate(total_weight)?;

    set_contract_version(deps.storage, CONTRACT_NAME, CONTRACT_VERSION)?;

    let cfg = Config {
        threshold: msg.threshold,
        total_weight,
        max_voting_period: msg.max_voting_period,
    };
    CONFIG.save(deps.storage, &cfg)?;

    // add all voters
    for voter in msg.voters.iter() {
        let key = deps.api.addr_validate(&voter.addr)?;
        VOTERS.save(deps.storage, &key, &voter.weight)?;
    }
    Ok(Response::default())
}
```
{% endcode %}
{% endtab %}
{% endtabs %}
