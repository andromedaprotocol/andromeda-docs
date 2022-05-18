---
description: >-
  An Andromeda Module is a struct that can be attached using the Instantiate
  Message to our ADOs and accessed in any standard Execute Message via generic
  hooks.
---

# Module Definitions

## Module Definitions

A struct describing a token module, provided with the instantiation message this struct is used to record the info about the module and how/if it should be instantiated.

```rust
pub struct Module {
    pub module_type: String,
    pub address: AndrAddress,
    pub is_mutable: bool,
}
```

| Name          | Type                                                    | Description                                                                                                     |
| ------------- | ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `module_type` | String                                                  | The name of the module to add. Can be set one of the following: "rates", "offers", "address\_list", "receipt" . |
| `address`     | [AndrAddress](../common-types/recipient.md#andraddress) | How to instantiate the module.                                                                                  |
| `is_mutable`  | bool                                                    | Whether the module can be later modified or not.                                                                |

{% hint style="info" %}
Any ADO that can implement modules uses this `Module` struct in it's Instantiation  to add the modules to the ADO.&#x20;
{% endhint %}

### AndrAdress

A struct used to reference  another ADO contract. Can be either an address, or identifier of an ADO in an app.&#x20;

```rust
pub struct AndrAddress {
    pub identifier: String,
 }
```

## Defining Modules

When instantiating an **Andromeda Digital Object** contract the modules can be defined within the `modules` field of the `InstantiateMsg` like so:

```json
{
  "modules": [
        {
          "module_type": "address_list",
          "address": {
          "identifier":"terra1..."
          }
          "is_mutable": false
        },
        
         {
          "module_type": "rates",
          "address": {
          "identifier":"my_rates",
          },
          "is_mutable": true,
        }
      ]
}
```

### Changing/Querying Modules.

Any contract that implements modules may implement the following messages:

```rust
pub enum AndromedaMsg {
    RegisterModule {
        module: Module,
    },
    DeregisterModule {
        module_idx: Uint64,
    },
    AlterModule {
        module_idx: Uint64,
        module: Module,
    },
}

pub enum AndromedaQuery {
    Module { id: Uint64 },
    ModuleIds {},
}

```

{% hint style="info" %}
More details about each of these messages can be found in [AndrReceive/AndrQuery](../ado\_base/andrreceive-andrquery.md#modules)
{% endhint %}

### Validating Modules

Validates `self` by checking that it is unique, does not conflict with any other module, and does not conflict with the creating ADO.

```rust
impl Module {
    pub fn validate(&self, modules: &[Module], ado_type: &str) -> Result<(), ContractError> {
        require(self.is_unique(modules), ContractError::ModuleNotUnique {})?;

        if ado_type == "cw20" && contains_module(modules, AUCTION) {
            return Err(ContractError::IncompatibleModules {
                msg: "An Auction module cannot be used for a CW20 ADO".to_string(),
            });
        }

        Ok(())
    }
```
