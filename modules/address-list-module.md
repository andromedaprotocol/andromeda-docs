---
description: >-
  A module used to interact with the Address List contract. Used for both
  Whitelisting and Blacklisting.
---

# Address List Module

## Definition

The address list contains a list of of addresses as operators. These addresses can either whitelisted or blacklisted. The `is_inclusive` field is used as a flag to specify whether to consider the address list as a whitelist or a blacklist.

### InstantiateMsg

The Instantiation message for the Address List contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub operators: Vec<String>,
    pub is_inclusive: bool,
}
```

| Name           | Type         | Description                                                                                                                        |
| -------------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `operators`    | Vec\<String> | A list of addresses that are authorised to interact with the contract.                                                             |
| `is_inclusive` | bool         | Whether or not the address list is inclusive. If `true` the address list is a whitelist, if false the address list is a blacklist. |

More information about the address list contract can be found [here](../ado-classes/address-list.md).

## Hooks

### module\_hook

Sends the provided hook message to all registered modules.

```rust
    pub fn module_hook<T>(
        &self,
        storage: &dyn Storage,
        querier: QuerierWrapper,
        hook_msg: AndromedaHook,
    ) -> Result<Vec<T>, ContractError>
    where
        T: DeserializeOwned,
    {
        let addresses: Vec<String> = self.load_module_addresses(storage)?;
        let mut resp: Vec<T> = Vec::new();
        for addr in addresses {
            let mod_resp: Option<T> = hook_query(querier, hook_msg.clone(), addr)?;
            if let Some(mod_resp) = mod_resp {
                resp.push(mod_resp);
            }
        }

        Ok(resp)
    }

```

### OnExecute

A hook sent to the Address List when performing an execute.

```rust
pub enum AndromedaHook {
    OnExecute {
        sender: String,
        payload: Binary,
    }
```

This would trigger the address list contract to handle this hook message returning a default response or an error if the address is not authorized to perform the execute.

```rust
fn handle_andr_hook(deps: Deps, msg: AndromedaHook) -> Result<Response, ContractError> {
    match msg {
        AndromedaHook::OnExecute { sender, .. } => {
            let is_included = includes_address(deps.storage, &sender)?;
            let is_inclusive = IS_INCLUSIVE.load(deps.storage)?;
            if is_included != is_inclusive {
                Err(ContractError::InvalidAddress {})
            } else {
                Ok(Response::default())
            }
        }
        _ => Err(ContractError::UnsupportedOperation {}),
    }
}
```
