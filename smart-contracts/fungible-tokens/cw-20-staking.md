# Cw-20 Staking

## Introduction

This contract allows users to stake a specified token and to receive rewards in any number of other tokens in proportion to their share. The reward token does not need to be the token they stake with but it can be.

**Ado\_type**: cw20\_staking

## InstantiateMsg

{% hint style="info" %}
The additional rewards should not include the staking token.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub staking_token: AndrAddress,
    pub additional_rewards: Option<Vec<AssetInfoUnchecked>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"staking_token":{
          "identifier":"juno1..."
              },
"additional_rewards":[
                     {"native":"uusd"}
                     ]
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                                                    | Description                                                                               |
| -------------------- | ------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `staking_token`      | [AndrAddress](../common-types/recipient.md#andraddress) | The cw20 token that can be staked                                                         |
| `additional_rewards` | Option\<Vec\<AssetInfoUnchecked>>                       | Any rewards in addition to the staking token. This list cannot include the staking token. |

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

## ExecuteMsg

### Receive

Receives Cw20 tokens which can be staked, or used to update the global index depending on the attached Cw20HookMsg

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

Add `asset_info` as another reward token.&#x20;

{% hint style="info" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
      AddRewardToken {
        asset_info: AssetInfoUnchecked,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_reward_token":{
     "native":"uusd"
     }
} 
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                      | Description                        |
| ------------ | --------------------------------------------------------- | ---------------------------------- |
| `asset_info` | [AssetInfoUnchecked](cw-20-staking.md#assetinfounchecked) | The token to be added as a reward. |

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

Updates the global reward index for the specified assets or all of the specified ones if none is specified. Funds may be sent along with this.

The Global index is updated according to the following formula:

```
global_index = global_index + deposited_amount / total_share
```

Here `deposited_amount` is the amount of reward tokens deposited since the last update of the index.

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

| Name          | Type                              | Description                                                            |
| ------------- | --------------------------------- | ---------------------------------------------------------------------- |
| `asset_infos` | Option\<Vec\<AssetInfoUnchecked>> | Optional vector to specify the assets to update the global index for.  |

### AndrReceive

Check [AndrReceive](../ado\_base/andrreceive-andrquery.md#andrrecieve).

## QueryMsg

### Config

Gets the config of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
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
    pub additional_reward_tokens: Vec<AssetInfo>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"staking_token":{
        "identifier":"juno1..."
        },
"additional_rewards_tokens":[
                     {"native":"uusd"}
                     ]
}
```
{% endtab %}
{% endtabs %}

| Name                       | Type                                                    | Description                                                               |
| -------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------- |
| `staking_token`            | [AndrAddress](../common-types/recipient.md#andraddress) | The token accepted for staking.                                           |
| `additional_reward_tokens` | Vec\<AssetInfo>                                         | Any additional tokens used for rewards. Cannot include the staking token. |

### State

Gets the state of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
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

| Name          | Type    | Description                                           |
| ------------- | ------- | ----------------------------------------------------- |
| `total_share` | Uint128 | The total share of the staking token in the contract. |

### Staker

Returns a `StakerResponse` for the given staker. The pending rewards are updated to the present index.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
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

| Name              | Type               | Description                                       |
| ----------------- | ------------------ | ------------------------------------------------- |
| `address`         | String             | The address of the staker.                        |
| `share`           | Uint128            | The share of the staker.                          |
| `pending_rewards` | Vec<(String,Uint)> | The rewards of the stakers for each reward token. |

### Stakers

Returns a `Vec<StakerResponse>` for range of stakers. The pending rewards are updated to the  present index for each staker.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
        Stakers {
        start_after: Option<String>,
        limit: Option<u32>,
    },
}
```
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

Returns a vector of [StakerResponse](cw-20-staking.md#stakerresponse).

### AndrQuery

Check [AndrQuery](../ado\_base/andrreceive-andrquery.md#andrquery).
