# CW20 Staking

## Introduction

This contract allows users to stake a specified token and to receive rewards in any number of other tokens in proportion to their share. The reward token does not need to be the token they stake with but it can be. The contract allows for two types of rewards:

* non-allocated rewards: These are rewards that get deposited periodically into the contract and get distributed proportionally to stakers. Rewards that are deposited are instantly granted to the stakers.&#x20;
* Allocated rewards:  The owner deposits a number of tokens to be distributed over the course of a set time. The rewards get distributed the same way, except all of the reward tokens are already deposited in the contract.

The added rewards are distributed proportionally between the stakers that are already staked.

#### Example

We assume that we instantiated the contract and there are no stakers as of yet. Then the following series of events happen:

1. First user stakes 100 tokens
2. Second user stakes 500 tokens
3. Third user stakes 400 tokens
4. 100 tokens are sent to the contract as rewards&#x20;
5. Fourth user stakes 1000 tokens&#x20;
6. 1000 tokens are sent as rewards&#x20;

In this case, when the 100 coins are sent the pending rewards are as following for each user:

* 10 tokens for user 1
* 50 tokens for user 2
* 40 tokens for user 3&#x20;
* 0 tokens for user 4 since he staked after the rewards were sent

After the 1000 tokens are sent to the contract as rewards, it is split as follows:

* 50 tokens for user 1 (60 in total)
* 250 tokens for user 2 (300 in total)
* 200 tokens for user 3 (240 in total)
* 500 tokens for user 4 (500 in total since did not receive rewards from first batch)

After the rewards have been sent, the stakers would not receive any extra rewards until a next batch of rewards are sent.

**Ado\_type**: cw20-staking

## InstantiateMsg

{% hint style="warning" %}
The additional rewards should not include the staking token.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
  pub staking_token: AndrAddress,
  pub additional_rewards: Option<Vec<RewardTokenUnchecked>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"staking_token":{
          "identifier":"juno1..."
              },
"additional_rewards": [{
          "asset_info":{
              "cw-20":"juno1..."
              },
          "allocation_config":{
              "init_timestamp": 104329432,
              "till_timestamp": 104334432,
              "cycle_rewards":"300",
              "cycle duration":"400"
              }
          }]
     }
```
{% endtab %}
{% endtabs %}

| Name                 | Type                                                                 | Description                                                                                                                                                               |
| -------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `staking_token`      | [AndrAddress](../platform-and-framework/common-types.md#andraddress) | The cw20 token that can be staked                                                                                                                                         |
| `additional_rewards` | Option\<Vec\<RewardTokenUnchecked>>                                  | Any rewards in addition to the staking token. This list cannot include the staking token since it is used as a reward by default. Can have a maximum of 10 reward tokens. |

#### RewardTokenUnchecked

```rust
pub struct RewardTokenUnchecked {
    pub asset_info: AssetInfoUnchecked,
    pub allocation_config: Option<AllocationConfig>,
}
```

| Name                | Type                                                         | Description                                                                                                |
| ------------------- | ------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| `asset_info`        | AssetInfoUnchecked                                           | The asset used as a reward.                                                                                |
| `allocation_config` | Option<[AllocationConfig](cw20-staking.md#allocationconfig)> | How to allocate the `asset_info` as rewards. If not set, then the rewards are of the "non-allocated" type. |

#### AssetInfoUnchecked

```rust
pub type AssetInfoUnchecked = AssetInfoBase<String>
```

#### AssetInfoBase

```rust
pub enum AssetInfoBase<T> {
    Cw20(T),
    Native(String),
}
```

* **Cw20**: To create an asset info instance of this type, provide the contract address.
* **Native**: To create an asset info instance of this type, provide the denomination.

#### AllocationConfig

```rust
pub struct AllocationConfig {
    pub init_timestamp: u64,
    pub till_timestamp: u64,
    pub cycle_rewards: Uint128,
    pub cycle_duration: u64,
    pub reward_increase: Option<Decimal>,
}
```

| Name              | Type               | Description                                                                                         |
| ----------------- | ------------------ | --------------------------------------------------------------------------------------------------- |
| `init_timestamp`  | u64                | Timestamp from which Rewards will start getting accrued against the staked LP tokens.               |
| `till_timestamp`  | u64                | Timestamp till which Rewards will be accrued. No staking rewards are accrued beyond this timestamp. |
| `cycle_rewards`   | Uint128            | Rewards distributed during the 1st cycle.                                                           |
| `cycle_duration`  | u64                | Cycle duration in seconds.                                                                          |
| `reward_increase` | Optional\<Decimal> | Percent increase in Rewards per cycle.                                                              |

## ExecuteMsg

### Receive

Receives Cw-20 tokens which can be staked, or used to update the global index depending on the attached Cw20HookMsg

```rust
pub enum ExecuteMsg {
    Receive(Cw20ReceiveMsg),
 }
```

#### Cw20ReceiveMsg

```rust
pub struct Cw20ReceiveMsg {
    pub sender: String,
    pub amount: Uint128,
    pub msg: Binary,
}
```

The `msg` in the `Cw20ReceiveMsg`should be a `Cw20HookMsg`.

#### Cw20HookMsg

```rust
pub enum Cw20HookMsg {
    StakeTokens {},
    UpdateGlobalIndex {},
}
```

**StakeTokens**: Stake the sent tokens. Address must match the `staking_token` given on instantiation. The user's pending rewards and indexes are updated for each additional reward token.&#x20;

**UpdateGlobalIndex**: Updates the global reward index on deposit of a valid cw20 token.

### AddRewardToken

Add `reward_token` as another reward token. A maximum of 10 reward tokens can be added.&#x20;

{% hint style="warning" %}
Only available to the contract owner/operator.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
      AddRewardToken {
        reward_token: RewardInfoUnchecked,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_reward_token":{
        "reward_token":{
                "cw20":"juno1..."
                }
     }
} 
```
{% endtab %}
{% endtabs %}

