# Error Handling and Migrate Function

## Local Error Types

To create a new error type, you can use the error.rs file as in any Cosmwasm contract. Since our ADO template implements a lot of functionality imported by the andromeda-std crate, we will need to wrap the andromeda error types to be included in our error.rs file.

In the error.rs file include the following:

```rust
use andromeda_std::error::ContractError as AndrContractError;
```

Then we wrap it into our ContractError enum like so:

```rust
pub enum ContractError {
    #[error("{0}")]
    Std(#[from] StdError),

    #[error("{0}")]
    Andr(#[from] AndrContractError),
        .
        .
        .
    }
```

{% hint style="info" %}
This is present by default in our [template](https://github.com/andromedaprotocol/andr-cw-template).
{% endhint %}

### Migrate Function

All our ADOs contain the following migrate function to be able to migrate an ADO to a new code\_id:

{% hint style="info" %}
This will be added to our template, but if not found at the time you go through this, make sure to add it.
{% endhint %}

This can be imported and added into your ADO like so:

{% code title="contract.rs" %}
```rust
///Import the message
use andromeda_std::{
    ado_base::{MigrateMsg},
    }
    
    
/// Add it to the ADO
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn migrate(deps: DepsMut, env: Env, _msg: MigrateMsg) -> Result<Response, ContractError> {
    ADOContract::default().migrate(deps, env, CONTRACT_NAME, CONTRACT_VERSION)
}
```
{% endcode %}
