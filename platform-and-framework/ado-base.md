---
description: >-
  The ADO Base contains all the common functionality applied on all ADOs upon
  instantiation.
---

# ADO Base

### InstantiateMsg

The struct used to hold important information about each instantiated ADO. Implemented as `BaseInstantiateMsg`.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub ado_type: String,
    pub ado_version: String,
    pub operators: Option<Vec<String>>,
    pub modules: Option<Vec<Module>>,
    pub primitive_contract: Option<String>,
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                                                     | Description                                                                                                    |
| -------------------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `ado_type`           | String                                                   | The type of the ADO. Usually, it is the same as the name. It is automatically set when an ADO is instantiated. |
| `ado_version`        | String                                                   | The version of the ADO.                                                                                        |
| `operators`          | Option\<Vec\<String>>                                    | The list of addresses to set as operators on the contract.                                                     |
| `modules`            | Option\<Vec<[Module](../modules/module-definitions.md)>> | The list of modules to add to the ADO.                                                                         |
| `primitive_contract` | Option\<String>                                          | The address of the primitive contract to use for data retrieval.                                               |

Many of the contracts will have repeating functionality. To normalize one struct across all contracts, we use the `AndrRecieve` and `AndrQuery` for certain functionalities that will be shown below. This way each contract doesn't have to independently handle the message.

## AndrRecieve

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

An enum lisiting the different types of `AndromedaMsg`. Not all of these messages can be executed by any contract.&#x20;

The withdraw feature should be enabled in order to execute:

* `Withdraw`

The modules feature should be enabled in order to execute:

* `RegisterModule`
* `DeregisterModule`
* `AlterModule`

The primitive feature should be enabled in order to execute:

* `RefreshAddress`
* `RefreshAddresses`

{% hint style="warning" %}
To check if an ADO uses a certain feature, check the AndrReceive section in that contract's documentation. If nothing is specified then the feature is not available.
{% endhint %}

```rust
pub enum AndromedaMsg {
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
    RefreshAddress {
        contract: String,
    },
    RefreshAddresses {
        limit: Option<u32>,
        start_after: Option<String>,
    },
}
```

### Receive

Receives binary data that will be parsed and executed if valid.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
    Receive(Option<Binary>)
    }
```
{% endtab %}
{% endtabs %}

### UpdateOwner

Updates the owner of the contract.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

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
            "address":"juno1..."
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

{% hint style="warning" %}
Removes previous list of operators.

Only available to the contract owner.
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
            "operators":["juno1...","juno1...",...]
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

Withdraws token to a specified `recipient`. Not common to all ADOs, but only to the ones with withdraw feature enabled.

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
                  "addr":"juno1..."
              },
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

| Name                 | Type                                           | Description                                                        |
| -------------------- | ---------------------------------------------- | ------------------------------------------------------------------ |
| `recipient`          | Option<[Recipient](common-types.md#recipient)> | The recipient of the funds.                                        |
| `tokens_to_withdraw` | Option\<Vec\<Withdrawal>                       | The tokens to withdraw. If not specified all tokens are withdrawn. |

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

## Modules

The following can be executed by the contracts that implement modules:

### RegisterModule

Adds a module to the contract.

{% hint style="warning" %}
Only the owner or an operator can execute RegisterModule.

Each module is assigned a u64 index so as it can be unregistered/altered by the owner.

Only available to the contract owner/operators.
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
 "andr_receive":{
     "register_module": {
          "module":{
               "module_type": "receipt",
               "address":{
                    "identifier":"juno1..."
                    },
               "is_mutable": true
        }
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

{% hint style="warning" %}
Only available to the contract owner/operators.
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
"andr_receive":{
    "deregister_module":{
        "module_idx":"3"
        }
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

{% hint style="warning" %}
Only available to the contract owner/operators.

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
"andr_receive":{
       "alter_module":{
          "module_idx":"3",
          "module":{
             "module_type": "receipt",
             "address":{
                "identifier":"juno1..."
                }
             "is_mutable": false
        }
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
<pre class="language-json"><code class="lang-json">{
"andr_receive":{
   "refresh_addresses":{
<strong>      "limit": 15
</strong>     }
   }
}
</code></pre>
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
    #[returns(Option<Binary>)]
    Get(Option<Binary>),
    #[returns(ContractOwnerResponse)]
    Owner {},
    #[returns(OperatorsResponse)]
    Operators {},
    #[returns(TypeResponse)]
    Type {},
    #[returns(PublisherResponse)]
    OriginalPublisher {},
    #[returns(BlockHeightResponse)]
    BlockHeightUponCreation {},
    #[returns(IsOperatorResponse)]
    IsOperator { address: String },
    #[returns(Module)]
    Module { id: Uint64 },
    #[returns(Vec<String>)]
    ModuleIds {},
    #[returns(VersionResponse)]
    Version {},
}
```