| Name           | Type                                                        | Description                        |
| -------------- | ----------------------------------------------------------- | ---------------------------------- |
| `reward_token` | [RewardInfoUnchecked](cw20-staking.md#rewardtokenunchecked) | The token to be added as a reward. |

### UnstakeTokens

Unstakes the specified amount of assets, or all if not specified. The user's pending rewards and indexes are updated for each additional reward token.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     UnstakeTokens {
        amount: Option<Uint128>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unstake_tokens":{
    "amount":"500"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                                                 |
| -------- | ---------------- | ----------------------------------------------------------- |
| `amount` | Option\<Uint128> | Optional amount to unstake. Defaults to the maximum amount. |

### ClaimRewards

Claims any outstanding rewards from the additional reward tokens.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    ClaimRewards {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim_rewards":{}
}
```
{% endtab %}
{% endtabs %}

### UpdateGlobalIndexes

Updates the global reward index for the specified assets or all of the specified ones if none is specified. Funds may be sent along with this. Usually called when new allocated rewards are deposited to update the rewards accordingly.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     UpdateGlobalIndexes {
        asset_infos: Option<Vec<AssetInfoUnchecked>>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
"update_global_indexes":{}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                                                   | Description                                                            |
| ------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `asset_infos` | Option\<Vec<[AssetInfoUnchecked](cw20-staking.md#assetinfounchecked)>> | Optional vector to specify the assets to update the global index for.  |

### AndrReceive

The rest of the executes can be found in the [`AndrReceive`](../platform-and-framework/ado-base.md#andrrecieve) section.

## QueryMsg

### Config

Gets the config of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
       #[returns(Config)]
       Config {},
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

#### Config

Returns the config structure.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct Config {
    pub staking_token: AndrAddress,
    pub number_of_reward_tokens: u32,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"staking_token":{
        "identifier":"juno1..."
        },
"number_of_rewards_tokens": 7                   
}
```
{% endtab %}
{% endtabs %}

| Name                      | Type                                                                 | Description                                                           |
| ------------------------- | -------------------------------------------------------------------- | --------------------------------------------------------------------- |
| `staking_token`           | [AndrAddress](../platform-and-framework/common-types.md#andraddress) | The token accepted for staking.                                       |
| `number_of_reward_tokens` | u32                                                                  | The current number of reward tokens, cannot exceed the maximum of 10. |

### State

Gets the state of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(State)]
    State {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"state":{}
}
```
{% endtab %}
{% endtabs %}

#### State

Returns the State structure.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct State {
    pub total_share: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"total_share":"1000"
}
```
{% endtab %}
{% endtabs %}

| Name          | Type    | Description                                                  |
| ------------- | ------- | ------------------------------------------------------------ |
| `total_share` | Uint128 | The total share/amount of the staking token in the contract. |

### Staker

Returns a `StakerResponse` for the given staker. The pending rewards are updated to the present index.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
      #[returns(StakerResponse)]
      Staker {
        address: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"staker":{
    "address":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                        |
| --------- | ------ | ---------------------------------- |
| `address` | String | The address of the staker to check |

#### StakerResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct StakerResponse {
    pub address: String,
    pub share: Uint128,
    pub balance: Uint128,
    pub pending_rewards: Vec<(String, Uint128)>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "address": "juno1...",
    "share": "10000000",
    "pending_rewards": [
      [
        "native:uusd",
        "6666666"
      ]
    ]
}

```
{% endtab %}
{% endtabs %}

| Name              | Type               | Description                                                                                       |
| ----------------- | ------------------ | ------------------------------------------------------------------------------------------------- |
| `address`         | String             | The address of the staker.                                                                        |
| `share`           | Uint128            | The staker's share of the staked tokens.                                                          |
| `balance`         | Uint128            | How many staking tokens the user has which is the staked amount + rewarded tokens.                |
| `pending_rewards` | Vec<(String,Uint)> |  The staker's pending rewards represented as \[(token\_1, amount\_1), ..., (token\_n, amount\_n)] |

### Stakers

Returns a `Vec<StakerResponse>` for range of stakers. The pending rewards are updated to the  present index for each staker.

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum QueryMsg {
   #[returns(Vec&#x3C;StakerResponse>)]
   Stakers {
          start_after: Option&#x3C;String>,
          limit: Option&#x3C;u32>,
            },
<strong>        }
</strong></code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"stakers":{
    "limit":20
    }
}
```
{% endtab %}
{% endtabs %}

|               |                 |                                                                                                        |
| ------------- | --------------- | ------------------------------------------------------------------------------------------------------ |
| `start_after` | Option\<String> | An optional ID to start after. Used for pagination.                                                    |
| `limit`       | Optional\<u32>  | An optional limit to the number of stakers to query. Defaults to 10 and can be set to a maximum of 30. |

Returns a vector of [StakerResponse](cw20-staking.md#stakerresponse).

### Timestamp

Queries the current timestamp in seconds.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
       #[returns(u64)]
       Timestamp {},
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"timestamp":{}
}
```
{% endtab %}
{% endtabs %}

Returns a u64 with the current timestamp in seconds.

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado-base.md#andrquery).
