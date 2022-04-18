# Anchor

## Introduction

The Anchor ADO is a smart contract that allows users to deposit funds to utilize the functionalities used by anchor protocol. For now, the ADO supports depositing to anchor and borrowing. For more information on what is anchor protocol refer to their docs found [here](https://docs.anchorprotocol.com/smart-contracts/deployed-contracts).

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

The primitive contract will have all the anchor addresses needed stored before working with the anchor contract. These include the contract addresses for:

```rust
pub const ANCHOR_MARKET: &str = "anchor_market_contract";
pub const ANCHOR_OVERSEER: &str = "anchor_overseer_contract";
pub const ANCHOR_BLUNA_HUB: &str = "anchor_bluna_hub_contract";
pub const ANCHOR_BLUNA_CUSTODY: &str = "anchor_bluna_custody_contract";
pub const ANCHOR_ORACLE: &str = "anchor_oracle_contract";
pub const ANCHOR_AUST: &str = "anchor_aust_contract";
pub const ANCHOR_BLUNA: &str = "anchor_bluna_contract";
```

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

{% hint style="info" %}
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
Only the contract can execute `DepositCollateralToAnchor`
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
"collateral_addr":"terra1...",
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
"addr":"terra1...",
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name              | Type                                 | Description                                                                                                                                              |
| ----------------- | ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `collateral_addr` | String                               | The address of the collateral token, should be the same as the anchor\_bluna token address since it is the only one supported as collateral **for now**. |
| `amount`          | Option\<Uint256>                     | The amount of collateral to withdraw.                                                                                                                    |
| `unbond`          | Option\<bool>                        | Optional flag to whether to unbond the funds. Defaults to false.                                                                                         |
| `recipient`       | Option<[Recipient](../recipient.md)> | Optional Recipient to the withdrawn funds.                                                                                                               |

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

| Name                | Type                                 | Description                                                                                                                                                                                                                                                                                             |
| ------------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `desired_ltv_ratio` | Decimal256                           | The desired loan-to-value ratio. Needs to be less than 1. More info on LTV can be found [here](https://spectrocoin.com/en/faqs/crypto-loans/what-is-ltv-ratio-and-how-to-maintain-it.html#:\~:text=The%20loan%2Dto%2Dvalue%20\(LTV\)%20ratio%20shows%20the,usually%20expressed%20as%20a%20percentage.). |
| `recipient`         | Option<[Recipient](../recipient.md)> | The address to receive the funds.                                                                                                                                                                                                                                                                       |

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

The rest of the executes can be found in the [`AndrReceive`](../ado\_base/andrreceive-andrquery.md#andrrecieve) section.

## QueryMsg

### AndrQuery

```rust
fn handle_andromeda_query(
    deps: Deps,
    env: Env,
    msg: AndromedaQuery,
) -> Result<Binary, ContractError> {
    match msg {
        AndromedaQuery::Get(data) => {
            let recipient: String = parse_message(&data)?;
            encode_binary(&query_position(deps, recipient)?)
        }
        _ => ADOContract::default().query(deps, env, msg, query),
    }
}
```

If the type is a `Get`, it will query the current anchor position and return a `PositionResponse`:

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct PositionResponse {
    pub recipient: Recipient,
    pub aust_amount: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"recipient":{
"addr":"terra1...",
  }
"aust_amount":"10000"
}
```
{% endtab %}
{% endtabs %}

The rest of the base queries can be found in [AndrQuery](../ado\_base/andrreceive-andrquery.md#andromedaquery) section.
