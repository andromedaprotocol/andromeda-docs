---
description: Messages commonly found in many of the contracts.
---

# AndrReceive/AndrQuery

Many of the contracts will have repeating functionality. To normalize one struct across all contracts we use the AndrRecieve and AndrQuery for certain functionalities that will below. This way each contract doesn't have to independently handle the message.

### AndrRecieve

Many of the contracts have an `AndrReceive` as an execute.

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

An enum lisiting the different types of AndromedaMsg.

```rust
pub enum AndromedaMsg {
    /// Standard Messages
    Receive(Option<Binary>),
    UpdateOwner {
        address: String,
    },
    UpdateOperators {
        operators: Vec<String>,
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
}

```

### UpdateOwner

Updates the owner of the contract.

{% tabs %}
{% tab title="Rust" %}
```
pub enum AndromedaMsg{
 update owner {
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

| Name      | Type         | Description                         |
| --------- | ------------ | ----------------------------------- |
| operators | Vec\<String> | The new addresses of the operators. |

### Withdraw

Withdraws token to a specified `recipient.`

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
            "recipient":"terra1...",
            "tokens_to_withdraw":
            
     }
   }
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                     | Description                 |
| -------------------- | ------------------------ | --------------------------- |
| `recipient`          | Option\<Recipient>       | The recipient of the funds. |
| `tokens_to_withdraw` | Option\<Vec\<Withdrawal> | The tokens to withdraw.     |

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
    Percentage(Uint128),
}
```

### Modules

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
pub enum ExecuteMsg {
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
     "is_mutable": true, 
        }
     }
 } 
```
{% endtab %}
{% endtabs %}

| Name   | Type                                      | Description         |
| ------ | ----------------------------------------- | ------------------- |
| module | [Module](andrreceive-andrquery.md#module) | The module to add.  |

### DeregisterModule

Removes a module from the contract.

{% hint style="info" %}
Only the owner or an operator can execute `DeregisterModule`.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{
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
    "module_idx":"3",
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
pub enum ExecuteMsg{
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
     "is_mutable": true, 
        }
    }
 }
```
{% endtab %}
{% endtabs %}

| Name         | Type                                      | Description                        |
| ------------ | ----------------------------------------- | ---------------------------------- |
| `module_idx` | Uint64                                    | The index of the module to change. |
| `module`     | [Module](andrreceive-andrquery.md#module) | The new module implement.          |

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
    pub owner: String,
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
  "id":"tokenid",jsn
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

| Name      | Type   | Description                                                 |
| --------- | ------ | ----------------------------------------------------------- |
| `module`  | Module | The information of the module are found in a Module struct. |
| `address` | String | The contract address of the module.                         |
|           |        |                                                             |

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
