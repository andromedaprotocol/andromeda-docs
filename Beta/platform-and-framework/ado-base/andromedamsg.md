---
description: The set of execute messages common to all ADOs in the Andromeda Logic Library.
---

# AndromedaMsg

## AndromedaMsg

All of the ADOs can call most of the base execute messages found in the **AndromedaMsg** enum.

{% hint style="danger" %}
AMP ADOs are the only ADOs that do not implement all the AndromedaMsg base executes.

Rates messages are only available to ADOs that implement Rates. This would be specified in the ADOs documentaion page.
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
    #[cfg(feature = "rates")]
    Rates(self::rates::RatesMessage),
    
    }
}
```
{% endtab %}
{% endtabs %}

All the ADOs can execute:&#x20;

* [`Ownership messages`](andromedamsg.md#ownership)
* [`UpdateAppContract`](andromedamsg.md#updateappcontract)
* [`UpdateKernelAddress`](andromedamsg.md#updatekerneladdress)
* [`AMPReceive`](andromedamsg.md#ampreceive)
* [`Permissioning messages`](andromedamsg.md#permissioning)

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
        expiration: Option<Expiry>,
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
        expiration: Option<Expiry>,
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
        "expiration":{
            "from_now":"6000000"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="178">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>new_owner</code></td><td>Addr</td><td>The address to offer ownership to.</td></tr><tr><td><code>expiration</code></td><td>Option&#x3C;<a href="../common-types.md#expiry-and-milliseconds">Expiry</a>></td><td>An optional expiration to the ownership offer. </td></tr></tbody></table>

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

Messages related to the permissioning of an ADO. Permissioning allows ADO owners to give/restrict access to addresses to execute messages on their ADOs.

{% hint style="warning" %}
You can use the Address List ADO to set up permissions.

Permissioning cannot bypass owner restricted messages.
{% endhint %}

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
        actors: Vec<AndrAddr>,
        action: String,
        permission: Permission,
    },
    RemovePermission {
        action: String,
        actors: Vec<AndrAddr>,
    },
    DisableActionPermissioning {
        action: String,
    },
}
```

### PermissionAction