### Get

Takes binary data which is parsed and executes the corresponding query if valid.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(Option<Binary>)]
    Get(Option<Binary>)
    }
```
{% endtab %}
{% endtabs %}

### Owner

Queries the owner of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
    #[returns(ContractOwnerResponse)]
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
"owner":"juno1..."
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
     #[returns(OperatorsResponse)]
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
"operators":["juno1...","juno1...",...]
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
  #[returns(IsOperatorResponse)]
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
     "address":"juno1..."
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

### Type

Queries the Ado\_type.&#x20;

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum AndromedaQuery {
<strong>    #[returns(TypeResponse)]
</strong>    Type {}
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json">{
"andr_query":{
<strong>        "type":{}
</strong><strong>  }
</strong>}
</code></pre>
{% endtab %}
{% endtabs %}

#### TypeResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct TypeResponse {
    pub ado_type: String,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ado_type":"gumball"
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description          |
| ---------- | ------ | -------------------- |
| `ado_type` | String | The type of the ado. |

### BlockHeightUponCreation

Queries the block height when the ADO was created.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
      #[returns(BlockHeightResponse)]
      BlockHeightUponCreation {},
      }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andr_query":{
    "block_height_upon_creation":{}
    }
}
```
{% endtab %}
{% endtabs %}

#### BlockHeightResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct BlockHeightResponse {
    pub block_height: u64,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"block_height": 200134
}
```
{% endtab %}
{% endtabs %}

| Name           | Type | Description                                          |
| -------------- | ---- | ---------------------------------------------------- |
| `block_height` | u64  | The block height at the time of creation of the ADO. |

### Version

Queries the version of the ADO.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
     #[returns(VersionResponse)]
     Version {}
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andr_query":{
        "version":{}
        }
}
```
{% endtab %}
{% endtabs %}

#### VersionResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct VersionResponse {
    pub version: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"version": "0.1.0"
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Descripton               |
| --------- | ------ | ------------------------ |
| `version` | String | The version of the ADO.  |

### OriginalPublisher

Queries the orginal address that published/instantiated the ADO.

{% tabs %}
{% tab title="Rust " %}
```rust
pub enum AndromedaQuery {
    #[returns(PublisherResponse)]
    OriginalPublisher {}
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andr_query":{
    "original_publisher":{}
    }
}
```
{% endtab %}
{% endtabs %}

#### PublisherResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct PublisherResponse {
    pub original_publisher: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"original_publisher":" juno1..."
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type   | Description                                               |
| -------------------- | ------ | --------------------------------------------------------- |
| `original_publisher` | String | The original address that instantiated/published the ADO. |

## Modules

The following can be executed by the contracts that implement modules:

### Module

Queries a module by its Id.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
  #[returns(Module)]
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
     "address": "juno1..."
     },
"address":"juno1..."
}
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
    #[returns(Vec<String>)]
    ModuleIds{}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andr_query":{
    "module_ids":{}
}
```
{% endtab %}
{% endtabs %}

Returns a `Vec<String>` containing the module ids.
