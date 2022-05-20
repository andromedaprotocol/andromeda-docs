# Vesting ADO

The **Vesting** ADO allows the vesting of tokens for one recipient which is fixed upon instantiation. The tokens can be set to release in one batch (All at once) or multiple batches which is specified by `is_multi_batch_enabled` upon instantiation.

A new batch can be created using the `CreateBatch` message which will create a batch with funds that are sent along with the message. This message contains parameters that define the lockup period and vesting parameters. All time-related parameters are done using seconds.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub recipient: Recipient,
    pub is_multi_batch_enabled: bool,
    pub denom: String,
    pub unbonding_duration: Duration,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"recipient":{
    "addr":"terra1..."
    },
"is_multi_batch_enabled": true,
"denom":"uusd",
"unbonding_duration":{
    "time":23248854
    }
}
```
{% endtab %}
{% endtabs %}

| Name                     | Type                                      | Description                                          |
| ------------------------ | ----------------------------------------- | ---------------------------------------------------- |
| `recipient`              | [Recipient](../common-types/recipient.md) | The recipient of all funds locked in this contract.  |
| `is_multi_batch_enabled` | bool                                      | Whether or not multi-batching has been enabled.      |
| `denom`                  | String                                    | The denom of the coin being vested.                  |
| `unbonding_duration`     | Duration                                  | The unbonding duration of the native staking module. |

#### Duration

Can be either a block height or block time in seconds.

```rust
pub enum Duration {
    Height(u64),
    Time(u64),
}
```

## ExecuteMsg

### CreateBatch

Creates a new batch with the funds sent along with the message.

{% hint style="warning" %}
Only available to the contract owner or an operator of the contract.

The attached funds should be a single native fund.

Denom should be the same as specified upon instantiation.

Each batch has an ID which starts at 1 and increments by 1 for each new batch.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   CreateBatch {
        lockup_duration: Option<u64>,
        release_unit: u64,
        release_amount: WithdrawalType,
        validator_to_delegate_to: Option<String>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"create_batch":{
    "lockup_duration": 500,
    "release_unit":3000,
    "release_amount":{
        "percentage":"0.2"
        },
    "validator_to_delegate_to": "validator"
    }
}
```
{% endtab %}
{% endtabs %}

| Name                       | Type                                                                   | Description                                                                                                                               |
| -------------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `lockup_duration`          | Option\<u64>                                                           | Specifying None would mean no lock up period and funds start vesting right away.                                                          |
| `release_unit`             | u64                                                                    | How often releases occur in seconds.                                                                                                      |
| `release_amount`           | [WithdrawalType](../ado\_base/andrreceive-andrquery.md#withdrawaltype) | Specifies how much is to be released after each `release_unit`. If it is a percentage, it would be the percentage of the original amount. |
| `validator_to_delegate_to` | Option\<String>                                                        | Optional validator to delegate to. If specified, funds will be automatically delegated to them.                                           |

### Claim

Claim the number of batches specified starting from the beginning. If not specified then the max amount will be claimed.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Claim {
        number_of_claims: Option<u64>,
        batch_id: u64,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim":{
    "number_of_claims": 3,
    "batch_id":7
    }
}
```
{% endtab %}
{% endtabs %}

| Name               | Type         | Description                                                                      |
| ------------------ | ------------ | -------------------------------------------------------------------------------- |
| `number_of_claims` | Option\<u64> | The number of batches to claim. Defaults to the maximum amount if not specified. |
| `batch_id`         | u64          | The Id of the batch.                                                             |

### ClaimAll

Claims tokens from all batches using a paginated approach. If `up_to_time` is specified then it will only claim up to a specific time, otherwise it will claim to the most recent release.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     ClaimAll {
        up_to_time: Option<u64>,
        start_after: Option<u64>,
        limit: Option<u32>,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim_all":{
    "up_to_time": 500,
    "limit": 8
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type         | Description                                                                                     |
| ------------- | ------------ | ----------------------------------------------------------------------------------------------- |
| `up_to_time`  | Option\<u64> | Optional time to claim up to. The minimum of the current time and the specified time is taken.  |
| `start_after` | Option\<u64> | Optional id to start after. Used for pagination.                                                |
| `limit`       | Option\<u32> | The number of batches to claim from. Defaults to 10 and can be set to a maximum of 30.          |

### Delegate

Delegates the given amount of tokens, or all if not specified.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum ExecuteMsg {
  Delegate {
        amount: Option<Uint128>,
        validator: String,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"delegate":{
    "amount":"2000",
    "validator":"validator"
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type             | Description                                                             |
| ----------- | ---------------- | ----------------------------------------------------------------------- |
| `amount`    | Option\<Uint128> | Optional amount to delegate. Takes the maximum amount if not specified. |
| `validator` | String           | The validator to delegate to.                                           |

### Undelegate

Undelegates the given amount of tokens, or all if not specified.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   Undelegate {
        amount: Option<Uint128>,
        validator: String,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"undelegate":{
    "amount":"2000",
    "validator":"validator"
    }
}
```
{% endtab %}
{% endtabs %}



