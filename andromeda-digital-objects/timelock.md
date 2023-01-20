# Timelock

The **Timelock** ADO or Escrow ADO is a smart contract built to hold funds for a period of time until the set condition is satisfied.&#x20;

There are two main conditions that can be used by the contract:

* **Expiration**: A time expiration to when the funds can be released.
* **MinimumFunds**: A minimum amount of funds to be deposited before they can be released.

Once a condition is satisfied, the funds can be released by anyone.

The contract supports [modules](broken-reference) to extend it's functionality.

**Ado\_type**: timelock

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub modules: Option<Vec<Module>>,
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                                                     | Description                                                                                                                           |
| --------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `modules` | Option\<Vec<[Module](../modules/module-definitions.md)>> | An optional vector of Andromeda[ Modules](broken-reference) that can be attached to the contract. "address-list" module can be added. |

## ExecuteMsg

### HoldFunds

Holds sent funds in escrow.

{% tabs %}
{% tab title="Rust" %}
```rust
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
         "addr":"andr1..."
         }
        "condition": {
            "expiration": {
              "at_height": 14738473
              }
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                                                                     | Description                                                     |
| ----------- | ------------------------------------------------------------------------ | --------------------------------------------------------------- |
| `recipient` | Option<[Recipient](../platform-and-framework/common-types.md#recipient)> | Optional recipient address. If not set, defaults to the sender. |
| `condition` | Option<[EscrowCondition](timelock.md#escrowcondition)>                   | An optional condition to unlock the Escrow.                     |

#### EscrowCondition

Enum used to specify the condition which must be met in order for the Escrow to unlock.

```rust
pub enum EscrowCondition {
    Expiration(Expiration),
    MinimumFunds(Vec<Coin>),
}
```

| EscrowCondition Type | Type                                                        | Description                                          |
| -------------------- | ----------------------------------------------------------- | ---------------------------------------------------- |
| `Expiration`         | [Expiration](broken-reference)                              | Requires a given time or block height to be reached. |
| `MinimumFunds`       | Vec<[Coin](../platform-and-framework/common-types.md#coin)> | Requires a minimum amount of funds to be deposited.  |

### ReleaseFunds

Releases any held funds of the specified recipient.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    ReleaseFunds {
    recipient_addr:Option<String>,
    start_after:Option<String>,
    limit:Option<u32>
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "release_funds": {
    
       "recipient_addr":"andr1...",
       "limit":"15"
    }
}
```
{% endtab %}
{% endtabs %}

| Name             | Type            | Description                                                                                                       |
| ---------------- | --------------- | ----------------------------------------------------------------------------------------------------------------- |
| `recipient_addr` | Option\<String> | Optional address release the funds for. Will default to the sender if not specified.                              |
| `start_after`    | Option\<String> | An optional address for which to start after, used for pagination.                                                |
| `limit`          | Option\<u32>    | Optional limit to the number of timelocks to attempt to unlock. Defaults to 10 and can be set to a maximum of 30. |

### ReleaseSpecificFunds

Release funds held by the `owner` to the `recipient`. (The recipient has to be the same as the one defined when the owner executed `HoldFunds`)

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
   "owner":"andr1...",
   "recipient_addr": "andr1..."
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

{% hint style="info" %}
Uses the modules feature.
{% endhint %}

The rest of the executes can be found in the [`AndrReceive`](../platform-and-framework/ado-base.md#andrrecieve) section.

## QueryMsg

### GetLockedFunds

Query any held funds for an address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(GetLockedFundsResponse)]
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
        "owner": "andr1...",
        "recipient":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                                |
| ----------- | ------ | ------------------------------------------ |
| `owner`     | String | The address of the owner of the funds.     |
| `recipient` | String | The address of the recipient of the funds. |

#### GetLockedFundsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct GetLockedFundsResponse {
    pub funds: Option<Escrow>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "funds": {
          "coins":[{
            "amount": "1000",
            "denom": "uandr"
        },
        ...
        ],
        
        "recipient": "juno1...",
        
        "condition": {
            "expiration": {
              "at_height": 175849
              }
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                 | Description                                                  |
| ------- | ------------------------------------ | ------------------------------------------------------------ |
| `funds` | Option<[Escrow](timelock.md#escrow)> | Optional Escrow with the held funds and related information. |

### GetLockedFundsForRecipient

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
   #[returns(GetLockedFundsForRecipientResponse)]
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
   "recipient":"juno1...",
   "limit":"15"
   }
 }
       
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                                   |
| ------------- | --------------- | ------------------------------------------------------------------------------------------------------------- |
| `recipient`   | String          | The address of the recipient..                                                                                |
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
        "coins":[{
            "amount": "1000",
            "denom": uandr
        },
        ...
        ],
        
        "recipient":{
            "addr":"juno1..."
        },
        
        "condition": {
            "expiration": {
              "at_height": 13249238492
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
pub struct Escrow {
    pub coins: Vec<Coin>,
    pub condition: Option<EscrowCondition>,
    pub recipient: Recipient,
}
```

| Name        | Type                                                             | Description                                               |
| ----------- | ---------------------------------------------------------------- | --------------------------------------------------------- |
| `coins`     | Vec<[Coin](../platform-and-framework/common-types.md#coin)>      | Funds being held within the Escrow.                       |
| `condition` | Option<[EscrowCondition](timelock.md#escrowcondition)>           | Optional condition for the Escrow.                        |
| `recipient` | [Recipient](../platform-and-framework/common-types.md#recipient) | The recipient of the funds once `condition` is satisfied. |

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado-base.md#andrquery).
