# CW721 Timelock

## Introduction

Lock an NFT with a contract for a certain amount of time (currently between one day & one year). Once the timelock has expired, anyone can call the `claim` function which will send the NFT to the defined recipient. Each locked NFT has a specific lock id which is compromised of the CW721 contract address concatenated with the token\_id.&#x20;

For example, if an NFT with token id "token-1" is sent from the CW721 ADO " andr1uqju2r...daskv9a6m", then the `lock_id` would be:

&#x20;"andr1uqju2r...daskv9a6mtoken-1"

**Ado\_type:** cw721-timelock

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub struct InstantiateMsg {
        pub kernel_address: String,        
<strong>        }
</strong></code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name             | Type   | Description                                                                                                                                                                                                                                                                                                                   |
| ---------------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `kernel_address` | String | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>). |

## ExecuteMsg

### ReceiveNft

Receives a token from a [`SendNft`](cw721.md#sendnft)and locks it based on the parameters defined in the `StartLock` hook message.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  ReceiveNft(Cw721ReceiveMsg),
  }
```
{% endtab %}
{% endtabs %}

#### Cw721ReceiveMsg

```rust
pub struct Cw721ReceiveMsg {
    pub sender: String,
    pub token_id: String,
    pub msg: Binary,
}
```

The `msg` field in the `Cw721ReceiveMsg` struct should be a `Cw721HookMsg` of type `StartLock`.

#### Cw721HookMsg

{% hint style="warning" %}
The minimum `lock_time` that can be set is 86\_400 which is one day.

The maximum `lock_time` that can be set is 31\_536\_000 which is one year.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw721HookMsg {
    StartLock {
        recipient: Option<String>,
        lock_time: u64,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"start_lock":{
    "recipient":"andr1...",
    "lock_time": 100000
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                                                                                                                                  |
| ----------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `recipient` | Option\<String> | The address to receive the NFT after the lock is done. Defaults to the sender.                                                                                               |
| `lock_time` | u64             | The amount of time to lock the NFT. Should be provided in seconds. The minimum amount of time is 1 day (86400 seconds). The max amount of time is 1 year (31536000 seconds). |

### Claim

Transfers the given token to the recipient once the time lock has expired.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  Claim {
        lock_id: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim":{
    "lock_id": "andr1...tokenid"
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                                                  |
| --------- | ------ | ------------------------------------------------------------ |
| `lock_id` | String | A concatinated Id made of the nft contract address+token Id. |

### AndrReceive

The rest of the executes can be found in the [`AndrReceive`](../platform-and-framework/ado-base.md#andrrecieve) section.

## QueryMsg

### LockedToken

Queries details on the locked token with the specified `lock_id`.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
       #[returns(LockDetails)]
       LockedToken {
           lock_id: String 
       },
   }
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"locked_token":{
    "lock_id":"andr1...tokenid"
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                                                  |
| --------- | ------ | ------------------------------------------------------------ |
| `lock_id` | String | A concatinated Id made of the nft contract address+token Id. |

#### LockedDetails

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct LockDetails {
    pub recipient: String,
    pub expiration: Expiration,
    pub nft_id: String,
    pub nft_contract: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"recipient":"andr1...",
"expiration":{
    "at_time": "128193232"
    },
"nft_id": "3",
"nft_contract":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name           | Type                                                               | Description                                                      |
| -------------- | ------------------------------------------------------------------ | ---------------------------------------------------------------- |
| `recipient`    | String                                                             | The recipient of the NFT once the lock time has passed.          |
| `expiration`   | [Expiration](../platform-and-framework/common-types.md#expiration) | When the lock time will be completed and the NFT can be claimed. |
| `nft_id`       | String                                                             | The Id of the NFT.                                               |
| `nft_contract` | String                                                             | The address of the NFT contract.                                 |

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado-base.md#andrquery).
