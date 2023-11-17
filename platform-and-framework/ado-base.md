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
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="235.66666666666669">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>ado_type</code></td><td>String</td><td>The type of the ADO. Usually, it is the same as the name. It is automatically set when an ADO is instantiated.</td></tr><tr><td><code>ado_version</code></td><td>String</td><td>The version of the ADO. It is automatically set when an ADO is instantiated.</td></tr><tr><td><code>operators</code></td><td>Option&#x3C;Vec&#x3C;String>></td><td>The list of addresses that are given operator privilege  on the contract. Automatically set to empty when an ADO is intantiated.</td></tr><tr><td><code>kernel_address</code></td><td>Option&#x3C;String></td><td>The contract address of the <a href="andromeda-messaging-protocol/kernel.md">kerne</a><a href="andromeda-messaging-protocol/kernel.md">l</a> ADO responsible for communication between ADOs. Specified by the creator in the instantiation message of an ADO.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>The address of the ADO owner. Specified by the creator in the instantiation message of an ADO. </td></tr></tbody></table>

Our ADOs have a set of execute and query messages refered to as the "base executes" and "base queries". These messages are common to every ADO in our Andromeda Digital Library or ALL. They are listed in the AndromedaMsg and AndromedaQuery enums which we will discuss next. &#x20;

## AndromedaMsg

All of the ADOs can call the base execute messages found in the AndromedaMsg

{% hint style="danger" %}
Some of the execute messages are feature specific meaning they are only available to all the ADOs that implement a specific feature such as the modules feature. Implemented features will be specified in the ADO's documentation page.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[cw_serde]
pub enum AndromedaMsg {
    UpdateOwner {
        address: String,
    },
    UpdateOperators {
        operators: Vec<String>,
    },
    UpdateAppContract {
        address: String,
    },
    #[cfg(feature = "withdraw")]
    Withdraw {
        recipient: Option<Recipient>,
        tokens_to_withdraw: Option<Vec<Withdrawal>>,
    },
    #[cfg(feature = "modules")]
    RegisterModule {
        module: Module,
    },
    #[cfg(feature = "modules")]
    DeregisterModule {
        module_idx: Uint64,
    },
    #[cfg(feature = "modules")]
    AlterModule {
        module_idx: Uint64,
        module: Module,
    },
    Deposit {
        recipient: Option<AndrAddr>,
        msg: Option<Binary>,
    },
    #[serde(rename = "amp_receive")]
    AMPReceive(AMPPkt),
    SetPermission {
        actor: AndrAddr,
        action: String,
        permission: Permission,
    },
    RemovePermission {
        action: String,
        actor: AndrAddr,
    },
    PermissionAction {
        action: String,
    },
}
```
{% endtab %}
{% endtabs %}

All the ADOs can execute:&#x20;

* [`UpdateOwner`](ado-base.md#updateowner)
* [`UpdateOperators`](ado-base.md#updateoperators)
* [`UpdateAppContract`](ado-base.md#updateappcontract)
* [`AMPReceive`](ado-base.md#ampreceive)
* [`SetPermission`](ado-base.md#setpermission)
* [`RemovePermission`](ado-base.md#removepermission)

The modules feature should be enabled for an ADO in order to execute:

* [`RegisterModule`](ado-base.md#registermodule)
* [`DeregisterModule`](ado-base.md#deregistermodule)
* [`AlterModule`](ado-base.md#altermodule)

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
"update_owner":{
     "address":"andr1..."
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
"update_operators":{
     "operators":["andr1...","andr1...",...]
     }
   }
```
{% endtab %}
{% endtabs %}

| Name        | Type         | Description                         |
| ----------- | ------------ | ----------------------------------- |
| `operators` | Vec\<String> | The new addresses of the operators. |

### UpdateAppContract

Updates the referenced App contract of an ADO. This allows the ADO to reference by name the components of the new App contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
  UpdateAppContract {
        address: String,
        },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_app_contract":{
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                          |
| --------- | ------ | ------------------------------------ |
| `address` | String | The address of the new App contract. |

### AMPReceive

This message is not called by the user, but is the case that handles receiving [AMP messsages](andromeda-messaging-protocol/) from the [Kernel](andromeda-messaging-protocol/kernel.md). It first verifies the AMP packet and then proceeds to execute the attached execute messges. The AMP packet is verified by checking the following:

1. The origin matches the sender
2. The sender is the kernel
3. The sender has a code ID stored within the ADODB (and as such is a valid ADO)

## Permissioning&#x20;

The next three base messages allow users to have full control over who is allowed to execute messages in the ADO.&#x20;

### PermissionAction

Enables permissioning on an action.&#x20;

{% hint style="warning" %}
By actions, we are reffering to execute messages.

