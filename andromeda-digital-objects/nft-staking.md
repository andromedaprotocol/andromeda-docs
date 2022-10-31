# NFT Staking

## Introduction

The **CW721 Staking** ADO is a smart contract that allows users stake, unstake, and claim their NFT alongside an accrued reward after surpassing the unbonding period.

Note:

* The unbonding period is set in seconds
* The rewards are distributed per second. This means if the reward is 50 ujunox, then after 10 seconds, my rewards should be 500 ujunox.
* When the NFT is staked, it is assigned a unique key, which consists of concatenating the cw721 contract address + token Id.&#x20;

**Ado\_type:** nft-staking

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub nft_contract: Vec<String>,
    pub unbonding_period: u64,
    pub reward: Coin,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"nft_contract":["juno1...","juno1...",...],
"unbonding_period": 100,
"reward":{
    "denom": "ujunox",
    "amount": "50"
    }
}
```
{% endtab %}
{% endtabs %}

| Name               | Type                                                   | Description                                                                                |
| ------------------ | ------------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| nft\_contract      | Vec\<String>                                           | The contract addresses of the NFT contracts that can stake their NFTs using this contract. |
| `unbonding_period` | u64                                                    | The unbonding period (time to unstake) of the NFTs in seconds.                             |
| `reward`           | [Coin](../platform-and-framework/common-types.md#coin) | The rewards to be distributed per second.                                                  |

## ExecuteMsg

### ReceiveNft

Receives an NFT from one of the `nft_contract` set on instantiation by executing a [`SendNft`](cw721.md#sendnft) from the NFT contract with the attached `Stake` message

```rust
pub enum ExecuteMsg {
     ReceiveNft(Cw721ReceiveMsg),
}
```

#### Cw721ReceiveMsg

```rust
pub struct Cw721ReceiveMsg {
    pub sender: String,
    pub token_id: String,
    pub msg: Binary,
}
```

Here the message should be a `Cw721HookMsg` of type `Stake` which tells the contract to stake the NFT.

```rust
pub enum Cw721HookMsg {
    Stake {},
}
```

### Unstake

Unstakes the NFT with the specified key.

{% hint style="warning" %}
Only available to the token owner.

The minimum time to call unstake after staking is one day.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
Unstake {
        key: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unstake":{
    "key":"juno1...4"
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type   | Description                                                                                      |
| ----- | ------ | ------------------------------------------------------------------------------------------------ |
| `key` | String | The key of the NFT to unstake. Consists of concatenating the cw721 contract address + token Id.  |

### UpdateAllowedContracts

Updates the list of allowed NFT contracts to stake using this ADO.

{% hint style="warning" %}
Only available to the contract owner/operator.

The old list is overwritten by the new one.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  UpdateAllowedContracts {
        contracts: Vec<String>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_allowed_contracts": {
    "contracts":["juno1...","juno1...",...]
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type         | Description                         |
| ----------- | ------------ | ----------------------------------- |
| `contracts` | Vec\<String> | The new list of contract addresses. |

### AddAllowedContract

Add a new NFT contract to the list.

{% hint style="warning" %}
Only available to the contract owner/operator.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    AddAllowedContract {
        new_contract: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_allowed_contract":{
    "new_contract": "juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name           | Type   | Description                                       |
| -------------- | ------ | ------------------------------------------------- |
| `new_contract` | String | The contract address of the  NFT contract to add. |

### RemoveAllowedContract

Remove a new NFT contract to the list.

{% hint style="warning" %}
Only available to the contract owner/operator.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   RemoveAllowedContract {
        old_contract: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"remove_allowed_contract":{
    "old_contract": "juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name           | Type   | Description                                          |
| -------------- | ------ | ---------------------------------------------------- |
| `old_contract` | String | The contract address of the  NFT contract to remove. |

### UpdateUnbondingPeriod

Changes the unbonding period to a new one.

{% hint style="warning" %}
Only available to the contract owner/operator.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateUnbondingPeriod {
        new_period: u64,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_unbonding_period":{
    "new_period": 1000
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type | Description                          |
| ------------ | ---- | ------------------------------------ |
| `new_period` | u64  | The new unbonding period in seconds. |

### Claim

Sends back the NFT to its original owner alongside the accrued rewards.

{% hint style="warning" %}
Can be executed after the unbonding period has passed.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Claim {
        key: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim":{
    "key":"juno1...2"
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type   | Description                                                                                    |
| ----- | ------ | ---------------------------------------------------------------------------------------------- |
| `key` | String | The key of the NFT to claim. Consists of concatenating the cw721 contract address + token Id.  |

### AndrReceive

The rest of the executes can be found in the [`AndrReceive`](../platform-and-framework/ado\_base.md#andrrecieve) section.

## QueryMsg

### StakedNft

Queries information on a staked NFT.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    StakedNft {
         key: String 
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"staked_nft":{
    "key":"juno1...3"
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type   | Description                                                                                       |
| ----- | ------ | ------------------------------------------------------------------------------------------------- |
| `key` | String | The key of the NFT get info on. Consists of concatenating the cw721 contract address + token Id.  |

#### StakedNft

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct StakedNft {
    pub owner: String,
    pub id: String,
    pub contract_address: String,
    pub time_of_staking: Timestamp,
    pub time_of_unbonding: Option<Timestamp>,
    pub reward: Coin,
    pub accrued_reward: Option<Coin>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"owner":"juno1...",
"id":"3",
"contract_address":"juno1...",
"time_of_staking":"123437462",
"rewards":{
    "denom":"ujunox",
    "amount":"50"
    }
}
```
{% endtab %}
{% endtabs %}

| Name                | Type                                                           | Description                                               |
| ------------------- | -------------------------------------------------------------- | --------------------------------------------------------- |
| `owner`             | String                                                         | The owner of the token.                                   |
| `id`                | String                                                         | The token id.                                             |
| `contract_address`  | String                                                         | The NFT contract address.                                 |
| `time_of_staking`   | Timestamp                                                      | The time of staking. Epoch time in seconds.               |
| `time_of_unbonding` | Option\<Timestamp>                                             | Optional time of unbonding if the NFT has been unstaked.  |
| `rewards`           | [Coin](../platform-and-framework/common-types.md#coin)         | The rewards being distributed per second.                 |
| `accrued_reward`    | Option<[Coin](../platform-and-framework/common-types.md#coin)> | The amount of rewards accumulated from staking.           |

### AllowedContracts

Queries the list of allowed NFT contracts to stake using this contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    AllowedContracts {}
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"allowed_contracts":{}
}
```
{% endtab %}
{% endtabs %}

Returns a Vec\<String> containing the addresses of the NFT contracts allowed to send NFTs to stake.

### UnbondingPeriod

Queries the unbonding period.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
        UnbondingPeriod {}
        }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unbonding_period":{}
}
```
{% endtab %}
{% endtabs %}

Returns a u64 value which represents the unbonding time in seconds.

### Reward

Queries the reward being distributed for staking.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
     Reward {}
     }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"reward":{}
}
```
{% endtab %}
{% endtabs %}

Returns a type [Coin](../platform-and-framework/common-types.md#coin) with the amount and denom of the reward being distributed per second.

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado\_base.md#andrquery).
