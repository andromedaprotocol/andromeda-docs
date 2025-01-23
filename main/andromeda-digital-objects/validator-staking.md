# Validator Staking

## Introduction

The **Staking** ADO is smart contract that allows users to stake native tokens with the validator of their choice.&#x20;

This ADO is not meant for general use meaning that it should not be open for the public (Have people other than the owner use it for staking). It is designed so the owner of the ADO has full control over the staking which is why most messages are restricted to the owner.&#x20;

The staking message can still be called by any address. This is to allow the ADO owner to integrate this ADO with Andromeda Apps allowing the allocation of funds gained by the App to be routed to this ADO and staked.&#x20;

#### Example

Let us assume a project is looking to start an NFT project having part of the project be that the users will get some funds over time:

{% hint style="info" %}
This would be just one part of the benefits of buying the project's NFT.&#x20;
{% endhint %}

* Project is looking to launch an NFT collection.
* &#x20;The collection will be sold using one of the ADOs like our Marketplace ADO.&#x20;
* Using a splitter connection, the funds from the sale are distributed having part of the funds go to the Staking ADO and start staking automatically.
* The rewards accrued from staking are then sent to another splitter that distributes the rewards back to the NFT buyers.

This gives a small idea of how you can use the **Validator** **Staking ADO** in your Andromeda Applications to achieve very usefull and cool use cases.&#x20;

**Ado-type:** validator-staking

**Version:  1.1.0**

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub default_validator: Addr,
    pub kernel_address:String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"default_validator":"andrvaloper1...",
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="252.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>default_validator</code></td><td>Addr</td><td>The address of the validator to delegate the tokens to by default. This means when you call a <a href="validator-staking.md#stake"><strong>Stake</strong></a> message without specifying the <code>validator</code> field, then the tokens will be staked with the default validator.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the kernel contract to be used for AMP messaging. Kernel contract address can be found in our deployed contracts.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

## ExecuteMsg

### Stake

Stakes the attached funds with the specified validator.

{% hint style="warning" %}
* If the validator is not specified, then the `default_validator` specified at instantiation will be used.
* Make sure to attach one type of funds only.
{% endhint %}



