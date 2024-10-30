---
description: The set of execute messages common to all ADOs in the Andromeda Logic Library.
---

# AndromedaMsg V1.0.0

## AndromedaMsg

All of the ADOs can call most of the base execute messages found in the **AndromedaMsg** enum.

{% hint style="danger" %}
AMP ADOs are the only ADOs that do not implement the AndromedaMsg base executes.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[cw_serde]
pub enum AndromedaMsg {
    Ownership(OwnershipMessage),
    
    UpdateAppContract {
        address: String,
    },
    
    UpdateKernelAddress {
        address: Addr,
    },
    
    #[serde(rename = "amp_receive")]
    AMPReceive(AMPPkt),
    Permissioning(PermissioningMessage),
    }
}
```
{% endtab %}
{% endtabs %}

All the ADOs can execute:&#x20;

* [`Ownership messages`](andromedamsg-v1.0.0.md#ownership)
* [`UpdateAppContract`](andromedamsg-v1.0.0.md#updateappcontract)
* [`UpdateKernelAddress`](andromedamsg-v1.0.0.md#updatekerneladdress)
* [`AMPReceive`](andromedamsg-v1.0.0.md#ampreceive)
* [`Permissioning messages`](andromedamsg-v1.0.0.md#permissioning)

## Ownership

Messages related to the ownership of an ADO.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
   Ownership(OwnershipMessage),
}
```
{% endtab %}
{% endtabs %}

#### OwnershipMessage

The enum containing the different ownership message options.

```rust
pub enum OwnershipMessage {
    UpdateOwner {
        new_owner: Addr,
        expiration: Option<MillisecondsExpiration>,
    },
    RevokeOwnershipOffer,
    AcceptOwnership,
    Disown,
}
```

### UpdateOwner

Offers the ADO's ownership to the specified `new_owner`. Once the offer has been made, the `new_owner` address can call **AcceptOwnership** to accept becoming the owner of the ADO.

{% hint style="warning" %}
Only available to the current ADO owner.

Only one offer can be made at a time.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum OwnershipMessage {
  UpdateOwner {
        new_owner: Addr,
        expiration: Option<MillisecondsExpiration>,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ownership":{
    "update_owner":{
        "new_owner":"andr1...",
        "expiration": 173493923498234
        }
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="178">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>new_owner</code></td><td>Addr</td><td>The address to offer ownership to.</td></tr><tr><td><code>expiration</code></td><td>Option&#x3C;<a href="../common-types.md#milliseconds">MillisecondsExpiration</a>></td><td>An optional expiration to the ownership offer. Uses a Timestamp in milliseconds.</td></tr></tbody></table>

### AcceptOwnership

Accepts the offer to become the new owner of the ADO.

{% hint style="warning" %}
Only an address that has an offer can accept it.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum OwnershipMessage {
 AcceptOwnership
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ownership":{
    "accept_ownership":{}
   }
}
```
{% endtab %}
{% endtabs %}

### RevokeOwnershipOffer

Removes any previously placed offer by the current ADO owner.

{% hint style="warning" %}
Only available to the current ADO owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum OwnershipMessage {
 RevokeOwnershipOffer 
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ownership":{
    "revoke_ownership_offer":{}
   }
}
```
{% endtab %}
{% endtabs %}

### Disown

Removes ownership from the current ADO owner.&#x20;

{% hint style="warning" %}
Only available to the contract owner.

Once disowned, an ADO can never have an owner again.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum OwnershipMessage {
 Disown 
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ownership":{
    "disown":{}
    }
}
```
{% endtab %}
{% endtabs %}

***

### UpdateAppContract

Updates the referenced App contract of an ADO. This allows the ADO to reference by name the components of the new App contract.

{% hint style="warning" %}
Only available to the ADO owner.
{% endhint %}

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

### UpdateKernelAddress

Updates the Kernel used by the ADO.

{% hint style="warning" %}
Only available to the ADO owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
    UpdateKernelAddress {
        address: Addr,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"update_kernel_address":{
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type  | Description                                                   |
| --------- | ----- | ------------------------------------------------------------- |
| `address` | Addr  | The contract address of the new kernel to be used by the ADO. |

### AMPReceive

This message is not called by the user, but is the case that handles receiving [AMP messsages](../andromeda-messaging-protocol/) from the [Kernel](../andromeda-messaging-protocol/kernel.md). It first verifies the AMP packet and then proceeds to execute the attached execute messges. The AMP packet is verified by checking the following:

1. The origin matches the sender
2. The sender is the kernel
3. The sender has a code ID stored within the ADODB (and as such is a valid ADO)

## Permissioning&#x20;

Messages related to the permissioning of an ADO.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
   Permissioning(PermissioningMessage),
}
```
{% endtab %}
{% endtabs %}

