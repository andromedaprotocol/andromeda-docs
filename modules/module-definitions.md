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
    pub name: Option<String>,
    pub address: AndrAddr,
    pub is_mutable: bool,
}
```

| Name         | Type                                                           | Description                                                                                                                                                                                                                                                                             |
| ------------ | -------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`       | Option\<String>                                                | An optional name to assign the module.                                                                                                                                                                                                                                                  |
| `address`    | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | Reference to the module ADO. Can be either the contract address or the name of the ADO in an App.                                                                                                                                                                                       |
| `is_mutable` | bool                                                           | Whether the module can be later modified or not. This means that if `is_mutable` is set to true, then you can run the base executes [`deregister_module`](../platform-and-framework/ado-base/#deregistermodule) and [`alter _module`](../platform-and-framework/ado-base/#altermodule). |

{% hint style="warning" %}
Any ADO that can implement modules uses this `Module` struct in its instantiation  to add modules to the ADO.&#x20;
{% endhint %}

## Defining Modules

When instantiating an **Andromeda Digital Object** contract, the modules can be defined within the `modules` field of the `InstantiateMsg` like so:

```json
{
  "modules": [
        {
          "name": "address_list",
          "address":"andr1...",
          "is_mutable": false
        },
        
        {
          "name": "rates",
          "address":"rates-1",
          "is_mutable": true
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
More details about each of these messages can be found in [ADO Base](../platform-and-framework/ado-base/) section.
{% endhint %}

### Module Usage&#x20;

As we know by now, not all ADOs can have modules added to them. Even the ADOs that accept modules do not necessarily accept all modules. Below is a summary on the modules that can be added to each ADO.

<table><thead><tr><th width="248">ADO</th><th data-type="checkbox">Address List</th><th data-type="checkbox">Rates</th></tr></thead><tbody><tr><td><strong>auction</strong></td><td>true</td><td>true</td></tr><tr><td><strong>cw721</strong></td><td>true</td><td>true</td></tr><tr><td><strong>cw20</strong></td><td>true</td><td>false</td></tr><tr><td><strong>rate-limiting-withdrawals</strong></td><td>true</td><td>false</td></tr><tr><td><strong>timelock</strong></td><td>true</td><td>false</td></tr><tr><td><strong>weighted-distribution-splitter</strong></td><td>true</td><td>false</td></tr><tr><td><strong>crowdfund</strong></td><td>true</td><td>true</td></tr><tr><td><strong>marketplace</strong></td><td>true</td><td>true</td></tr></tbody></table>
