# Astroport

**Ado\_type**: astroport

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub primitive_contract: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"primitive_contract":"terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type   | Description                                                        |
| -------------------- | ------ | ------------------------------------------------------------------ |
| `primitive_contract` | String | The primitive contract to store the different Astroport contracts. |

The primitive contract will have all the Astroport addresses needed before working with the contract. These include the contract addresses for:

{% tabs %}
{% tab title="Rust" %}
```rust
pub const ASTROPORT_FACTORY: &str = "astroport_factory_contract";
pub const ASTROPORT_ROUTER: &str = "astroport_router_contract";
pub const ASTROPORT_STAKING: &str = "astroport_staking_contract";
pub const ASTROPORT_GENERATOR: &str = "astroport_generator_contract";
pub const ASTROPORT_ASTRO: &str = "astroport_astro_contract";
pub const ASTROPORT_XASTRO: &str = "astroport_xastro_contract";
```
{% endtab %}
{% endtabs %}

| Name                  | Description                                                                                                                                       |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ASTROPORT_FACTORY`   | Information on the Astroport factory contract can be found [here](https://docs.astroport.fi/astroport/smart-contracts/astroport-factory).         |
| `ASTROPORT_ROUTER`    | Information on the Astroport router contract can be found [here](https://docs.astroport.fi/astroport/smart-contracts/router).                     |
| `ASTROPORT_STAKING`   | Information on the Astroport staking contract can be found [here](https://docs.astroport.fi/astroport/smart-contracts/tokenomics/xastro-staking). |
| `ASTROPORT_GENERATOR` | Information on the Astroport token contract can be found [here](https://docs.astroport.fi/astroport/smart-contracts/tokenomics/generator).        |
| `ASTROPORT_ASTRO`     | Information of Astro contract can be found [here](https://docs.astroport.fi/astroport/smart-contracts/astro-token).                               |
| `ASTROPORT_XASTRO`    | Information of the xAstro contract can be found [here](https://docs.astroport.fi/astroport/smart-contracts/tokenomics/xastro-token).              |

## ExecuteMsg

### Swapper

Swaps between to types of funds.

{% hint style="info" %}
The swap should be supported by Astroport.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Swapper(SwapperMsg)
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"swapper":{
      "swap":{
            "offer_asset_info": {
                "cw20":"terra1..."
                },
              "ask_asset_info": {
                "native":"uusd"
                }
          }
     }
```
{% endtab %}
{% endtabs %}

#### SwapperMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum SwapperMsg {
    Swap {
        offer_asset_info: AssetInfo,
        ask_asset_info: AssetInfo,
    },
}
```
{% endtab %}
{% endtabs %}

| Name               | Type      | Description                       |
| ------------------ | --------- | --------------------------------- |
| `offer_asset_info` | AssetInfo | The coins that are being swapped. |
| `ask_asset_info`   | AssetInfo | The funds we want to swap to.     |

#### AssetInfo

```rust
pub type AssetInfo = AssetInfoBase<Addr>
```

#### AssetInfoBase

```rust
pub enum AssetInfoBase<T> {
    Cw20(T),
    Native(String),
}
```

* **CW20**: tokens. To create an **asset info** instance of this type, provide the contract address
* **Native SDK coins**: To create an **asset info** instance of this type, provide the denomination.

### Receive

Users can perform a swap by sending Cw-20 tokens to the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Receive(Cw20ReceiveMsg)
}
```
{% endtab %}
{% endtabs %}

#### Cw20ReceiveMsg

```rust
pub struct Cw20ReceiveMsg {
    pub sender: String,
    pub amount: Uint128,
    pub msg: Binary,
}
```

The `msg` in `Cw20ReceiveMsg` should be a `Cw20HookMsg` of type `Swapper`.

#### Cw20HookMsg

```rust
pub enum Cw20HookMsg {
    Swapper(SwapperCw20HookMsg),
}
```

#### SwapperCw20HookMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum SwapperCw20HookMsg {
    Swap { ask_asset_info: AssetInfo },
}
```
{% endtab %}
{% endtabs %}

| Name             | Type                                | Description                  |
| ---------------- | ----------------------------------- | ---------------------------- |
| `ask_asset_info` | [AssetInfo](astroport.md#assetinfo) | The coin we want to swap to. |

### ProvideLiquidity

Provided Liquidity to the specified asset pair.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
      ProvideLiquidity {
        assets: [AssetUnchecked; 2],
        slippage_tolerance: Option<Decimal>,
        auto_stake: Option<bool>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"provide_liquidity": {
    "assets": [
      {
        "info": {
          "cw20":"terra1..."
          },
        "amount": "1000000"
      },
      {
        "info": {
          "native":"uusd" 
        },
        "amount": "1000000"
      }
    ],
    "slippage_tolerance": "0.01",
    "auto_stake": true
      }
    }
 }        
```
{% endtab %}
{% endtabs %}

