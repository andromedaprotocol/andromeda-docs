# Instantiation

### Instantiation

To initialize the data required by an ADO we must call the `instantiate` function from within our instantiation entry point for our contract.&#x20;

{% code title="contract.rs" %}
```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn instantiate(
    deps: DepsMut,
    env: Env,
    info: MessageInfo,
    msg: InstantiateMsg,
) -> Result<Response, ContractError> {
    // ... ADO instantiation code
    
    let contract = ADOContract::default();
    let resp = contract.instantiate(
        deps.storage,
        env,
        deps.api,
        &deps.querier,
        info.clone(),
        BaseInstantiateMsg {
            ado_type: CONTRACT_NAME.to_string(),
            ado_version: CONTRACT_VERSION.to_string(),
            kernel_address: msg.kernel_address,
            owner: msg.owner,
        },
    )?;

    Ok(resp
        .add_attribute("method", "instantiate")
        .add_attribute("owner", info.sender)
        )
}
```
{% endcode %}

This will create an ado with type `ADO_NAME` and a current version of `CONTRACT_VERSION`. **This is used by the ADO database on chain to verify that the current contract is an ADO so make sure this is correct**.&#x20;
