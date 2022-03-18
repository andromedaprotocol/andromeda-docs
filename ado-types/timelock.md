---
description: An ADO contract to hold funds for a set period of time.
---

# Timelock

## Escrow

The time lock contract uses a basic struct to store a record of funds being held.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Escrow {
    pub coins: Vec<Coin>,
    pub condition: Option<EscrowCondition>,
    pub recipient: Recipient,
}
```

| Name        | Type                                                   | Description                                               |
| ----------- | ------------------------------------------------------ | --------------------------------------------------------- |
| `coins`     | Vec\<Coin>                                             | Funds being held within the Escrow.                       |
| `condition` | Option<[EscrowCondition](timelock.md#escrowcondition)> | Optional condition for the Escrow.                        |
| `recipient` | Recipient                                              | The recipient of the funds once `condition` is satisfied. |

#### EscrowCondition

Enum used to specify the condition which must be met in order for the Escrow to unlock.

```rust
pub enum EscrowCondition {
    Expiration(Expiration),
    MinimumFunds(Vec<Coin>),
}
```

| EscrowCondition Type | Type       | Description                                         |
| -------------------- | ---------- | --------------------------------------------------- |
| Expiration           | Expiration | Requires a given time or block height to be reached |
| MinimumFunds         | Vec\<Coin> | Requires a minimum amount of funds to be deposited  |

### Recipient

Enum to define the recipient. Can be either an address or another ADO.&#x20;

```rust
pub enum Recipient {
    Addr(String),
    ADO(ADORecipient),
}
```

### ADORecipient

Struct used when the recipient is an ADO.

```rust
pub struct ADORecipient {
    pub addr: String,
    pub msg: Option<Binary>,
}
```

{% hint style="info" %}
ADOs use a default Receive message for handling funds, this struct states that the recipient is an ADO and may attach the data field to the Receive message
{% endhint %}

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub address_list: Option<AddressListModule>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "address_list": {
        "code_id": 1,
        "operators": ["terra1..."],
        "inclusive": false
    }
}

//OR

{
    "address_list": {
        "address": "terra1...",
        "inclusive": false
    }
}

//OR

{
    "address_list": null
}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                                  | Description                                                       |
| ------------- | ----------------------------------------------------- | ----------------------------------------------------------------- |
| address\_list | Option<[AddressListModule](../modules/address-list/)> | An optional address list module to restrict usage of the contract |

## ExecuteMsg

### HoldFunds

Holds sent funds in escrow.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    HoldFunds {
        condition: Option<EscrowCondition>,
        recipient: Option<Recipient>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "hold_funds": {
        "recipient": "terra1...",
        "condition": {
            "expiration": {
              "at_height": 1
              }
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                                                   | Description                                                    |
| ----------- | ------------------------------------------------------ | -------------------------------------------------------------- |
| `recipient` | Option<[Recipient](timelock.md#recipient)>             | Optional recipient address. If not set defaults to the sender. |
| `condition` | Option<[EscrowCondition](timelock.md#escrowcondition)> | An optional condition to unlock the Escrow                     |

### ReleaseFunds

Releases any held funds for the sender.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    ReleaseFunds {
    recipient_addr:Option<String>,
    start_after:Option<String>,
    limit:Option<32>
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "release_funds": {
    
       "recipient_addr":"terra1...",
       "limit":"15"
    }
}
```
{% endtab %}
{% endtabs %}

| Name             | Type            | Description                                                                                                    |
| ---------------- | --------------- | -------------------------------------------------------------------------------------------------------------- |
| `recipient_addr` | Option\<String> | Optional address to receive the released funds. Will default to the sender if not specified.                   |
| `start_after`    | Option\<String> | An optional address for which to start after, used for pagination.                                             |
| `limit`          | Option<32>      | Optional limit to the number timelocks to attempt to unlock. Defaults to 10 and can be set to a maximum of 30. |

### ReleaseSpecificFunds

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
ReleaseSpecificFunds {
        owner: String,
        recipient_addr: Option<String>,
    }
  } 
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"release_specific_funds": {
   "owner":"terra1...",
   "recipient_addr": "terra1..."
        }
     }
  
