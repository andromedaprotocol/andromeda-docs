---
description: >-
  An Andromeda Module is a struct that can be attached using the Instantiate
  Message to any ADO and accessed in any standard CW721 Execute Message via
  generic hooks
---

# Modules

## Module Definitions

Each module is defined using a `ModuleDefinition` enum which contains the metadata about the module:

```rust
enum ModuleDefinition {
    Whitelist { moderators: Vec<String> },
    Blacklist { moderators: Vec<String> },
    Taxable { tax: Rate, receivers: Vec<String> },
    Royalties { fee: Rate, receivers: Vec<String> },
    Receipt { }
}
```

## Defining Modules

When instantiating an **Andromeda Digital Object **contract the modules can be defined within the `modules` field of the `InstantiateMsg` like so:

```rust
{
    "modules": [
        {
            "whitelist": {
                "moderators": ["terra1...", "terra1..."]
            }
        },
        {
            "taxable": {
                "tax": 4,
                "receivers": ["terra1...", "terra1..."]
            }
        }
    ]
}
```

Each module has a **Definition **section within their respective documentation that describes the object required to define the module.

## Module Trait

Each `ModuleDefinition` can then be mapped to a struct that implements the `Module` trait using the `as_definition()` function:

```rust
impl ModuleDefinition {
    pub fn as_module(&self) -> Box<dyn Module> {
        match self {
            ModuleDefinition::Whitelist { moderators } => Box::from(Whitelist {
                moderators: moderators.clone(),
            }),
            ModuleDefinition::Taxable { tax, receivers } => Box::from(Taxable {
                tax: tax.clone(),
                receivers: receivers.clone(),
            }),
        }
    }
}

pub trait Module: MessageHooks {
    fn validate(&self, extensions: Vec<ModuleDefinition>) -> StdResult<bool>;
    fn as_definition(&self) -> ModuleDefinition;
}
```

`validate`

Validates whether the module definition is suitable given the other set of modules it is defined alongside. Returns a `StdResult<bool>` indicating whether the module is valid.

| Name    | Type                   | Description                                             |
| ------- | ---------------------- | ------------------------------------------------------- |
| modules | Vec\<ModuleDefinition> | The vector of modules that the module is defined within |

`as_definition`

Returns the module as a `ModuleDefinition` enum.

## MessageHooks

Each module also implements a `MessageHooks` trait which is a set of predefined hook functions for each possible `ExecuteMsg` including a generic `on_execute` function. 

#### HookResponse

Each hook responds with a `HookResponse` struct as follows:

```rust
#[derive(Debug, PartialEq)]
pub struct HookResponse {
    pub msgs: Vec<CosmosMsg>,
    pub events: Vec<Event>,
}

impl HookResponse {
    pub fn default() -> Self {
        HookResponse {
            msgs: vec![],
            events: vec![],
        }
    }
    pub fn add_event(mut self, event: Event) -> Self {
        self.events.push(event);
        self
    }
    pub fn add_message(mut self, message: CosmosMsg) -> Self {
        self.msgs.push(message);
        self
    }
    pub fn add_resp(mut self, resp: HookResponse) -> Self {
        for event in resp.events {
            self.events.push(event);
        }
        self
    }
}
```

The `HookResponse` contains any messages that the hook may wish to send in the response and a list of events used to log anything important that the hook may have done, for example if the `Taxable` module is used it will add an event of what payments were made in the case of an agreed transfer.

`on_instantiate`

A hook called whenever an `Instantiate` message is sent.

```rust
pub fn on_instantiate(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    env: Env,
) -> StdResult<HookResponse> {
    Ok(HookResponse)
}
```

| Name | Type        | Description                                                         |
| ---- | ----------- | ------------------------------------------------------------------- |
| deps | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info | MessageInfo | The standard message info struct provided with any execute message  |
| env  | Env         | The standard environment struct provided with any execute message   |

`on_execute`

{% hint style="danger" %}
Work in Progress
{% endhint %}

A hook called whenever any `ExecuteMsg` is sent. 

