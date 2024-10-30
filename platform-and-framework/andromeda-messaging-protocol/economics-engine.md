# Economics Engine

The Economics ADO allows users to deposit funds to be used to pay fees implemented on ADO actions by the [ADODB](andromeda-factory.md). Deposited funds can be either native funds such as "uandr" or CW20 tokens where the contract address is used. The fees are automatically called by the ADO that implements them.&#x20;

Fees are charged in the following order:

{% hint style="warning" %}
A fee can be paid by a combination of the below. For example if a 1000 uandr fee is required and the balance of the ADO is 800 uandr, then 800 will be deducted from the 1000, and the remainder is paid by App contract. If the App does not have sufficient funds to cover the rest, then the funds are deducted from the payee next.
{% endhint %}

1. **ADO:** First, the ADO requesting the fees is checked for funds and if available will use these funds to pay the fee.
2. **App:** The App contract of the ADO requesting the fees.
3. **Payee:** The address that sent the message to the ADO that is requesting the fees.

**Ado\_type:** economics

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"kernel_address":"andr1...",
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name             | Type            | Description                                                                                                                         |
| ---------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `kernel_address` | String          | The address of the kernel contract on chain. This can be found in our [deployed contracts](<../deployed-contracts (1).md>) section. |
| `owner`          | Option\<String> | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                         |

## ExecuteMsg

### Deposit

Deposit funds to be used by the Andromeda economics module to pay for ADO fees.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
     Deposit {
        address: Option<AndrAddr>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json">{
"deposit":{
<strong>    "address":"andr1..."
</strong>    }
}
</code></pre>
{% endtab %}
{% endtabs %}

| Name      | Type                                            | Description                                                                                                                         |
| --------- | ----------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `address` | Option<[AndrAddr](../common-types.md#andraddr)> | If specified, then your deposit would be on the behalf of  the AndrAddr. If not specified, then the deposit will be for the sender. |

### Receive

Handles receiving CW20 tokens to be deposited as funds to pay fees with. Called by sending CW20 tokens from the CW20 ADO using the [Send](../../andromeda-digital-objects/cw20-v1.0.0.md#send) execute message.&#x20;

{% hint style="warning" %}
This is not called directly by the user, but called when the user sends CW20 tokens to this ADO.&#x20;

The msg field of the Send execute should be a base64 encoded [Cw20HookMsg](economics-engine.md#cw20hookmsg).
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Receive(Cw20ReceiveMsg),
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

The `msg` field needs to be a `Cw20HookMsg` of type `Deposit`. The message is attached as a bsae64 encoded binary of the JSON representation of the Deposit message.

#### Cw20HookMsg

Deposits CW20 tokens to be used as funds to pay fees.

```rust
#[cw_serde]
pub enum Cw20HookMsg {
    Deposit { address: Option<AndrAddr> },
}
```

| Name      | Type                                            | Description                                                                                                                         |
| --------- | ----------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `address` | Option<[AndrAddr](../common-types.md#andraddr)> | If specified, then your deposit would be on the behalf of the AndrAddr. If not specified, then the deposit will be for the sender.  |

### PayFee

Pay a fee for the given action. The sender must be a valid ADO contract.

{% hint style="warning" %}
This message is never called by the user directly, but by an ADO when the user calls a message that requires fees to be payed.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    PayFee {
        payee: Addr,
        action: String,
    },
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                                                                   |
| -------- | ------ | --------------------------------------------------------------------------------------------- |
| `payee`  | Addr   | The address that will pay in case the ADO and App did not have enough funds to cover the fee. |
| `action` | String | The action we are paying a fee for.                                                           |

### Withdraw

Withdraw native funds from the Andromeda economics module. If no amount is provided, all funds are withdrawn for the given asset.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     Withdraw {
        amount: Option<Uint128>,
        asset: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw":{
    "amount":"500000",
    "asset":"uandr"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                            | Description                                                                                         |
| -------- | ----------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| `amount` | Option<[AndrAddr](../common-types.md#andraddr)> | The amount of the specified asset to withdraw. If not specified then the total amount is withdrawn. |
| `asset`  | String                                          | The native asset to withdraw.                                                                       |

### WithdrawCW20

Withdraw CW20 funds from the Andromeda economics module. If no amount is provided all funds are withdrawn for the given asset.

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong> pub enum ExecuteMsg {
</strong><strong> #[serde(rename = "withdraw_cw20")]
</strong><strong> WithdrawCW20 {
</strong>        amount: Option&#x3C;Uint128>,
        asset: String,
    }
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw_cw20":{
    "amount":"800",
    "asset":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                                                                                         |
| -------- | ---------------- | --------------------------------------------------------------------------------------------------- |
| `amount` | Option\<Uint128> | The amount of the specified asset to withdraw. If not specified then the total amount is withdrawn. |
| `asset`  | String           | The contract address of the CW20 token to withdraw.                                                 |

### Ownership

The set of ownerhsip messages. These messages are the same as the ones found in the [ADO base section](../ado-base/andromedamsg-v1.0.0.md#ownership).

## QueryMsg

### Balance

Queries the amount of the specified `asset` for the specified `address`.

{% tabs %}
{% tab title="Rust " %}
```rust
pub enum QueryMsg {
    #[returns(Uint128)]
    Balance { asset: String, address: AndrAddr },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"balance":{
    "asset":"uandr",
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                                    | Description                                                                                |
| --------- | --------------------------------------- | ------------------------------------------------------------------------------------------ |
| `asset`   | String                                  | The asset to get the balance for. Can be either a native token or a CW20 contract address. |
| `address` | [AndrAddr](../common-types.md#andraddr) | The address to get the balance for.                                                        |

Returns a `Uint128` representing the current balance.

### Version

Queries the version of the ADO.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
     #[returns(VersionResponse)]
     Version {}
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"version":{}
}
```
{% endtab %}
{% endtabs %}

#### VersionResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct VersionResponse {
    pub version: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"version": "0.1.0"
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Descripton               |
| --------- | ------ | ------------------------ |
| `version` | String | The version of the ADO.  |

### Owner

Queries the owner of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
    #[returns(ContractOwnerResponse)]
    Owner{}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "owner":{}
  }
}
```
{% endtab %}
{% endtabs %}

#### ContractOwnerResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ContractOwnerResponse {
    pub owner: String
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

### Type

Queries the ADO type.&#x20;

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum AndromedaQuery {
<strong>    #[returns(TypeResponse)]
</strong>    Type {}
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json">{
<strong>"type":{}
</strong>}
</code></pre>
{% endtab %}
{% endtabs %}

#### TypeResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct TypeResponse {
    pub ado_type: String,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ado_type":"auction"
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description          |
| ---------- | ------ | -------------------- |
| `ado_type` | String | The type of the ado. |

### KernelAddress

Queries the kernel address of the chain the ADO is deployed on.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(KernelAddressResponse)]
    KernelAddress {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"kernel_address":{}
}
```
{% endtab %}
{% endtabs %}

Returns a String containing the contract address of the Kernel.