#### PermissioningMessage

```rust
pub enum PermissioningMessage {
  PermissionAction {
        action: String,
    },
    SetPermission {
        actor: AndrAddr,
        action: String,
        permission: Permission,
    },
    RemovePermission {
        action: String,
        actor: AndrAddr,
    },
    DisableActionPermissioning {
        action: String,
    },
}
```

### PermissionAction

Enables permissioning on an action.&#x20;

{% hint style="warning" %}
By actions, we are reffering to execute messages.

Only available to the ADO owner.

Permissioning an action will allow you to call SetPermission on that action.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum PermissioningMessage {
      PermissionAction {
        action: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"permissioning":{
    "permission_action":{
        "action":"Mint"
    }
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
Only availabe to the ADO owner.

The action needs to be permissioned by calling [PermissionAction](andromedamsg-v1.0.0.md#permissionaction) to be able to set a permission for it.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum PermissioningMessage {
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
"permissioning"{
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
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                    | Description                                                                                                                          |
| ------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `actor`      | [AndrAddr](../common-types.md#andraddr) | The address to assign permissions for.                                                                                               |
| `action`     | String                                  | The execute message to assign a permission for. Action needs to be capitalized i.e. "UpdateSale" to specify the UpdateSale execute.  |
| `permission` | Permission                              | The type of permission assigned.                                                                                                     |

#### Permission

An enum to represent a user's permission for an action:

{% hint style="warning" %}
Expiration defaults to `Never` if not provided.
{% endhint %}

```rust
pub enum Permission {
    Blacklisted(Option<MillisecondsExpiration>),
    Limited {
        expiration: Option<MillisecondsExpiration>,
        uses: u32,
    },
    Whitelisted(Option<MillisecondsExpiration>),
}
```

{% hint style="warning" %}
All expirations are set as a timestamp in milliseconds using the [MillisecondsExpiration](../common-types.md#milliseconds) type.
{% endhint %}

* **Blacklisted:** The user cannot perform the action until after the provided expiration.
* **Limited:** The user can perform the action while uses are remaining and before the provided expiration.
* **Whitelisted:** The user can perform the action until the provided expiration.

### RemovePermission

Remove a previously assigned permission for the specified actor.

{% hint style="warning" %}
Only available to the ADO owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum PermissioningMessage {
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
"permissioning":{
    "remove_permission":{
        "actor":"andr1...",
        "action":"Mint"
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                    | Description                                                                  |
| -------- | --------------------------------------- | ---------------------------------------------------------------------------- |
| `actor`  | [AndrAddr](../common-types.md#andraddr) | The address that you want to remove permissions for.                         |
| `action` | String                                  | The execute message to have its permission removed for the specified actor.  |

### DisableActionPermissioning

Disables permissioning on an action.&#x20;

{% hint style="warning" %}
This message is the reverse of a **PermissionAction** message.&#x20;

Only available to the ADO owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum PermissioningMessage {
      DisableActionPermissioning {
        action: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"permissioning":{
    "disable_action_permissioning":{
        "action":"Mint"
    }
  }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                             |
| -------- | ------ | ------------------------------------------------------- |
| `action` | String | The action/execute message to remove permissioning for. |
