---
description: A module used to create a list of unauthorised addresses
---

# Blacklist

## Definition

The blacklist module has only one configurable field, the `moderators` list.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Blacklist {
    pub moderators: Vec<String>,
}
```
{% endtab %}

{% tab title="Javascript" %}
```javascript
{
    "blacklist": {
        "moderators": ["terra1...", "terra1..."]
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| moderators | Vec&lt;String&gt; | The list of addresses authorised to moderate the blacklist |

## Methods

### `is_moderator`

Used to determine if a given address is a moderator of the blacklist.

```rust
pub fn is_moderator(&self, addr: &String) -> bool {
    self.moderators.contains(addr)
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| addr | &String | The address to check against the moderators list |

### `blacklist_addr`

Adds an address to the list of unauthorised address.

```rust
pub fn blacklist_addr(storage: &mut dyn Storage, addr: &String) -> StdResult<()> {
    BLACKLIST.save(storage, addr.clone(), &true)
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| storage | &mut dyn Storage | The storage mechanism within which the address list is stored |
| addr | String | The address to be blacklisted |

### `remove_blacklist`

Removes an address from the blacklist

```rust
pub fn remove_blacklist(&self, storage: &mut dyn Storage, addr: &String) -> StdResult<()> {
    BLACKLIST.save(storage, addr.clone(), &false)
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| storage | &mut dyn Storage | The storage mechanism within which the address list is stored |
| addr | String | The address for which to remove authorisation |

### `is_blacklisted`

Used to determine is a given address is blacklisted. Returns `StdResult<bool>` indicating whether the address is blacklisted or not.

```rust
pub fn is_blacklisted(&self, storage: &dyn Storage, addr: &String) -> StdResult<bool> {
    match BLACKLIST.load(storage, addr.clone()) {
        Ok(blacklisted) => Ok(blacklisted),
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

## Hooks

The Blacklist module implements the following hooks:

### `on_execute`

Used to determine if the address is authorised to interact with the contract, generates a generic error if the address is not authorised.

```rust
fn on_execute(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    env: Env,
) -> StdResult<HookResponse> {
    require(
        !self.is_blacklisted(deps.storage, &info.sender.to_string())?,
        StdError::generic_err("Address is blacklisted"),
    )?;

    Ok(HookResponse::default())
}
```

## Validation

The Blacklist module has the following validation requirements:

* Must be unique
* Cannot be included alongside a `Whitelist` module