| Name        | Type             | Description                                                               |
| ----------- | ---------------- | ------------------------------------------------------------------------- |
| `amount`    | Option\<Uint128> | Optional amount to undelegate. Takes the maximum amount if not specified. |
| `validator` | String           | The validator to delegate to.                                             |

### AndrReceive

Check[ AndrReceive](../ado\_base/andrreceive-andrquery.md#andrrecieve).

## QueryMsg

### Config

Queries the configuration of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
     Config {}
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"config":{}
}
```
{% endtab %}
{% endtabs %}

#### ConfigResponse

Returns a config struct.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct Config {
    pub recipient: Recipient,
    pub is_multi_batch_enabled: bool,
    pub denom: String,
    pub unbonding_duration: Duration,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"recipient":{
    "addr":"terra1..."
    },
"is_multi_batch_enabled": true,
"denom":"uusd",
"unbonding_duration":{
    "time":3248325932
    }
}
```
{% endtab %}
{% endtabs %}

| Name                     | Type                                 | Description                                          |
| ------------------------ | ------------------------------------ | ---------------------------------------------------- |
| `recipient`              | Recipient                            | The recipient of each batch.                         |
| `is_multi_batch_enabled` | bool                                 | Whether or not multiple batches are supported.       |
| `denom`                  | String                               | The denom of the coin being vested.                  |
| `unbonding_duration`     | [Duration](vesting-ado.md#undefined) | The unbonding duration of the native staking module. |

### Batch

Queries batch information from the specified id.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
       Batch {
        id: u64,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"batch":{
    "id": 3
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description                   |
| ---- | ---- | ----------------------------- |
| `id` | u64  | The id of the batch to query. |

#### BatchResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct BatchResponse {
    pub id: u64,
    pub amount: Uint128,
    pub amount_claimed: Uint128,
    pub amount_available_to_claim: Uint128,
    pub number_of_available_claims: Uint128,
    pub lockup_end: u64,
    pub release_unit: u64,
    pub release_amount: WithdrawalType,
    pub last_claimed_release_time: u64,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"id": 3,
"amount":"200",
"amount_claimed":"400",
"amount_available_to_claim":"2000",
"number_of_available_claims":"5",
"lockup_end":100,
"release_unit":3000,
"release_amount":{
    "percentage":"0.2",
    },
"last_claimed_release_time":"200"
}   
```
{% endtab %}
{% endtabs %}

| Name                         | Type                                                                   | Description                                                                                                                               |
| ---------------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                         | u64                                                                    | The id of the batch.                                                                                                                      |
| `amount`                     | Uint128                                                                | The amount of tokens in the batch.                                                                                                        |
| `amount_claimed`             | Uint128                                                                | The amount of tokens that have been already claimed.                                                                                      |
| `amount_available_to_claim`  | Uint128                                                                | The amount of tokens available to claim right now.                                                                                        |
| `number_of_available_claims` | Uint128                                                                | The number of available claims now.                                                                                                       |
| `lockup_end`                 | u64                                                                    | When the lockup ends.                                                                                                                     |
| `release_unit`               | u64                                                                    | How often releases occur in seconds.                                                                                                      |
| `release_amount`             | [WithdrawalType](../ado\_base/andrreceive-andrquery.md#withdrawaltype) | Specifies how much is to be released after each `release_unit`. If it is a percentage, it would be the percentage of the original amount. |
| `last_claimed_release_time`  | u64                                                                    | The time at which the last claim took place.                                                                                              |

### Batches

Queries the batches with pagination.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
      Batches {
        start_after: Option<u64>,
        limit: Option<u32>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"batches":{
    "limit": 15
    }
} 
```
{% endtab %}
{% endtabs %}

| Name          | Type         | Description                                                                           |
| ------------- | ------------ | ------------------------------------------------------------------------------------- |
| `start_after` | Option\<u64> | Optional Id used for pagination.                                                      |
| `limit`       | Option\<u32> | The number of batches to claim from. Defaults to 10 and can be set to a maximum of 30 |

Returns a Vec<[BatchResponse](vesting-ado.md#batchresponse)>.

### AndrQuery

Check [AndrQuery](../ado\_base/andrreceive-andrquery.md#andrquery).
