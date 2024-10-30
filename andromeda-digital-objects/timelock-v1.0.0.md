# Timelock V1.0.0

The **Timelock** ADO or Escrow ADO is a smart contract built to hold funds (Native coins) for a period of time until the set condition is satisfied.&#x20;

There are two main conditions that can be used by the contract:

* **Expiration**: A time expiration to when the funds can be released.
* **MinimumFunds**: A minimum amount of funds to be deposited before they can be released.

Once a condition is satisfied, the funds can be released by anyone.

**Ado\_type**: timelock

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub kernel_address:String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="252.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">kernel contract</a> to be used for <a href="../platform-and-framework/andromeda-messaging-protocol/">AMP</a> messaging. Kernel contract address can be found in our <a href="../platform-and-framework/deployed-contracts (1).md">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

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
             "address":"andr1..."
         },
        "condition": {
            "expiration":360000000
        }
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="155.33333333333331">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipient</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#recipient">Recipient</a>></td><td>Optional recipient address. If not set, defaults to the sender.</td></tr><tr><td><code>condition</code></td><td>Option&#x3C;<a href="timelock-v1.0.0.md#escrowcondition">EscrowCondition</a>></td><td>An optional condition to unlock the Escrow.</td></tr></tbody></table>

#### EscrowCondition

Enum used to specify the condition which must be met in order for the Escrow to unlock.

{% hint style="warning" %}
The Expiration timestamp is taken in nanosecond precision. Using another precision will give a "Time in the past" error.&#x20;
{% endhint %}

```rust
pub enum EscrowCondition {
    Expiration(MillisecondsExpiration),
    MinimumFunds(Vec<Coin>),
}
```

| EscrowCondition Type | Type                                                                             | Description                                                                 |
| -------------------- | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `Expiration`         | [MillisecondsExpiration](../platform-and-framework/common-types.md#milliseconds) | Requires a given time to be reached. The time is specified in milliseconds. |
| `MinimumFunds`       | Vec<[Coin](../platform-and-framework/common-types.md#coin)>                      | Requires a minimum amount of funds to be deposited.                         |

### ReleaseFunds

Releases any held funds by the specified recipient.

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
| `recipient_addr` | Option\<String> | Optional address to release the funds for. Will default to the sender if not specified.                           |
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

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

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
        
        "recipient": "andr1...",
        
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

| Name    | Type                                        | Description                                                  |
| ------- | ------------------------------------------- | ------------------------------------------------------------ |
| `funds` | Option<[Escrow](timelock-v1.0.0.md#escrow)> | Optional Escrow with the held funds and related information. |

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
   "recipient":"andr1...",
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
            "addr":"andr1..."
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

| Name    | Type                                     | Description                                                  |
| ------- | ---------------------------------------- | ------------------------------------------------------------ |
| `funds` | Vec<[Escrow](timelock-v1.0.0.md#escrow)> | Optional Escrow with the held funds and related information. |

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
| `condition` | Option<[EscrowCondition](timelock-v1.0.0.md#escrowcondition)>    | Optional condition for the Escrow.                        |
| `recipient` | [Recipient](../platform-and-framework/common-types.md#recipient) | The recipient of the funds once `condition` is satisfied. |

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
