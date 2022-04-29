---
description: Executes commonly found in many of the contracts.
---

# AndrReceive/AndrQuery

Many of the contracts will have repeating functionality. To normalize one struct across all contracts, we use the `AndrRecieve` and `AndrQuery` for certain functionalities that will be shown below. This way each contract doesn't have to independently handle the message.

### AndrRecieve

All of the contracts have an `AndrReceive` as an execute.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  AndrReceive(AndromedaMsg)
  }
```
{% endtab %}
{% endtabs %}

### AndromedaMsg

An enum lisiting the different types of `AndromedaMsg`. Not all of these messages can be executed by any contract. Some are specific to ADOs that use modules or primitives.

```rust
pub enum AndromedaMsg {
    Receive(Option<Binary>),
    UpdateOwner {
        address: String,
    },
    UpdateOperators {
        operators: Vec<String>,
    },
    UpdateMissionContract {
        address: String,
    },
    Withdraw {
        recipient: Option<Recipient>,
        tokens_to_withdraw: Option<Vec<Withdrawal>>,
    },
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
    RefreshAddress {
        contract: String,
    },
    RefreshAddresses {
        limit: Option<u32>,
        start_after: Option<String>,
    },
}
```

### UpdateOwner

Updates the owner of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg{
   UpdateOwner {
   address:String
   }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "andr_receive":{
      "update_owner":{
            "address":"terra1..."
     }
   }
}

```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                   |
| --------- | ------ | ----------------------------- |
| `address` | String | The address of the new owner. |

### UpdateOperators

Updates the assigned operators of the contract.