{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Stake {
        validator: Option<Addr>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"stake":{
    "validator":"andrvaloper1..."
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="252.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>validator</code></td><td>Option&#x3C;Addr></td><td>The address of the validator to delegate the tokens to.</td></tr></tbody></table>

### Unstake

Unstakes the delegated tokens from the specified validator.

{% hint style="warning" %}
* Only available to the contract owner.
* If the validator is not specified, then the `default_validator` specified at instantiation will be used.
* The tokens will be released back to the ADO after the unbonding period has passed. You will need to call the **WithdrawFunds** message after that to get the tokens back into your wallet.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   Unstake {
        validator: Option<Addr>,
        amount: Option<Uint128>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unstake":{
    "validator":"andrvaloper1...",
    "amount": "5000"
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="252.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>validator</code></td><td>Option&#x3C;Addr></td><td>The address of the validator to undelegate the tokens from.</td></tr><tr><td><code>amount</code></td><td>Option&#x3C;Uint128></td><td>The amount of tokens to  unstake. Defaults to the maximum amount if not specified.</td></tr></tbody></table>

### Claim

Claim the rewards accrued from staking for a specific validator.

{% hint style="warning" %}
* Only available to the contract owner.
* If the validator is not specified, then the `default_validator` specified at instantiation will be used.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   Claim {
        validator: Option<Addr>,
    },
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim":{
    "validator":"andrvaloper1..."
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="252.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>validator</code></td><td>Option&#x3C;Addr></td><td>The address of the validator to claim the rewards for.</td></tr></tbody></table>

### WithdrawFunds

Withdraw unstaked tokens from the ADO.

{% hint style="warning" %}
* Only available to the contract owner.
* Make sure the unbonding period has passed before trying to withdraw funds.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  WithdrawFunds {
        denom: Option<String>,
        recipient: Option<AndrAddr>,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw_funds":{
    "denom":"uandr",
    "recipient":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="252.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>denom</code></td><td>Option&#x3C;String></td><td>The denom of the previously staked funds to withdraw. If not specified, all available funds are withdrawn</td></tr><tr><td><code>recipient</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#andraddr">AndrAddr</a>></td><td>An optional address to receive the withdrawn funds. Will default to the sender if not specified.</td></tr></tbody></table>

### UpdateDefaultValidator

Update the set default validator.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  UpdateDefaultValidator {
        validator: Addr,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_default_validator":{
    "validator":"andrvaloper1..."
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="252.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>validator</code></td><td>Addr</td><td>The new validator address to be used by default.</td></tr></tbody></table>

### Redelegate

Moves the specified amount of staked tokens from one validator to another.&#x20;

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg {
   Redelegate {
        src_validator: Option<Addr>,
        dst_validator: Addr,
        amount: Option<Uint128>,
    },
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"redelegate":{
    "src_validator":"andrvaloper1...",
    "dst_validator":"andrvaloper1...",
    "amount": "10000"
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type             | Description                                                                    |
| --------------- | ---------------- | ------------------------------------------------------------------------------ |
| `src_validator` | Option\<Addr>    | The address of the validator to move tokens from.                              |
| `dst_validator` | Addr             | The address of the validator to move tokens to.                                |
| `amount`        | Option\<Uint128> | The amount of tokens to redelegate. Takes the maximum amount if not specified. |

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### StakedTokens

Queries the staked tokens with the specified validator.

{% hint style="warning" %}
If the validator is not specified, then the `default_validator` specified at instantiation will be used.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(Option<::cosmwasm_std::FullDelegation>)]
    StakedTokens { validator: Option<Addr> },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"staked_tokens":{
    "validator":"andrvaloper1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type          | Description                                   |
| ----------- | ------------- | --------------------------------------------- |
| `validator` | Option\<Addr> | The validator to check the staked tokens for. |

#### FullDelegation

Returns the information of the staked tokens.

```rust
pub struct FullDelegation {
    pub delegator: Addr,
    pub validator: String,
    pub amount: Coin,
    pub can_redelegate: Coin,
    pub accumulated_rewards: Vec<Coin>,
}
```

| Name                  | Type                                                        | Description                                                                                                                                  |
| --------------------- | ----------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `delegator`           | Addr                                                        | The address of the delegator. Will be the address of the staking ADO in our case.                                                            |
| `validator`           | String                                                      | The validator that the tokens are delegated to.                                                                                              |
| `amount`              | [Coin](../platform-and-framework/common-types.md#coin)      | The amount of funds locked in the delegation.                                                                                                |
| `can_redelegate`      | [Coin](../platform-and-framework/common-types.md#coin)      | `can_redelegate` captures how much can be immediately redelegated. 0 is no redelegation and `can_redelegate` == `amount` is redelegate all.  |
| `accumulated_rewards` | Vec<[Coin](../platform-and-framework/common-types.md#coin)> | The amount of rewards accruied from the staking that can be currently withdrawn.                                                             |

### UnstakedTokens

Queries the tokens that are unstaked and provides the time they can be claimed (Unbonding period is over).

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum QueryMsg {
 #[returns(Option<Vec<UnstakingTokens>>)]
    UnstakedTokens {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ustaked_tokens":{}
}
```
{% endtab %}
{% endtabs %}

#### UnstakingTokens

Struct containing information of the unstaked tokens.

```rust
pub struct UnstakingTokens {
    pub fund: Coin,
    pub payout_at: Timestamp,
}
```

| Name        | Type                                                             | Description                                                                          |
| ----------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `fund`      | [Coin](../platform-and-framework/common-types.md#coin)           | The denom and amount of the token being unstaked.                                    |
| `payout_at` | [Timestamp](../platform-and-framework/common-types.md#timestamp) | The time these tokens can be claimed which is the time the unbonding period is over. |

### DefaultValidator

Queries the default validator address set at instantiation.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
   #[returns(GetDefaultValidatorResponse)]
    DefaultValidator {},
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"default_validator":{}
}
```
{% endtab %}
{% endtabs %}

Returns the address as a string.

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