```
{% endtab %}
{% endtabs %}

| Name             | Type            | Description                                                                                  |
| ---------------- | --------------- | -------------------------------------------------------------------------------------------- |
| `owner`          | String          | The address of the funds to be released.                                                     |
| `recipient_addr` | Option\<String> | Optional address to receive the released funds. Will default to the sender if not specified. |

### UpdateAddressList

Updates the `address_list` config field.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
   UpdateAddressList { 
      address_list: Option<AddressListModule>
   },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "updated_address_list": {
        "address_list": {
            "code_id": 1,
            "operators": ["terra1...", "terra1..."]
            "inlcusive":false,
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name           | Type                                                  | Description                                                        |
| -------------- | ----------------------------------------------------- | ------------------------------------------------------------------ |
| `address_list` | Option<[AddressListModule](../modules/address-list/)> | An optional address list module for limiting authorised addresses. |

### UpdateOwner/UpdateOperators

Check [AndrReceive](../andrreceive-andrquery.md).

## QueryMsg

### GetLockedFunds

Query any held funds for an address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    GetLockedFunds{
        owner: String,
        recipient:String
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "get_locked_funds": {
        "owner": "terra1..."
        "recipient":"terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                               |
| ----------- | ------ | ----------------------------------------- |
| `owner`     | String | The address of the owner of the funds     |
| `recipient` | String | The address of the recipient of the funds |

#### GetLockedFundsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct GetLockedFundsResponse {
    pub funds: Option<Escrow>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "funds": {
        "coins": {
            "uluna": 1000
        },
        
        "recipient": "terra1...",
        
        "condition": {
            "expiration": {
              "at_height": 1
              }
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type                                 | Description                                                  |
| ----- | ------------------------------------ | ------------------------------------------------------------ |
| funds | Option<[Escrow](timelock.md#escrow)> | Optional Escrow with the held funds and related information. |

### GetLockedFundsForRecipient

{% tabs %}
{% tab title="Rust" %}
```rust
   pub enum QueryMsg {
   GetLockedFundsForRecipient {
        recipient: String,
        start_after: Option<String>,
        limit: Option<u32>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "get_locked_funds_for_recipient":{
   "recipient":"terra1...",
   "limit":"15"
   }
 }
       
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                                   |
| ------------- | --------------- | ------------------------------------------------------------------------------------------------------------- |
| `recipient`   | String          | The address of the recipient                                                                                  |
| `start_after` | Option\<String> | An optional address for which to start after, used for pagination.                                            |
| `limit`       | Option          | Optional limit to the number timelocks to attempt to query. Defaults to 10 and can be set to a maximum of 30. |

#### GetLockedFundsForRecipientResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct GetLockedFundsForRecipientResponse {
    pub funds: Vec<Escrow>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "funds": {
        "coins": {
            "uluna": 1000
        },
        
        "recipient": "terra1...",
        
        "condition": {
            "expiration": {
              "at_height": 1
              }
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                              | Description                                                  |
| ------- | --------------------------------- | ------------------------------------------------------------ |
| `funds` | Vec<[Escrow](timelock.md#escrow)> | Optional Escrow with the held funds and related information. |

### GetTimelockConfig

Queries the contract's configuration.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    GetTimelockConfig {
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "get_timelock_config": {
    }
}
```
{% endtab %}
{% endtabs %}

#### GetTimelockConfigResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct GetTimelockConfigResponse {
    pub address_list: Option<AddressListModule>,
    pub address_list_contract: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "address_list": {
        "address": "terra1..."
    },
    "address_list_contract": "terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name                    | Type                                                             | Description                                          |
| ----------------------- | ---------------------------------------------------------------- | ---------------------------------------------------- |
| `address_list`          | Option<[AddressListModule](../modules/address-list/#definition)> | The `AddressList` module definition.                 |
| `address_list_contract` | Option\<String>                                                  | The contract address for the `AddressList` contract. |

### Owner/Operators/IsOperator

Check [AndrQuery](../andrreceive-andrquery.md).