{% hint style="info" %}
Removes previous list of operators.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg{
 UpdateOperators {
        operators: Vec<String>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "andr_receive":{
      "update_operators":{
            "operators":["terra1...","terra1...",...]
     }
   }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type         | Description                         |
| ----------- | ------------ | ----------------------------------- |
| `operators` | Vec\<String> | The new addresses of the operators. |

### Withdraw

Withdraws token to a specified `recipient`. Not common to all ADOs.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg{
  Withdraw {
        recipient: Option<Recipient>,
        tokens_to_withdraw: Option<Vec<Withdrawal>>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "andr_receive":{
      "withdraw":{
            "recipient":{
                  "addr":"terra1...",
              }
            "tokens_to_withdraw":[
                {
                    "token":"uusd",
                    "withdrawal_type":{
                        "percentage":"0.3"
                        }
                    },
                ...
          ]          
   }
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                                              | Description                 |
| -------------------- | ------------------------------------------------- | --------------------------- |
| `recipient`          | Option<[Recipient](../common-types/recipient.md)> | The recipient of the funds. |
| `tokens_to_withdraw` | Option\<Vec\<Withdrawal>                          | The tokens to withdraw.     |

#### Withdrawal

```rust
pub struct Withdrawal {
    pub token: String,
    pub withdrawal_type: Option<WithdrawalType>,
}
```

#### WithdrawalType

Withdrawal can be a certain amount or a percentage of the tokens.

```rust
pub enum WithdrawalType {
    Amount(Uint128),
    Percentage(Decimal),
}
```

### UpdateMissionContract

Changes the contract address of the mission contract.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg{
   UpdateMissionContract {
   address:String
   }
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
 "andr_receive":{
      "update_mission_contract":{
            "address":"terra1...",
     }
   }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                                         |
| --------- | ------ | --------------------------------------------------- |
| `address` | String | The address of the new mission contract to be used. |

## Modules

The following can be executed by the contracts that implement modules:

### RegisterModule

Adds a module to the contract.

{% hint style="info" %}
Only the owner or an operator can execute RegisterModule.

Each module is assigned a u64 index so as it can be unregistered/altered by the owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
  RegisterModule {
        module: Module,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
 "register_module": {
      "module":{
          "module_type": "receipt"
          "instantiate":{
          "address":"terra1..."
          }
     "is_mutable": true
        }
     }
 } 
```
{% endtab %}
{% endtabs %}

| Name   | Type                                       | Description         |
| ------ | ------------------------------------------ | ------------------- |
| module | [Module](../modules/module-definitions.md) | The module to add.  |

### DeregisterModule

Removes a module from the contract.

{% hint style="info" %}
Only the owner or an operator can execute `DeregisterModule`.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum AndromedaMsg{
  DeregisterModule {
        module_idx: Uint64,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deregistering_module":{
    "module_idx":"3"
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type   | Description                            |
| ------------ | ------ | -------------------------------------- |
| `module_idx` | Uint64 | The index of the module to be removed. |

### AlterModule

Changes a module to a new one specified.

{% hint style="info" %}
Only the owner or an operator can execute `AlterModule`.

Only mutable modules can be altered.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg{
 AlterModule {
        module_idx: Uint64,
        module: Module,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"alter_module":{
   "module_idx":"3",
   "module":{
          "module_type": "receipt",
          "instantiate":{
          "address":"terra1..."
          },
     "is_mutable": true
        }
    }
 }
```
{% endtab %}
{% endtabs %}

| Name         | Type                                       | Description                        |
| ------------ | ------------------------------------------ | ---------------------------------- |
| `module_idx` | Uint64                                     | The index of the module to change. |
| `module`     | [Module](../modules/module-definitions.md) | The new module implement.          |

### Primitives

The following messages are used by contracts that implement primitives.

### RefreshAddress

Used to save the value for the specified `contract` in the cache to be used later on in the contract. Queries the primitive contract and saves the result in the cache.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum AndromedaMsg
 RefreshAddress {
        contract: String,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"refresh_address":"anchor"
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                             |
| ---------- | ------ | --------------------------------------- |
| `contract` | String | The key used in the primitive contract. |

### RefreshAddresses

Similar to `RefreshAddress`, but is used to save all the values from the primitive to the cache.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum AndromedaMsg{
   RefreshAddresses {
        limit: Option<u32>,
        start_after: Option<String>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"refresh_addresses":{
"limit": 15,
   }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                   |
| ------------- | --------------- | --------------------------------------------------------------------------------------------- |
| `limit`       | Option<32>      | An optional limit to the number of addresses to refresh. Defaults to 10. The max limit is 20. |
| `start_after` | Option\<String> | An optional address for which to start after, used for pagination.                            |

## AndrQuery

```rust
pub enum QueryMsg {
    AndrQuery(AndromedaQuery),
}
```

### AndromedaQuery

```rust
pub enum AndromedaQuery {
    Get(Option<Binary>),
    Owner {},
    Operators {},
    IsOperator { address: String },
    Module { id: Uint64 },
    ModuleIds {},
}
```

### Owner

Queries the owner of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
Owner{}
}
```
{% endtab %}

{% tab title="Json" %}
```json
{
"andr_query":{
"owner":{}
  }
}

```
{% endtab %}
{% endtabs %}

#### ContractOwnerResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ContractOwnerResponse {
    pub owner: String
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"owner":"terra1..."
}
```
{% endtab %}
{% endtabs %}

### Operators

Queries the operators of a contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
Operators{}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andr_query":{
"operators":{}
  }
}
```
{% endtab %}
{% endtabs %}

#### OperatorsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct OperatorsResponse {
    pub operators: Vec<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"operators":["terra1...","terra1...",...]
}
```
{% endtab %}
{% endtabs %}

### IsOperator

Checks if  the specified `address` is an operator of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
IsOperators{
  address:String,
  }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andr_query":{
"is_operators":{
  "address":"terra1...",
     }
  }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                       |
| --------- | ------ | --------------------------------- |
| `address` | String | The address to check as operator. |

#### IsOperatorResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct IsOperatorResponse {
    pub is_operator: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"is_operator": true
}
```
{% endtab %}
{% endtabs %}

## Modules

The following can be executed by the contracts that implement modules:

### Module

Queries a module by its Id.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
Module{
  id:Uint64,
  }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andr_query":{
"module":{
  "id":"tokenid"
     }
  }
}

```
{% endtab %}
{% endtabs %}

| Name | Type   | Description                    |
| ---- | ------ | ------------------------------ |
| `id` | Uint64 | The Id of the module to query. |

#### ModuleInfoWithAddress

Struct used to represent a module and its currently recorded address**.**

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ModuleInfoWithAddress {
    pub module: Module,
    pub address: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"module":{
  "module_type":"address_list",
  "instantiate":{
     "address": "terra1...",
     }
"address":"terra1...",
```
{% endtab %}
{% endtabs %}

| Name      | Type                                       | Description                                                 |
| --------- | ------------------------------------------ | ----------------------------------------------------------- |
| `module`  | [Module](../modules/module-definitions.md) | The information of the module are found in a Module struct. |
| `address` | String                                     | The contract address of the module.                         |
|           |                                            |                                                             |

### ModuleIds

Queries all of the module ids.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
ModuleIds{}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andr_query":{
"module_ids":{},
}
```
{% endtab %}
{% endtabs %}

Returns a `Vec<String>` containing the module ids.
