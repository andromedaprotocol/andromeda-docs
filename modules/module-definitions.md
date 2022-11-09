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

| Name          | Type                            | Description                                                                                                    |
| ------------- | ------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `module_type` | String                          | The name of the module to add. Can be set one of the following: "rates", "offers", "address-list", "receipt" . |
| `address`     | [AndrAddress](broken-reference) | How to instantiate the module.                                                                                 |
| `is_mutable`  | bool                            | Whether the module can be later modified or not.                                                               |

{% hint style="warning" %}
Any ADO that can implement modules uses this `Module` struct in its instantiation  to add the modules to the ADO.&#x20;
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
            "identifier":"juno1..."
              },
          "is_mutable": false
        },
        
        {
          "module_type": "rates",
          "address": {
            "identifier":"my_rates"
            },
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
More details about each of these messages can be found in [AndrReceive/AndrQuery](../platform-and-framework/ado\_base.md#modules)
{% endhint %}

### Module Usage&#x20;

As we know by now, not all ADOs can have modules added to them. Even the ADOs that accept modules do not necessarily accept all modules. Below is a summary on the modules that can be added to each ADO.

<table><thead><tr><th>ADO</th><th data-type="checkbox">Address List</th><th data-type="checkbox">Receipt</th><th data-type="checkbox">Rates</th><th data-type="checkbox">Offers</th></tr></thead><tbody><tr><td><strong>auction</strong></td><td>true</td><td>true</td><td>true</td><td>false</td></tr><tr><td><strong>cw721</strong></td><td>true</td><td>true</td><td>true</td><td>true</td></tr><tr><td><strong>cw20</strong></td><td>true</td><td>false</td><td>false</td><td>false</td></tr><tr><td><strong>rate-limiting-withdrawals</strong></td><td>true</td><td>false</td><td>false</td><td>false</td></tr><tr><td><strong>splitter</strong></td><td>true</td><td>false</td><td>false</td><td>false</td></tr><tr><td><strong>timelock</strong></td><td>true</td><td>false</td><td>false</td><td>false</td></tr><tr><td><strong>weighted-distribution-splitter</strong></td><td>true</td><td>false</td><td>false</td><td>false</td></tr><tr><td><strong>crowdfund</strong></td><td>true</td><td>true</td><td>true</td><td>false</td></tr><tr><td><strong>marketplace</strong></td><td>true</td><td>true</td><td>true</td><td>false</td></tr></tbody></table>
