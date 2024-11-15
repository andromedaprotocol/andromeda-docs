# ADO Example

In this example, we will be looking at transforming the [cw3 fixed multisig contract](https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw3-fixed-multisig) from the cw-plus repo to a fixed multisig ADO. The final result can be found here.

{% hint style="warning" %}
This conversion is a simple one and does not use all of the available Andromeda features such as the VFS and Permissioning.&#x20;
{% endhint %}

## Initial Setup

The easiest and most recommended way of starting development on any ADO is to use the [Andromeda ADO Template](https://github.com/andromedaprotocol/andr-cw-template). From the template, we can start a new project by running the following in the terminal:

{% hint style="warning" %}
We are using the minimal template here.&#x20;
{% endhint %}

```
cargo generate --git https://github.com/andromedaprotocol/andr-cw-template.git --name fixed-multisig -d minimal=true
```

You will be prompted if you would like to include permissioning in the template. For this example I will not use permissioning.

{% hint style="warning" %}
Exlcuding permissioning would mean that the base messages for permissioning will not be included in the ADO. You can read more about permissioning [here](../platform-and-framework/ado-base/#permissioning).
{% endhint %}

You will notice that the name the ado will be the taken from the name of the project by default:

{% hint style="warning" %}
For naming an ADO, try to conform with our naming standard which uses a - to separate the name in case the name is made of several words.
{% endhint %}

```rust
 BaseInstantiateMsg {
            ado_type: "fixed-multisig".to_string(),
            ado_version: CONTRACT_VERSION.to_string(),
            operators: None,
            kernel_address: msg.kernel_address,
            owner: msg.owner,
        },
```

In the **Cargo.toml** file, you can check the version of your imported crates. The latest version of andromeda-std is "0.1.2" so make sure you update it in case another version is being used:

```toml
andromeda-std = { version = "0.1.2" }
```

## Building the ADO

Now that you are set up with the template, you can start filling in the messages for the ADO. Since we already have most of the logic from the cw3 fixed multisig, we can just move them to our ADO.

&#x20;As explained  in the [Getting Started](getting-started/) section, we use an **ExecuteContext** struct to be able to handle AMP messages. This information is passed to a **ctx** variable that we use. For example let us take a look   at our **Propose** message from the cw3 version and compare it to the ADO version:

<pre class="language-rust"><code class="lang-rust"><strong>//CW3 version
</strong><strong>ExecuteMsg::Propose {
</strong>            title,
            description,
            msgs,
            latest,
        } => execute_propose(deps, env, info, title, description, msgs, latest),
        
//ADO version
ExecuteMsg::Propose {
            title,
            description,
            msgs,
            latest,
        } => execute_propose(ctx, title, description, msgs, latest),
</code></pre>

The ctx contains the deps,env, and info and can be used as a parameter instead. In our **excecute\_propose** function we include:

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

This will assign the values back to deps, info, and env from our ctx and we can work with them normally. As you can see from the ADO version, the rest of the logic remained the same as the CW3 version.

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

Other than what we have covered, most of the logic of the CW3 remained the same. Unit test and integration tests were not changed as they are already written and all passed in the ADO version as well. As mentioned [before](getting-started/#testing), we do provide custom mock structs for testing. You can check any of our published ADOs testing from our [core repo](https://github.com/andromedaprotocol/andromeda-core) to see how these structs can be used to conduct testing.&#x20;
