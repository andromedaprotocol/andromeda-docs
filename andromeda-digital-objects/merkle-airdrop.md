# Merkle-Airdrop

## Introduction

The Merkle-Airdrop ADO is a smart contract that allows projects to launch airdrops using the Merkle-tree (hashing). If you do not know what is a Merkle-airdrop and how it is different from a normal airdrop, please refer to the following [article](https://medium.com/mochilab/merkle-airdrop-one-of-the-best-airdrop-solution-for-token-issues-e2279df1c5c1).

**Ado\_type**: merkle-airdrop

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub asset_info: AssetInfoUnchecked,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"asset_info":{
"asset_info_unchecked":{
    "cw20":"juno1..."
     }
   }
} 
```
{% endtab %}
{% endtabs %}

#### AssetInfoUnchecked

Represents an **asset info** instance that may contain unverified data; to be used in messages.

```rust
pub type AssetInfoUnchecked = AssetInfoBase<String>;
```

#### AssetInfoBase

Represents the type of an fungible asset. Each asset info instance can be one of two variants:&#x20;

* **CW20 tokens**: To create an **** asset info instance of this type, provide the contract address of the token.
* &#x20;**Native SDK coins**: To create an asset info instance of this type, provide the denomination ("ujuno","uatom").

```rust
pub enum AssetInfoBase<T> {
    Cw20(T),
    Native(String),
}
```

## ExecuteMsg

### RegisterMerkleRoot

Sets the provided Merkle-root that contains the whitelisted addresses that can claim tokens from the airdrop.

{% hint style="warning" %}
Only the owner can execute `RegisterMerkleRoot`.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg {
 RegisterMerkleRoot {
        merkle_root: String,
        expiration: Option<Expiration>,
        total_amount: Option<Uint128>,
  }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"register_merkle_root":{
"merkle_root":"876dd0a3ef4a2816ffd1c12ab649825a958b0f...",
"total_amount":"1000000"
  }
}
```
{% endtab %}
{% endtabs %}

| Name           | Type                                                                       | Description                                                                                      |
| -------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| `merkle_root`  | String                                                                     | A hex-encoded Merkle root.                                                                       |
| `expiration`   | Option<[Expiration](../platform-and-framework/common-types/expiration.md)> | An optional expiration for the root.                                                             |
| `total_amount` | Option\<Uint128>                                                           | An optional amount to specify the maximum number of tokens that can be claimed from the airdrop. |

### Claim

Claims the funds assigned to the address executing the claim.

{% hint style="warning" %}
Only addresses found in the Merkle-Root can claim tokens.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{  
   Claim {
        stage: u8,
        amount: Uint128,
        proof: Vec<String>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim":{
 "stage": 3,
 "amount":"10000",
 "proof":["876dd0a3ef4a28","..."]
}
```
{% endtab %}
{% endtabs %}

| Name     | Type         | Description                                                                                                                                                                                                                                             |
| -------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `stage`  | u8           | Stage is used to index which airdrop to claim from. There can be more than one airdrop and each is referenced by it's designated stage.                                                                                                                 |
| `amount` | Uint128      | The amount of tokens to claim.                                                                                                                                                                                                                          |
| `proof`  | Vec\<String> | Hex-encoded Merkle proof that proves that the address claiming the tokens from the airdrop is found in the Merkle-Root. Needs to be calculated similar to [this](https://github.com/CosmWasm/cw-plus/tree/0.9.x/contracts/cw20-merkle-airdrop/helpers). |

### Burn

Burn the remaining tokens (unclaimed) after expire time for the specified `stage`.

{% hint style="warning" %}
Only the owner can execute `Burn`.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{
   Burn {
        stage: u8,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"burn":{
 "stage": 2
   }
 }
```
{% endtab %}
{% endtabs %}

| Name    | Type | Description                                                                |
| ------- | ---- | -------------------------------------------------------------------------- |
| `stage` | u8   | The stage of the airdrop used to specify which airdrop to execute burn on. |

### AndrReceive

Check [AndrReceive](../platform-and-framework/ado\_base.md#andrrecieve)

## Query

### Config

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

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ConfigResponse {
    pub asset_info: AssetInfo,
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"asset_info":{
 "Cw20":"juno1..."
   }
 }
```
{% endtab %}
{% endtabs %}

| Name         | Type      | Description        |
| ------------ | --------- | ------------------ |
| `asset_info` | AssetInfo | The type of Asset. |

#### AssetInfo

Represents an asset info instance containing only verified data which is saved in the contract storage.

```rust
pub type AssetInfo = AssetInfoBase<Addr>
```

[AssetInfoBase](merkle-airdrop.md#assetinfobase)

### MerkleRoot

Queries the Merkle-Root  for the specified `stage`.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
MerkleRoot {
 stage: u8
  }
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"merkle_root"{
"stage": 2
   }
}
```
{% endtab %}
{% endtabs %}

|         |    |                                                     |
| ------- | -- | --------------------------------------------------- |
| `stage` | u8 | The stage which we want to get the Merkle root for. |

#### MerkleRootResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct MerkleRootResponse {
    pub stage: u8,
    pub merkle_root: String,
    pub expiration: Expiration,
    pub total_amount: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"stage":2,
"merkle_root":"876dd0a3ef4a2816ffd1c12ab649825a958b0f",
"expiration": {
"at_height": 500
 },
 "total_amount":"1000000"
 }
```
{% endtab %}
{% endtabs %}

| Name           | Type                                                               | Description                                                         |
| -------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------- |
| `stage`        | u8                                                                 | The stage that belongs to this root.                                |
| `merkle_root`  | String                                                             | The Merkle-Root of this stage.                                      |
| `expiration`   | [Expiration](../platform-and-framework/common-types/expiration.md) | The expiration for the airdrop of this stage.                       |
| `total_amount` | Uint128                                                            | The total amount of funds to be airdropped belonging to this stage. |

### LatestStage

Queries the last stage.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
  LatestStage {}
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"latest_stage":{}
}
```
{% endtab %}
{% endtabs %}

#### LastStageResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct LatestStageResponse {
    pub latest_stage: u8,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"latest_stage": 4 
}
```
{% endtab %}
{% endtabs %}

### IsClaimed

&#x20;Checks if the specified `address` has claimed its&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    IsClaimed {
     stage: u8,
     address: String
      }
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"is_claimed": 2,
"address":"juno1..."
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description           |
| --------- | ------ | --------------------- |
| `stage`   | u8     | The stage to check.   |
| `address` | String | The address to check. |

#### IsClaimedResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct IsClaimedResponse {
    pub is_claimed: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"is_claimed": false
}
```
{% endtab %}
{% endtabs %}

| Name         | Type | Description                                                 |
| ------------ | ---- | ----------------------------------------------------------- |
| `is_claimed` | bool | Returns true if the funds have claimed and false otherwise. |

### TotalClaimed

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
 TotalClaimed {
  stage: u8
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"total_claimed":{
"stage": 2
  }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type | Description                            |
| ------- | ---- | -------------------------------------- |
| `stage` | u8   | The stage to check the amount claimed. |

#### TotalClaimedResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct TotalClaimedResponse {
    pub total_claimed: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"total_claimed":"10000"
}
```
{% endtab %}
{% endtabs %}

| Name            | Type    | Description                            |
| --------------- | ------- | -------------------------------------- |
| `total_claimed` | Uint128 | The stage to check the amount claimed. |

### AndrQuery

Check [AndrQuery](../platform-and-framework/ado\_base.md#andrquery).