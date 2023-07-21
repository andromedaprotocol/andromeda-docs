# Vesting

The **Vesting** ADO allows the vesting of tokens for one recipient which is fixed upon instantiation. The tokens can be set to release in one batch (All at once) or multiple batches which is specified by `is_multi_batch_enabled` upon instantiation.

A new batch can be created using the `CreateBatch` message which will create a batch with the funds that are sent along with the message. This message contains parameters that define the lockup period and vesting parameters. All time-related parameters are done using seconds.

#### What is super vesting?

Super vesting is when you vest all your tokens in one batch.&#x20;

**Ado\_type**: vesting

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
    "addr":"andr1..."
    },
"is_multi_batch_enabled": true,
"denom":"uandr",
"unbonding_duration":{
    "time":23248854
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="287.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipient</code></td><td><a href="../platform-and-framework/common-types.md#recipient">Recipient</a></td><td>The recipient of all funds locked in this contract.</td></tr><tr><td><code>is_multi_batch_enabled</code></td><td>bool</td><td>Whether or not multi-batching has been enabled.</td></tr><tr><td><code>denom</code></td><td>String</td><td>The denom of the coin being vested.</td></tr><tr><td><code>unbonding_duration</code></td><td>Duration</td><td>The unbonding duration of the native staking module.</td></tr></tbody></table>

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
    "validator_to_delegate_to": "andrvaloper1f..."
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="304.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>lockup_duration</code></td><td>Option&#x3C;u64></td><td>A lockup period before vesting starts. Specifying None would mean no lock up period and funds start vesting right away.</td></tr><tr><td><code>release_unit</code></td><td>u64</td><td>How often releases occur in seconds.</td></tr><tr><td><code>release_amount</code></td><td><a href="../platform-and-framework/ado-base.md#withdrawaltype">WithdrawalType</a></td><td>Specifies how much is to be released after each <code>release_unit</code>. If it is a percentage, it would be the percentage of the original amount.</td></tr><tr><td><code>validator_to_delegate_to</code></td><td>Option&#x3C;String></td><td>Optional validator to delegate to. If specified, funds will be automatically delegated to them.</td></tr></tbody></table>

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
    "validator":"andrvaloper1f9vl..."
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
    "validator":"andrvaloper1f..."
    }
}
```
{% endtab %}
{% endtabs %}



| Name        | Type             | Description                                                                                                                                                                        |
| ----------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `amount`    | Option\<Uint128> | Optional amount to undelegate. Takes the maximum amount if not specified. Also takes the max amount if the amount specified is greater than the actual amount of tokens delegated. |
| `validator` | String           | The validator to delegate to.                                                                                                                                                      |

### Redelegate

Redelegates the given amount of tokens, or all from the `from` validator to the `to` validator.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     Redelegate {
        amount: Option<Uint128>,
        from: String,
        to: String,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"redelegate":{
    "amount": "4000",
    "from":"andrvaloper1f...",
    "to":"andrvaloper1zk..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                                                                                                                                                               |
| -------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `amount` | Option\<Uint128> | The amount to redelegate. Takes the max amount if not specified. Also takes the max amount if the amount specified is greater than the actual amount of tokens delegated. |
| `from`   | String           | The validator to take the tokens from.                                                                                                                                    |
| `to`     | String           | The new validator to delegate tokens to.                                                                                                                                  |

### Vote

Votes on the specified proposal with the specified vote.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Vote {
        proposal_id: u64,
        vote: VoteOption,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"vote":{
    "proposal_id": 4,
    "vote": "no"
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type       | Description             |
| ------------- | ---------- | ----------------------- |
| `proposal_id` | u64        | The id of the proposal. |
| `vote`        | VoteOption | The vote.               |

#### VoteOption

Enum with the types of votes.

```rust
pub enum VoteOption {
    Yes,
    No,
    Abstain,
    NoWithVeto,
}
```

### WithdrawRewards

Withdraws rewards from all delegations to the sender.

{% hint style="warning" %}
Only available to the contract owner.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    WithdrawRewards {}
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw_rewards":{}
}
```
{% endtab %}
{% endtabs %}

### AndrReceive

The rest of the executes can be found in the [`AndrReceive`](../platform-and-framework/ado-base.md#andrrecieve) section.

## QueryMsg

### Config

Queries the configuration of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
     #[returns(Config)]
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
    "addr":"andr1..."
    },
"is_multi_batch_enabled": true,
"denom":"uandr",
"unbonding_duration":{
    "time":3248325932
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="294.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipient</code></td><td>Recipient</td><td>The recipient of each batch.</td></tr><tr><td><code>is_multi_batch_enabled</code></td><td>bool</td><td>Whether or not multiple batches are supported.</td></tr><tr><td><code>denom</code></td><td>String</td><td>The denom of the coin being vested.</td></tr><tr><td><code>unbonding_duration</code></td><td><a href="vesting.md#duration">Duration</a></td><td>The unbonding duration of the native staking module.</td></tr></tbody></table>

### Batch

Queries batch information from the specified id.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
       #[returns(BatchResponse)]
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

<table><thead><tr><th width="316.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>id</code></td><td>u64</td><td>The id of the batch.</td></tr><tr><td><code>amount</code></td><td>Uint128</td><td>The amount of tokens in the batch.</td></tr><tr><td><code>amount_claimed</code></td><td>Uint128</td><td>The amount of tokens that have been already claimed.</td></tr><tr><td><code>amount_available_to_claim</code></td><td>Uint128</td><td>The amount of tokens available to claim right now.</td></tr><tr><td><code>number_of_available_claims</code></td><td>Uint128</td><td>The number of available claims now.</td></tr><tr><td><code>lockup_end</code></td><td>u64</td><td>When the lockup ends.</td></tr><tr><td><code>release_unit</code></td><td>u64</td><td>How often releases occur in seconds.</td></tr><tr><td><code>release_amount</code></td><td><a href="../platform-and-framework/ado-base.md#withdrawaltype">WithdrawalType</a></td><td>Specifies how much is to be released after each <code>release_unit</code>. If it is a percentage, it would be the percentage of the original amount.</td></tr><tr><td><code>last_claimed_release_time</code></td><td>u64</td><td>The time at which the last claim took place.</td></tr></tbody></table>

### Batches

Queries the batches with pagination.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
      #[returns(Vec<BatchResponse>)]
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

Returns a Vec<[BatchResponse](vesting.md#batchresponse)>.

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado-base.md#andrquery).