Permissioning an action will allow you to call SetPermission on that action.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
      PermissionAction {
        action: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"permission_action":{
    "action":"mint"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                               |
| -------- | ------ | ----------------------------------------- |
| `action` | String | The action/execute message to permission. |

### SetPermission

Assigns permissions to the specified actor. The action needs to be permissioned by calling PermissionAction before&#x20;

{% hint style="warning" %}
Only availabe to the contract owner.

The action needs to be permissioned by calling [PermissionAction](ado-base.md#permissionaction) to be able to set a permission for it.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
  SetPermission {
        actor: AndrAddr,
        action: String,
        permission: Permission,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"set_permission":{
    "actor"::"andr1...",
    "action":"Mint",
    "permission":{
        "limited":{
            "uses": 5
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                 | Description                                                                                                                          |
| ------------ | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| `actor`      | [AndrAddr](common-types.md#andraddr) | The address to assign permissions for.                                                                                               |
| `action`     | String                               | The execute message to assign a permission for. Action needs to be capitalized i.e. "UpdateSale" to specify the UpdateSale execute.  |
| `permission` | Permission                           | The type of permission assigned.                                                                                                     |

#### Permission

An enum to represent a user's permission for an action:

{% hint style="warning" %}
Expiration defaults to `Never` if not provided.
{% endhint %}

```rust
pub enum Permission {
    Blacklisted(Option<Expiration>),
    Limited {
        expiration: Option<Expiration>,
        uses: u32,
    },
    Whitelisted(Option<Expiration>),
}
```

* **Blacklisted:** The user cannot perform the action until after the provided expiration.
* **Limited:** The user can perform the action while uses are remaining and before the provided expiration.
* **Whitelisted:** The user can perform the action until the provided expiration.

### RemovePermission

Remove a previously assigned permission for the specified actor.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
     RemovePermission {
        action: String,
        actor: AndrAddr,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"remove_permission":{
    "actor":"andr1...",
    "action":"Mint"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                 | Description                                                                  |
| -------- | ------------------------------------ | ---------------------------------------------------------------------------- |
| `actor`  | [AndrAddr](common-types.md#andraddr) | The address that you want to remove permissions for.                         |
| `action` | String                               | The execute message to have its permission removed for the specified actor.  |

## Modules

The following can be executed by the contracts that implement modules:

### RegisterModule

Adds a module to the contract.

{% hint style="warning" %}
Only the owner/operators can execute RegisterModule.

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
```json
{
   "register_module": {
          "module":{
               "name": "receipt",
               "address":"andr1...",
               "is_mutable": true
        }
     }
   }
```
{% endtab %}
{% endtabs %}

| Name     | Type                                       | Description         |
| -------- | ------------------------------------------ | ------------------- |
| `module` | [Module](../modules/module-definitions.md) | The module to add.  |

### DeregisterModule

Removes a module from the contract.

{% hint style="warning" %}
Only available to the contract owner/operators.

Only mutable modules can be deregistered.&#x20;
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
    "deregister_module":{
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
 "alter_module":{
          "module_idx":"3",
          "module":{
             "module_type": "receipt",
             "address":"andr1...",
             "is_mutable": false
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

***

### Withdraw

Withdraws funds from the ADO. Only available to ADOs with the "withdraw" feature is enabled.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
     Withdraw {
        recipient: Option<Recipient>,
        tokens_to_withdraw: Option<Vec<Withdrawal>>,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw":{
"recipient":{
    "address":"andr1..."
    },
"withdrawals":[
                {
                    "token":"uandr",
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

| Name                 | Type                                               | Description                                                                                      |
| -------------------- | -------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| `recipient`          | Option<[Recipient](common-types.md#recipient)>     | The address to receive the withdrawn funds when withdrawing from vault. Defaults to the sender.  |
| `tokens_to_withdraw` | Option\<Vec<[Withdrawal](ado-base.md#withdrawal)>> | The funds to withdraw. All funds are withdrawn if not specified.                                 |

#### Withdrawal

```rust
pub struct Withdrawal {
    pub token: String,
    pub withdrawal_type: Option<WithdrawalType>,
}
```

| Name              | Type                    | Description             |
| ----------------- | ----------------------- | ----------------------- |
| `token`           | String                  | The token to withdraw.  |
| `withdrawal_type` | Option\<WithdrawalType> | The type of withdrawal. |

#### WithdrawalType

```rust
pub enum WithdrawalType {
    Amount(Uint128),
    Percentage(Decimal),
}
```

There are two main withdrawal types:

* **Amount:** Withdraw a flat amount from the vault/strategy.
* **Percentage:** Withdraw a percentage of funds found in the vault/strategy.

## AndromedaQuery

All of the ADOs can call the base query messages found in the AndromedaMsg.

{% hint style="danger" %}
Some of the query messages are feature specific meaning they are only available to all the ADOs that implement a specific feature such as the modules feature. Implemented features will be specified in the ADO's documentation page.&#x20;
{% endhint %}

```rust
pub enum AndromedaQuery {
    #[returns(self::ownership::ContractOwnerResponse)]
    Owner {},
    #[returns(self::operators::OperatorsResponse)]
    Operators {},
    #[returns(self::ado_type::TypeResponse)]
    Type {},
    #[returns(self::kernel_address::KernelAddressResponse)]
    KernelAddress {},
    #[returns(self::ownership::PublisherResponse)]
    OriginalPublisher {},
    #[returns(self::block_height::BlockHeightResponse)]
    BlockHeightUponCreation {},
    #[returns(self::operators::IsOperatorResponse)]
    IsOperator { address: String },
    #[returns(self::version::VersionResponse)]
    Version {},
    #[returns(Option<::cosmwasm_std::Addr>)]
    AppContract {},
    #[cfg(feature = "modules")]
    #[returns(Module)]
    Module { id: Uint64 },
    #[cfg(feature = "modules")]
    #[returns(Vec<String>)]
    ModuleIds {},
    #[returns(::cosmwasm_std::BalanceResponse)]
    Balance { address: AndrAddr },
    #[returns(Vec<self::permissioning::PermissionInfo>)]
    Permissions {
        actor: AndrAddr,
        limit: Option<u32>,
        start_after: Option<String>,
    },
    #[returns(Vec<String>)]
    PermissionedActions {},
    
}
```

### Owner

Queries the owner of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
    #[returns(self::ownership::ContractOwnerResponse)]
    Owner{}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
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
"owner":"andr1..."
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
   #[returns(self::operators::OperatorsResponse)]
     Operators{}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "operators":{}
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
"operators":["andr1...","andr1...",...]
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
  #[returns(self::operators::IsOperatorResponse)]
  IsOperators{
      address:String,
  }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
   "is_operators":{
     "address":"andr1..."
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

Queries the ADO type.&#x20;

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum AndromedaQuery {
<strong>    #[returns(self::ado_type::TypeResponse)]
</strong>    Type {}
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json">{
<strong>"type":{}
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
"ado_type":"auction"
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description          |
| ---------- | ------ | -------------------- |
| `ado_type` | String | The type of the ado. |

### KernelAddress

Queries the kernel address of the chain the ADO is deployed on.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(self::kernel_address::KernelAddressResponse)]
    KernelAddress {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"kernel_address":{}
}
```
{% endtab %}
{% endtabs %}

Returns a String containing the contract address of the Kernel.

### BlockHeightUponCreation

Queries the block height when the ADO was created.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
      #[returns(self::block_height::BlockHeightResponse)]
      BlockHeightUponCreation {},
      }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"block_height_upon_creation":{}
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
"block_height": 2000134
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
     #[returns(self::version::VersionResponse)]
     Version {}
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"version":{}
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

### AppContract

### OriginalPublisher

Queries the orginal address that published/instantiated the ADO.

{% tabs %}
{% tab title="Rust " %}
```rust
pub enum AndromedaQuery {
    #[returns(self::ownership::PublisherResponse)]
    OriginalPublisher {}
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"original_publisher":{}
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
"original_publisher":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type   | Description                                               |
| -------------------- | ------ | --------------------------------------------------------- |
| `original_publisher` | String | The original address that instantiated/published the ADO. |

### Permisions

Queries the permissions set for the specified actor address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(Vec<self::permissioning::PermissionInfo>)]
    Permissions {
        actor: AndrAddr,
        limit: Option<u32>,
        start_after: Option<String>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"permissions":{
    "actor":"andr1...",
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                 | Description                                                                                             |
| ------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------- |
| `actor`       | [AndrAddr](common-types.md#andraddr) | The address we are checking permissions for.                                                            |
| `limit`       | Option\<u32>                         | Optional limit to the number of permissions returned. Defaults to 25 and can be set to a maximum of 50. |
| `start_after` | Option\<String>                      | Optional permission to start after. Used for pagination.                                                |

#### PermissionInfo

Returns a vector of the PermissionInfo struct containing all the permissions for the actor.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct PermissionInfo {
    pub permission: Permission,
    pub action: String,
    pub actor: String,
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                 | Description                                       |
| ------------ | ------------------------------------ | ------------------------------------------------- |
| `permission` | [Permission](ado-base.md#permission) | The permission that the actor was given.          |
| `action`     | String                               | The action or message that the permission is for. |
| `actor`      | String                               | The address that has these permissions.           |

### PermissionedActions

Queries the actions or execute messages that are permissioned in the ADO.&#x20;

{% hint style="warning" %}
The actions that are permissioned are the ones that have [PermissionAction](ado-base.md#permissionaction) called on them.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(Vec<String>)]
    PermissionedActions {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"permissioned_actions":{}
}
```
{% endtab %}
{% endtabs %}

Returns a Vector of String containing the actions that are permissioned.&#x20;

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
   "module":{
     "id":"tokenid"
     }
}
```
{% endtab %}
{% endtabs %}

| Name | Type   | Description                    |
| ---- | ------ | ------------------------------ |
| `id` | Uint64 | The Id of the module to query. |

Returns a [Module](../modules/module-definitions.md#module-definitions) struct with the module information.&#x20;

### ModuleIds

Queries all of the module Ids.

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
"module_ids":{}
}
```
{% endtab %}
{% endtabs %}

Returns a `Vec<String>` containing the module Ids.
