# Anchor Lend

## Introduction

The Anchor Lend ADO is a smart contract that allows users to deposit funds to utilize the functionalities used by anchor protocol.&#x20;

For now, the ADO supports depositing to anchor, borrowing, and staking. For more information on what is anchor protocol refer to their docs found [here](https://docs.anchorprotocol.com/smart-contracts/deployed-contracts).

**Ado\_type**: anchor-lend

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub primitive_contract: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"primitive_contract":"terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type   | Description                                                                       |
| -------------------- | ------ | --------------------------------------------------------------------------------- |
| `primitive_contract` | String | The primitive contract that would contain all the anchor addresses needed for the |

The primitive contract will have all the anchor addresses needed  before working with the anchor contract. These include the contract addresses for:

```rust
pub const ANCHOR_MARKET: &str = "anchor_market_contract";
pub const ANCHOR_OVERSEER: &str = "anchor_overseer_contract";
pub const ANCHOR_BLUNA_HUB: &str = "anchor_bluna_hub_contract";
pub const ANCHOR_BLUNA_CUSTODY: &str = "anchor_bluna_custody_contract";
pub const ANCHOR_ORACLE: &str = "anchor_oracle_contract";
pub const ANCHOR_BLUNA: &str = "anchor_bluna_contract";
pub const ANCHOR_GOV: &str = "anchor_gov_contract";
pub const ANCHOR_ANC: &str = "anchor_anc_contract";
```

| Name                   | Description                                                                                                                                                                                                 |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ANCHOR_MARKET`        | Information on the market contract can be found [here](https://docs.anchorprotocol.com/smart-contracts/money-market/market).                                                                                |
| `ANCHOR_OVERSEER`      | Information on the overseer contract can be found [here](https://docs.anchorprotocol.com/smart-contracts/money-market/overseer).                                                                            |
| `ANCHOR_BLUNA_HUB`     | Information on the bluna hub contract can be found [here](https://docs.terra.lido.fi/contracts/hub/).                                                                                                       |
| `ANCHOR_BLUNA_CUSTODY` | Information on the bluna custody contract can be found here.                                                                                                                                                |
| `ANCHOR_ORACLE`        | Information on the oracle contract can be found [here](https://docs.anchorprotocol.com/smart-contracts/money-market/oracle).                                                                                |
| `ANCHOR_AUST`          | Cw20 compliant contract. Contract address of the deployed  anchor AUST contract can be found [here](https://docs.anchorprotocol.com/smart-contracts/deployed-contracts) (money market section).             |
| `ANCHOR_BLUNA`         | Cw20 compliant contract. Contract address of the deployed  anchor AUST contract can be found [here](https://docs.anchorprotocol.com/smart-contracts/deployed-contracts) (money market section).             |
| `ANCHOR_GOV`           | Information on the oracle contract can be found [here](https://docs.anchorprotocol.com/smart-contracts/anchor-token/gov).                                                                                   |
| `ANCHOR_ANC`           | Cw-20 compliant token contract. Contract address of the deployed  anchor AUST contract can be found [here](https://docs.anchorprotocol.com/smart-contracts/deployed-contracts#anc-related-smart-contracts). |

The contract addresses of these deployed contracts by anchor can be found [here](https://docs.anchorprotocol.com/smart-contracts/deployed-contracts).

## ExecuteMsg

### Receive

Deposits collateral by sending Cw20 tokens to the contract.

```rust
pub enum ExecuteMsg {
    Receive(Cw20ReceiveMsg)
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

{% hint style="info" %}
The message in Cw20ReceiveMsg should be a Cw20HookMsg of type `DepositCollateral.`
{% endhint %}

#### Cw20HookMsg

Deposit Cw20 assets as collateral.

```rust
pub enum Cw20HookMsg {
    DepositCollateral {},
}
```

### DepositCollateral

Deposit LUNA as collateral which will be converted to bLUNA. The collateral funds are then deposited to anchor by executing `DepositCollateralToAnchor`.

{% hint style="warning" %}
Only the owner or an operator of the contract can execute `DepositCollateral`.

Only "uluna" can be used as collateral.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
 DepositCollateral {}
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deposit_collateral":{}
}
```
{% endtab %}
{% endtabs %}

### DepositCollateralToAnchor

An internal function to deposit collateral to anchor. It is automatically called when we execute `DepositCollateral`.

{% hint style="info" %}
Only the contract can execute `DepositCollateralToAnchor.`
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
DepositCollateralToAnchor {
    collateral_addr: String,
 }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deposit_collateral_to_anchor":{
"collateral_addr":"terra1..."
   }
}
```
{% endtab %}
{% endtabs %}

| Name              | Type   | Description                                                                                                                                              |
| ----------------- | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `collateral_addr` | String | The address of the collateral token, should be the same as the anchor\_bluna token address since it is the only one supported as collateral **for now**. |

### WithdrawColllateral

Withdraw specified collateral. If unbond is true and collateral is bLuna, the unbonding process will begin, otherwise the collateral will be sent to the given recipient.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  WithdrawCollateral {
        collateral_addr: String,
        amount: Option<Uint256>,
        unbond: Option<bool>,
        recipient: Option<Recipient>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"withdraw_collateral":{
"collateral_addr":"terra1...",
"amount":"10000",
"unbond": false,
"recipient":{
        "addr":"terra1..."
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name              | Type                                                 | Description                                                                                                                                              |
| ----------------- | ---------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `collateral_addr` | String                                               | The address of the collateral token, should be the same as the anchor\_bluna token address since it is the only one supported as collateral **for now**. |
| `amount`          | Option\<Uint256>                                     | The amount of collateral to withdraw.                                                                                                                    |
| `unbond`          | Option\<bool>                                        | Optional flag to whether to unbond the funds. Defaults to false.                                                                                         |
| `recipient`       | Option<[Recipient](../../common-types/recipient.md)> | Optional Recipient to the withdrawn funds.                                                                                                               |

### Borrow

Borrows funds to reach the desired loan-to-value ratio and sends the borrowed funds to the given recipient.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
  Borrow {
        desired_ltv_ratio: Decimal256,
        recipient: Option<Recipient>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"borrow":{
"desired_ltv_ration": "0.8",
"recipient":{
"addr":"terra1..."
  }
}
```
{% endtab %}
{% endtabs %}

| Name                | Type                                                 | Description                                                                                                                                                                                 |
| ------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `desired_ltv_ratio` | Decimal256                                           | The desired loan-to-value ratio. Needs to be less than 1. More info on LTV can be found [here](https://spectrocoin.com/en/faqs/crypto-loans/what-is-ltv-ratio-and-how-to-maintain-it.html). |
| `recipient`         | Option<[Recipient](../../common-types/recipient.md)> | The address to receive the funds.                                                                                                                                                           |

### RepayLoan

Repays any existing loan with sent stable coins.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
RepayLoan {}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"repay_loan":{}
}
```
{% endtab %}
{% endtabs %}

### StakeAnc

{% hint style="info" %}
Only available to the owner/operators.
{% endhint %}

Stakes all or the specified amount of ANC tokens in the contract in governance.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     StakeAnc {
        amount: Option<Uint128>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"stake_anc":{
    "amount":"1000"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                                                                         |
| -------- | ---------------- | ----------------------------------------------------------------------------------- |
| `amount` | Option\<Uint128> | Optional amount to stake. If not specified, the maximum available amount is staked. |

### UnstakeAnc

{% hint style="info" %}
Only available to the owner/operators.
{% endhint %}

Unstakes all or the specified amount of ANC tokens in the contract in governance.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UnstakeAnc {
        amount: Option<Uint128>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unstake_anc":{
    "amount":"1000"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                                                                           |
| -------- | ---------------- | ------------------------------------------------------------------------------------- |
| `amount` | Option\<Uint128> | Optional amount to unstake. If not specified, the maximum available amount is staked. |

### ClaimAncRewards

Claims any outstanding ANC rewards with an option to stake them in governance.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    ClaimAncRewards {
        auto_stake: Option<bool>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim_anc_rewards":{
    "auto_stake": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type          | Description                                                                                    |
| ------------ | ------------- | ---------------------------------------------------------------------------------------------- |
| `auto_stake` | Option\<bool> | Whether to automatically stake the rewards in governance. If not specified, defaults to false. |

### WithdrawUnbonded

Withdraws any unbonded bLuna from the hub contract.

{% hint style="info" %}
Only available to the owner/operators.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 WithdrawUnbonded {
        recipient: Option<Recipient>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```
// Some code
```
{% endtab %}
{% endtabs %}

| Name        | Type                 | Description                                                   |
| ----------- | -------------------- | ------------------------------------------------------------- |
| `recipient` | Optional\<Recipient> | An optional recipient to the tokens. Defaults to the sender.  |

### AndrReceive

{% hint style="info" %}
Uses the withdraw and primitive features.
{% endhint %}

Check[ AndrReceive](../../ado\_base/andrreceive-andrquery.md#andrrecieve).

## QueryMsg

### AndrQuery

Check [AndrQuery](../../ado\_base/andrreceive-andrquery.md#andrquery).
