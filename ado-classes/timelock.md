---
description: An ADO contract to hold funds for a set period of time.
---

# Timelock

The **Timelock** ADO or Escrow ADO is a smart contract built to hold funds for a period of time until the set condition is satisfied.&#x20;

There are two main conditions that can be used by the contract:

* **Expiration**: A time expiration to when the funds can be released.
* **MinimumFunds**: A minimum amount of funds to be deposited before they can be released.

Once a condition is satisfied, the funds can be released.

The contract supports [modules](broken-reference) to extend its functionality.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub modules: Option<Vec<Module>>,
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                                                     | Description                                                                                                             |
| --------- | -------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `modules` | Option\<Vec<[Module](../modules/module-definitions.md)>> | A vector of Andromeda Module definitions. The module definitions can be found[ here](../modules/module-definitions.md). |

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
        "recipient":{
         "addr":"terra1..."
         }
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
| `recipient` | Option<[Recipient](../common-types/recipient.md)>      | Optional recipient address. If not set defaults to the sender. |
| `condition` | Option<[EscrowCondition](timelock.md#escrowcondition)> | An optional condition to unlock the Escrow                     |

#### EscrowCondition

Enum used to specify the condition which must be met in order for the Escrow to unlock.

```rust
pub enum EscrowCondition {
    Expiration(Expiration),
    MinimumFunds(Vec<Coin>),
}
```

| EscrowCondition Type | Type                                        | Description                                         |
| -------------------- | ------------------------------------------- | --------------------------------------------------- |
| `Expiration`         | [Expiration](../common-types/expiration.md) | Requires a given time or block height to be reached |
| `MinimumFunds`       | Vec<[Coin](../common-types/coin.md)>        | Requires a minimum amount of funds to be deposited  |

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
    limit:Option<u32>
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
| `limit`          | Option\<u32>    | Optional limit to the number timelocks to attempt to unlock. Defaults to 10 and can be set to a maximum of 30. |

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

### AndrReceive

Check [AndrReceive](../ado\_base/andrreceive-andrquery.md).

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
        "owner": "terra1...",
        "recipient":"terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                                |
| ----------- | ------ | ------------------------------------------ |
| `owner`     | String | The address of the owner of the funds      |
| `recipient` | String | The address of the recipient of the funds. |

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
| `limit`       | Option\<u32>    | Optional limit to the number timelocks to attempt to query. Defaults to 10 and can be set to a maximum of 30. |

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
        
        "recipient":{
        "addr":"terra1..."
        }
        
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

#### Escrow

The time-lock contract uses a basic struct to store a record of funds being held.

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
| `coins`     | Vec<[Coin](../common-types/coin.md)>                   | Funds being held within the Escrow.                       |
| `condition` | Option<[EscrowCondition](timelock.md#escrowcondition)> | Optional condition for the Escrow.                        |
| `recipient` | [Recipient](../common-types/recipient.md)              | The recipient of the funds once `condition` is satisfied. |

### AndrQuery

Check[ AndrQuery](../ado\_base/andrreceive-andrquery.md#andrquery).
