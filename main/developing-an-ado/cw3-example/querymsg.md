# QueryMsg

The only difference when handling query messages in the fixed multisig ADO is the use of the.        `#[andr_query]`attribute. Just like `#[andr_instantiate]` and `#[andr_exec]`, this macro ensures that the [common ADO queries](../../platform-and-framework/ado-base/andromedaquery.md)  are included.

{% code title="msg.rs" %}
```rust
#[andr_query]
#[cw_serde]
#[derive(QueryResponses)]
pub enum QueryMsg {
    ...
}
```
{% endcode %}

This case is handled here:

{% code title="contract.rs" %}
```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, env: Env, msg: QueryMsg) -> Result<Binary, ContractError> {
    match msg {
    
        /// handle base queries
        _ => ADOContract::default().query(deps, env, msg),
    }
}
```
{% endcode %}

The remaining logic for the queries is the same as in both the CW3 and ADO versions.
