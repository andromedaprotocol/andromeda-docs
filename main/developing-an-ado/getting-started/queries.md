# Queries

### Queries

Handling queries in an ADO is a lot simpler, this is simply done by adding a catchall call to the `query` handler of the `ADOContract` interface.

{% code title="contract.rs" %}
```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, env: Env, msg: QueryMsg) -> Result<Binary, ContractError> {
    match msg {
    // Query Messages
        _ => ADOContract::default().query(deps, env, msg),
    }
}
```
{% endcode %}
