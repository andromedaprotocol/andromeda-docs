---
description: >-
  A module used to create a list of authorised addresses to interact with a
  contract
---

# Whitelist

## Definition

The whitelist module has only one configurable field, the `moderators` list.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Whitelist {
    pub moderators: Vec<String>,
}
```
{% endtab %}

{% tab title="Javascript" %}
```javascript
{
    "whitelist": {
        "moderators": ["terra1...", "terra1..."]
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| moderators | Vec&lt;String&gt; | The list of addresses authorised to moderate the whitelist |

## Methods

### `is_moderator`

Used to determine if a given address is a moderator of the whitelist.

```rust
pub fn is_moderator(&self, addr: &String) -> bool {
    self.moderators.contains(addr)
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| addr | &String | The address to check against the moderators list |

### `whitelist_addr`

Adds an address to the list of authorised address.

```rust
pub fn whitelist_addr(storage: &mut dyn Storage, addr: &String) -> StdResult<()> {
    WHITELIST.save(storage, addr.clone(), &true)
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| storage | &mut dyn Storage | The storage mechanism within which the address list is stored |
| addr | String | The address to be whitelisted |

### `remove_whitelist`

Removes an address' authorisation status.

```rust
pub fn remove_whitelist(&self, storage: &mut dyn Storage, addr: &String) -> StdResult<()> {
    WHITELIST.save(storage, addr.clone(), &false)
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| storage | &mut dyn Storage | The storage mechanism within which the address list is stored |
| addr | String | The address for which to remove authorisation |

### `is_whitelisted`

Used to determine is a given address is whitelisted. Returns `StdResult<bool>` indicating whether the address is whitelisted or not.

```rust
pub fn is_whitelisted(&self, storage: &dyn Storage, addr: &String) -> StdResult<bool> {
    match WHITELIST.load(storage, addr.clone()) {
        Ok(whitelisted) => Ok(whitelisted),
        Err(e) => match e {
            cosmwasm_std::StdError::NotFound { .. } => Ok(false),
            _ => Err(e),
        },
    }
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| storage | &mut dyn Storage | The storage mechanism within which the address list is stored |
| addr | String | The address for which to check whitelist status |

## Validation

The Whitelist module has the following validation requirements:

* Must be unique
* Cannot be included alongside a `BlackList` module

## Hooks

The Whitelist module implements the following hooks:

### `on_execute`

Used to determine if the address is authorised to interact with the contract, generates a generic error if the address is not authorised.

```rust
fn on_execute(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    env: Env,
    msg: ExecuteMsg,
) -> StdResult<HookResponse> {
    require(
        self.is_whitelisted(deps.storage, &info.sender.to_string())?
            || self.is_moderator(&info.sender.to_string()),
        StdError::generic_err("Address is not whitelisted"),
    )?;

    Ok(HookResponse::default())
}
```