| Name                 | Type                        | Description                                                                                                                                         |
| -------------------- | --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `assets`             | Vector of 2 AssetUnchecked  | The pair of assets we want to provide liquidity for.                                                                                                |
| `slippage_tolerance` | Optional\<Decimal>          | The slippage tolerance. If not familiar, definition can be found [here](https://docs.coin98.com/products/coin98-super-app/faqs/slippage-tolerance). |
| `auto_stake`         | Option\<bool>               | Whether to automatically stake the funds.                                                                                                           |

### WithdrawLiquidity

Withdraw liquidity from the pool.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
       WithdrawLiquidity {
        pair_address: String,
        amount: Option<Uint128>,
        recipient: Option<Recipient>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw_liquidity":{
    "pair_address":"terra...",
    "amount":"500",
    "recipient":{
        "addr":"terra1..."
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name           | Type                                                 | Description                                                         |
| -------------- | ---------------------------------------------------- | ------------------------------------------------------------------- |
| `pair_address` | String                                               | The contract address of the  asset pair to withdraw liquidity from. |
| `amount`       | Option\<Uint128>                                     | The amount of liquidity to withdraw. Defaults to the total amount.  |
| `recipient`    | Option<[Recipient](../../common-types/recipient.md)> | The address to receive the funds. Defaults to the sender.           |

### StakeLp

Stakes the specified `amount` of Lp tokens.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{
 StakeLp {
        lp_token_contract: String,
        amount: Option<Uint128>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"stake_lp":{
    "lp_token_contract":"terra1...",
    "amount":"300"
    }
}
```
{% endtab %}
{% endtabs %}

| Name                | Type             | Description                        |
| ------------------- | ---------------- | ---------------------------------- |
| `lp_token_contract` | String           | The liquidity pool token contract. |
| `amount`            | Option\<Uint128> | Optional amount to stake.          |

### UnstakeLp

Unstakes the specified `amount` of Lp tokens.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{ 
  UnstakeLp {
        lp_token_contract: String,
        amount: Option<Uint128>,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unstake_lp":{
    "lp_token_contract": "terra1...",
    "amount":"1000"
    }
}  
```
{% endtab %}
{% endtabs %}

| Name                | Type             | Description                                         |
| ------------------- | ---------------- | --------------------------------------------------- |
| `lp_token_contract` | String           | The liquidity pool contract to unstake tokens from. |
| `amount`            | Option\<Uint128> | The amount of tokens to unstake.                    |

### ClaimLpStakingRewards

Claims the rewards for staking from the specified liquidity pool contract.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{
 ClaimLpStakingRewards {
        lp_token_contract: String,
        auto_stake: Option<bool>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim_lp_staking_rewards":{
    "lp_token_contract":"terra1...",
    "auto_stake": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name                | Type          | Description                                                            |
| ------------------- | ------------- | ---------------------------------------------------------------------- |
| `lp_token_contract` | String        | The liquidity pool contract to claim rewards from.                     |
| `auto_stake`        | Option\<bool> | Whether to automatically stake the claimed rewards. Defaults to false. |

### StakeAstro

Stakes the specified `amount` of Astro tokens.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{
 StakeAstro {
        amount: Option<Uint128>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"stake_astro":{
    "amount":"1000"
     }
  }
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                                                                                      |
| -------- | ---------------- | ------------------------------------------------------------------------------------------------ |
| `amount` | Option\<Uint128> | The amount of Astro tokens to stake. If not specified then the available balance will be staked. |

### UnstakeAstro

Unstake the specified `amount` of Astro tokens.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
UnstakeAstro {
           amount: Option<Uint128>,
     }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unstake_astro":{
    "amount":"1000"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                                                                            |
| -------- | ---------------- | -------------------------------------------------------------------------------------- |
| `amount` | Option\<Uint128> | Optional amount of Astro tokens to unstake. If not specified, will unstake all tokens. |

### AstroportExecuteMsg

{% tabs %}
{% tab title="Rust" %}
```rust
AstroportFactoryExecuteMsg(AstroportFactoryExecuteMsg),
```
{% endtab %}
{% endtabs %}

#### AstroportFactoryExecuteMsg

```rust
pub enum ExecuteMsg {
    /// UpdateConfig updates relevant code IDs
    UpdateConfig {
        /// CW20 token contract code identifier
        token_code_id: Option<u64>,
        /// contract address to send fees to
        fee_address: Option<String>,
        /// contract address that used for auto_stake from pools
        generator_address: Option<String>,
    },
    /// UpdatePairConfig updates configs of pair
    UpdatePairConfig {
        /// new [`PairConfig`] settings for pair
        config: PairConfig,
    },
    /// CreatePair instantiates pair contract
    CreatePair {
        /// the type of pair available in [`PairType`]
        pair_type: PairType,
        /// the type of asset infos available in [`AssetInfo`]
        asset_infos: [AssetInfo; 2],
        /// Optional binary serialised parameters for custom pool types
        init_params: Option<Binary>,
    },
    /// Deregister removes a previously created pair
    Deregister {
        /// the type of asset infos available in [`AssetInfo`]
        asset_infos: [AssetInfo; 2],
    },
    /// ProposeNewOwner creates an offer for a new owner. The validity period of the offer is set in the `expires_in` variable.
    ProposeNewOwner {
        /// contract address that used for controls settings for factory, pools and tokenomics contracts
        owner: String,
        /// the offer expiration date for the new owner
        expires_in: u64,
    },
    /// DropOwnershipProposal removes the existing offer for the new owner.
    DropOwnershipProposal {},
    /// Used to claim(approve) new owner proposal, thus changing contract's owner
    ClaimOwnership {},
}
```

Information on the Factory contract execute messages can be found in the Astroport docs [here](https://docs.astroport.fi/astroport/smart-contracts/astroport-factory).

### AndrReceive

{% hint style="info" %}
Uses withdraw and primitive features.
{% endhint %}

Check [AndrReceive](../../ado\_base/andrreceive-andrquery.md#andrrecieve).

## QueryMsg

### AndrQuery

Check [AndrQuery](../../ado\_base/andrreceive-andrquery.md#andromedaquery).