Makes an action permissioned. This means that if an address is not whitelisted using [Set Permission](andromedamsg.md#setpermission), it cannot call the action.

{% hint style="warning" %}
By actions, we are reffering to execute messages.

Only available to the ADO owner.

**PermissionAction**  blacklists all addresses from calling the action unless they are whitelisted to do so.
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
        "action":"SetValue"
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

Assigns permissions to the specified actor. In case of whitelists, the action needs to be permissioned first by calling [PermissionAction](andromedamsg.md#permissionaction).

{% hint style="warning" %}
Only availabe to the ADO owner.

In case of blacklisting, there is no need to call PermissionAction.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum PermissioningMessage {
  SetPermission {
        actors: Vec<AndrAddr>,
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
        "actors":["andr1...","and1...",...],
        "action":"Buy",
        "permission":{
            "local":{
            "limited":{
                "uses": 5
                }
             }
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                     | Description                                                                                                                          |
| ------------ | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `actor`      | [AndrAddr](../common-types.md#andraddr)  | The address to assign permissions for.                                                                                               |
| `action`     | String                                   | The execute message to assign a permission for. Action needs to be capitalized i.e. "UpdateSale" to specify the UpdateSale execute.  |
| `permission` | [Permission](andromedamsg.md#permission) | The type of permission assigned.                                                                                                     |

#### Permission

An enum to represent a user's permission for an action:

{% hint style="warning" %}
Expiration defaults to `Never` if not provided.
{% endhint %}

```rust
pub enum Permission {
    Local(LocalPermission),
    Contract(AndrAddr),
}
```

* **Local:** The permissions are directly set from this ADO. You would directly specify the type of permission in this case.&#x20;
* **Contract:** The permissions are taken from an [Address List ADO](../../andromeda-digital-objects/address-list.md). You would reference the Address List in this case.

**LocalPermission**

```rust
pub enum LocalPermission {
    Blacklisted(Option<Expiry>),
    Limited {
        expiration: Option<Expiry>,
        uses: u32,
    },
    Whitelisted(Option<Expiry>),
}
```

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
        actor: Vec<AndrAddr>,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"permissioning":{
    "remove_permission":{
        "actors":["andr1...","andr1...",...],
        "action":"Mint"
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                         | Description                                                                  |
| -------- | -------------------------------------------- | ---------------------------------------------------------------------------- |
| `actor`  | Vec<[AndrAddr](../common-types.md#andraddr)> | The address that you want to remove permissions for.                         |
| `action` | String                                       | The execute message to have its permission removed for the specified actor.  |

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

## Rates

Sets a rate on a specific action in the ADO. Rates are used to add taxes or royalties on ADOs where it makes sense such as NFT sales for example. Here is the list of actions that can have rates applied to them:

| ADO             | Messages                                  | Usage                                                                                                        |
| --------------- | ----------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **Marketplace** | `Buy`                                     | Add a fee on NFT sales.                                                                                      |
| **Auction**     | `Claim`                                   | Add a fee on auction sales.                                                                                  |
| **CW721**       | `Transfer`                                | Add a fee in case of a transfer agreement sale.                                                              |
| **CW20**        | `Send`, `Transfer, SendFrom,TransferFrom` | Add a fee on token transfers. You have to set rates on each message to include rates on all token transfers. |
| **Primitive**   | `SetValue`                                | Add a fee for setting a value in the primitive. Can only be a flat rate.                                     |

{% hint style="warning" %}
Only available to the ADOs that implement rates.

You can use the [Rates](../../andromeda-digital-objects/rates.md) ADO to set up rates.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaMsg {
    #[cfg(feature = "rates")]
    Rates(self::rates::RatesMessage),
}
```
{% endtab %}
{% endtabs %}

#### RatesMessage

```rust
#[cw_serde]
pub enum RatesMessage {
    SetRate { action: String, rate: Rate },
    RemoveRate { action: String },
}
```

### SetRate

Sets a rate on the specified action.

{% hint style="info" %}
Check the [table above ](andromedamsg.md#rates)to see which actions can have rates applied.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum RatesMessage {
    SetRate {
     action: String,
     rate: Rate 
      },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"rates":{
    "set_rate":{
    "action":"Buy",
    "rate":{
        "local":{
            "rate_type":"additive",
            "recipients":["andr1...","andr2..."],
            "value":{
                "percent":{
                    "percent": "0.05"
                    }
                },
            "description":"a 5% tax on minting"
            }
          }
        }
    }
  }
```
{% endtab %}
{% endtabs %}

| Name     | Type                         | Description                                               |
| -------- | ---------------------------- | --------------------------------------------------------- |
| `action` | String                       | The execute message to add rates on.                      |
| `rate`   | [Rate](andromedamsg.md#rate) | The type of rate. Either local or taken from a rates ADO. |

#### Rate

```rust
pub enum Rate {
    Local(LocalRate),
    Contract(AndrAddr),
}
```

* **Local:** The rate configurations are specified within the ADO itself.
* **Contract:** The rate configurations are taken from a rates ADO. In this case, you need to reference the [rates ADO ](../../andromeda-digital-objects/rates.md)to take the configurations from using an [AndrAddr](../common-types.md#andraddr).

#### Local

```rust
pub struct LocalRate {
    pub rate_type: LocalRateType,
    pub recipients: Vec<Recipient>,
    pub value: LocalRateValue,
    pub description: Option<String>,
}
```

| Name          | Type                                             | Description                                                                                                                                                                                    |
| ------------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rate_type`   | [LocalRateType](andromedamsg.md#localratetype)   | The type of rate.                                                                                                                                                                              |
| `recipients`  | Vec<[Recipients](../common-types.md#recipient)>  | The addresses to recieve the rates. Each address specified will receive the full amount of rate. For example, having a rate of 5% and 2 recipients means each address will receive a 5% rate.  |
| `value`       | [LocalRateValue](andromedamsg.md#localratevalue) | The amount of funds taken.                                                                                                                                                                     |
| `description` | Option\<String>                                  | Optional description for the purpose of the rate.                                                                                                                                              |

#### LocalRateType

An enum specifying the type of the rate.

```rust
pub enum LocalRateType {
    Additive,
    Deductive,
}
```

* **Additive:** The rate amount acts as a tax and is added to the price and payed by the buyer. For example a 10% additive rate (with one recipient) on a price of 1000 uandr means the buyer has to send 1100 uandr.
* **Deductive:** The rate amount acts as a royalty and is deducted from the price. For example a 10% deductive rate on a price of 1000 uandr means the buyer needs to send 1000 uand and 100 will be taken to the recipient of the rate.

#### LocalRateValue

{% hint style="warning" %}
Percentage is specified as a decimal. This means a rate of 10% is specified as 0.1 and not 10.
{% endhint %}

```rust
pub enum LocalRateValue {
    Percent(PercentRate),
    Flat(Coin),
} 

pub struct PercentRate {
    pub percent: Decimal,
}
```

* **Percent:** The rate is specified as a percentage of the price using decimal.
* **Flat:** The rate is specified as a fixed amount of [Coin](../common-types.md#coin).

### RemoveRate

Removes the rate set on the specified action.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum RatesMessage {
  RemoveRate {
    action: String 
  },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"rates":{
    "remove_rate":{
        "action":"Claim"
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                    |
| -------- | ------ | ---------------------------------------------- |
| `action` | String | The execute message to remove the rates from.  |
