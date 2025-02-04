# Error Handling

## Local Error Types

To create a new error type, you can use the error.rs file as in any Cosmwasm contract. Since our ADO template implements a lot of functionality imported by the andromeda-std crate, we will need to wrap the andromeda error types to be included in our error.rs file.

In the error.rs file include the following:

```rust
use andromeda_std::error::ContractError as AndromedaContractError;
```

Then we wrap it into our ContractError enum like so:

```rust
pub enum ContractError {
    #[error("{0}")]
    Std(#[from] StdError),

    #[error("0")]
    ADO(#[from] AndromedaContractError),
        .
        .
        .
    }
```

Finally we need to implement the **AndromedaContractError** into our local **ContractError.** Under the **ContractError** enum we include:

```rust
impl Into<AndromedaContractError> for ContractError {
    fn into(self) -> AndromedaContractError {
        match self {
            ContractError::ADO(err) => err,
            _ => panic!("Unsupported error type"),
        }
    }
}
```

### Migrate Function

All our ADOs contain the following migrate function to be able to migrate an ADO to a new code\_id:

{% hint style="info" %}
This will be added to our template, but if not found at the time you go through this, make sure to add it.
{% endhint %}

```rust
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn migrate(deps: DepsMut, _env: Env, _msg: MigrateMsg) -> Result<Response, ContractError> {
    // New version
    let version: Version = CONTRACT_VERSION.parse().map_err(from_semver)?;

    // Old version
    let stored = get_contract_version(deps.storage)?;
    let storage_version: Version = stored.version.parse().map_err(from_semver)?;

    let contract = ADOContract::default();

    ensure!(
        stored.contract == CONTRACT_NAME,
        ContractError::CannotMigrate {
            previous_contract: stored.contract,
        }
    );

    // New version has to be newer/greater than the old version
    ensure!(
        storage_version < version,
        ContractError::CannotMigrate {
            previous_contract: stored.version,
        }
    );

    set_contract_version(deps.storage, CONTRACT_NAME, CONTRACT_VERSION)?;

    // Update the ADOContract's version
    contract.execute_update_version(deps)?;

    Ok(Response::default())
}
```

This can be imported and added into your ADO like so:

```rust
///Import the message
use andromeda_std::{
    ado_base::{MigrateMsg},
    }
    ...
/// Add it to the ADO
ADOContract::default().migrate(deps, CONTRACT_NAME, CONTRACT_VERSION)
```
