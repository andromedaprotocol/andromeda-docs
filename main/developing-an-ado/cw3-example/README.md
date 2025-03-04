# CW3 EXAMPLE

In this example, we will be looking at transforming the cw3 [fixed multisig contract](https://github.com/CosmWasm/cw-plus/blob/main/contracts/cw3-fixed-multisig) from the cw-plus repo to a fixed multisig ADO. The final result can be found [here](https://github.com/andromedaprotocol/andromeda-core/tree/main/contracts/accounts/andromeda-fixed-multisig).

## Initial Setup

The easiest and most recommended way of starting development on any ADO is to use the [Andromeda ADO Template](https://github.com/andromedaprotocol/andr-cw-template). From the template, we can start a new project by running the following in the terminal:

```
cargo generate --git https://github.com/andromedaprotocol/andr-cw-template.git --name fixed-multisig -d minimal=true
```

You will notice that the ADO name will be the taken from the specified name of the project by default:

{% hint style="warning" %}
For naming an ADO, try to conform with our naming standard which uses a - to separate the name in case the name is made of several words.
{% endhint %}

{% code title="contract.rs" %}
```rust
// version info for migration info
const CONTRACT_NAME: &str = "crates.io:fixed-multisig";
const CONTRACT_VERSION: &str = env!("CARGO_PKG_VERSION");
```
{% endcode %}

In the **Cargo.toml** file, you can check the version of your imported crates. The latest version of andromeda-std is "0.1.2" so make sure you update it in case another version is being used:

{% code title="Cargo.toml" %}
```toml
andromeda-std = { version = "0.1.2" }
```
{% endcode %}

## Building the ADO

Now that you are set up with the template, you can start filling in the messages for the ADO. Since we already have most of the logic from the cw3 fixed multisig, we can just move them to our ADO template.

## Local Error Types

To create a new error type, you can use the error.rs file as in any Cosmwasm contract. Since our ADO template implements a lot of functionality imported by the andromeda-std crate, we will need to wrap the andromeda error types to be included in our error.rs file.

{% hint style="warning" %}
This is already present in the template.
{% endhint %}

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

### Migrate Function

All our ADOs contain the following migrate function to be able to migrate an ADO to a new code\_id:

{% hint style="info" %}
This will be added to our template, but if not found at the time you go through this, make sure to add it.
{% endhint %}

This can be imported and added into your ADO like so:

```rust
///Import the message
use andromeda_std::{ado_base::{MigrateMsg}},
    
    
/// Add it to the ADO
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn migrate(deps: DepsMut, env: Env, _msg: MigrateMsg) -> Result<Response, ContractError> {
    ADOContract::default().migrate(deps, env, CONTRACT_NAME, CONTRACT_VERSION)
}
```

Other than what we have covered, most of the logic of the CW3 remained the same. Unit test and integration tests were not changed as they are already written and all passed in the ADO version as well. As mentioned [before](../getting-started/#testing), we do provide custom mock structs for testing. You can check any of our published ADOs testing from our [core repo](https://github.com/andromedaprotocol/andromeda-core) to see how these structs can be used to conduct testing.&#x20;