```rust
fn on_execute(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    env: Env
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name | Type        | Description                                                         |
| ---- | ----------- | ------------------------------------------------------------------- |
| deps | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info | MessageInfo | The standard message info struct provided with any execute message  |
| env  | Env         | The standard environment struct provided with any execute message   |

`on_mint`

A hook called whenever a `ExecuteMsg::Mint` message is sent.

```rust
fn on_mint(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    env: Env,
    token_id: String,
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name     | Type        | Description                                                         |
| -------- | ----------- | ------------------------------------------------------------------- |
| deps     | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info     | MessageInfo | The standard message info struct provided with any execute message  |
| env      | Env         | The standard environment struct provided with any execute message   |
| token_id | String      | The id of the token being minted                                    |

`on_transfer`

A hook called whenever a `ExecuteMsg::Transfer` is sent. 

```rust
fn on_transfer(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    recipient: String,
    token_id: String,
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name      | Type        | Description                                                         |
| --------- | ----------- | ------------------------------------------------------------------- |
| deps      | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info      | MessageInfo | The standard message info struct provided with any execute message  |
| env       | Env         | The standard environment struct provided with any execute message   |
| recipient | String      | The recipient of the transfer                                       |
| token_id  | String      | The id of the token being transferred                               |

`on_send`

A hook called whenever a `ExecuteMsg::Send` is sent. 

```rust
fn on_send(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    contract: String,
    token_id: String,
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name     | Type        | Description                                                         |
| -------- | ----------- | ------------------------------------------------------------------- |
| deps     | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info     | MessageInfo | The standard message info struct provided with any execute message  |
| env      | Env         | The standard environment struct provided with any execute message   |
| contract | String      | The recipient contract of the transfer                              |
| token_id | String      | The id of the token being transferred                               |

`on_approve`

A hook called whenever a `ExecuteMsg::Approve` is sent. 

```rust
fn on_transfer(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    spender: String,
    token_id: String,
    expires: Option<Expiration>
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name     | Type                | Description                                                         |
| -------- | ------------------- | ------------------------------------------------------------------- |
| deps     | \&DepsMut           | The standard mutable dependencies provided with any execute message |
| info     | MessageInfo         | The standard message info struct provided with any execute message  |
| env      | Env                 | The standard environment struct provided with any execute message   |
| spender  | String              | The new operator                                                    |
| token_id | String              | The id of the token                                                 |
| expires  | Option\<Expiration> | The optional expiration of the approval                             |

`on_revoke`

A hook called whenever a `ExecuteMsg::Revoke` is sent. 

```rust
fn on_revoke(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    spender: String,
    token_id: String,
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name     | Type        | Description                                                         |
| -------- | ----------- | ------------------------------------------------------------------- |
| deps     | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info     | MessageInfo | The standard message info struct provided with any execute message  |
| env      | Env         | The standard environment struct provided with any execute message   |
| spender  | String      | The operator to be revoked                                          |
| token_id | String      | The id of the token                                                 |

`on_approve_all`

A hook called whenever a `ExecuteMsg::ApproveAll` is sent. 

```rust
fn on_approve_all(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    operator: String,
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name     | Type        | Description                                                         |
| -------- | ----------- | ------------------------------------------------------------------- |
| deps     | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info     | MessageInfo | The standard message info struct provided with any execute message  |
| env      | Env         | The standard environment struct provided with any execute message   |
| operator | String      | The address of the new operator                                     |

`on_transfer_agreement`

A hook called whenever a `ExecuteMsg::TransferAgreement` is sent. 

```rust
fn on_transfer_agreement(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    token_id: String,
    purchaser: String,
    amount: u128,
    denom: String
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name      | Type        | Description                                                         |
| --------- | ----------- | ------------------------------------------------------------------- |
| deps      | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info      | MessageInfo | The standard message info struct provided with any execute message  |
| env       | Env         | The standard environment struct provided with any execute message   |
| token_id  | String      | The id of the token                                                 |
| purchaser | String      | The address of the purchaser                                        |
| amount    | u128        | The agreed amount to be paid                                        |
| denom     | String      | The agreed denomination to be paid                                  |

`on_burn`

A hook called whenever a `ExecuteMsg::Burn` is sent. 

```rust
fn on_burn(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    token_id: String,
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name     | Type        | Description                                                         |
| -------- | ----------- | ------------------------------------------------------------------- |
| deps     | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info     | MessageInfo | The standard message info struct provided with any execute message  |
| env      | Env         | The standard environment struct provided with any execute message   |
| token_id | String      | The id of the token                                                 |

`on_archive`

A hook called whenever a `ExecuteMsg::Archive` is sent. 

```rust
fn on_archive(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    token_id: String,
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name     | Type        | Description                                                         |
| -------- | ----------- | ------------------------------------------------------------------- |
| deps     | \&DepsMut   | The standard mutable dependencies provided with any execute message |
| info     | MessageInfo | The standard message info struct provided with any execute message  |
| env      | Env         | The standard environment struct provided with any execute message   |
| token_id | String      | The id of the token                                                 |

`on_agreed_transfer`

A hook called whenever a `ExecuteMsg::Transfer` is sent and the sender is the purchaser in the token's `TransferAgreement`. 

```rust
fn on_approve_all(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    payments: &mut Vec<BankMsg>,
    owner: String,
    purchaser: String,
    amount: Coin
) -> StdResult<HookResponse> {
    Ok(HookResponse::default())
}
```

| Name      | Type                | Description                                                                                                                            |
| --------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| deps      | \&DepsMut           | The standard mutable dependencies provided with any execute message                                                                    |
| info      | MessageInfo         | The standard message info struct provided with any execute message                                                                     |
| env       | Env                 | The standard environment struct provided with any execute message                                                                      |
| payments  | \&mut Vec\<BankMsg> | A mutable vector of payments to be sent following the hook. **Any payments added will be automatically added to the response struct.** |
| owner     | String              | The current owner of the ADO                                                                                                           |
| purchaser | String              | The purchaser of the ADO                                                                                                               |
| amount    | Coin                | The agreed transfer amount of the ADO                                                                                                  |

## Modules Struct

To simplify management of the modules defined in the contract a `Modules` struct is used. This is a simple wrapper around a vector of `ModuleDefinitions` that implements all of the aforementioned hooks. When one of the hooks is called from this struct it will call the same hook on the modules defined within the vector.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Eq)]
pub struct Modules {
    pub module_defs: Vec<ModuleDefinition>,
}
```

The modules are stored within the contract's storage:

```rust
pub const MODULES: Item<Modules> = Item::new("modules");

pub fn store_modules(
    storage: &mut dyn Storage,
    module_defs: Vec<ModuleDefinition>,
) -> StdResult<()> {
    //Validate each module before storing
    let modules = Modules::new(module_defs);
    modules.validate()?;

    MODULES.save(storage, &modules)
}

pub fn read_modules(storage: &dyn Storage) -> StdResult<Modules> {
    let module_defs = MODULES.may_load(storage).unwrap_or_default();

    match module_defs {
        Some(mods) => Ok(mods),
        None => Ok(Modules::default()),
    }
}
```

## Rates

Rates that are used in various modules can be broken in to two categories:

* Flat - A flat amount with a defined denomination that is sent to each receiver
* Percentage - A percentage of the transfer agreement amount

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Eq)]
#[serde(rename_all = "snake_case")]
pub struct FlatRate {
    amount: Uint128,
    denom: String,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Eq)]
#[serde(rename_all = "snake_case")]
pub enum Rate {
    Flat(FlatRate),
    Percent(Uint128),
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
//Flat
{
    "rate": {
        "flat": {
            "amount": "2",
            "denom": "uluna",
        }
    }
}

//Percent
{
    "rate": {
        "percent": "2"
    }
}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
In the case of the Percentage rate the rounding always favours the receivers, rounding up in the case of a remainder. This can result in a fluctuation of +1 (of the defined denom) to the fee amount per receiver.
{% endhint %}
