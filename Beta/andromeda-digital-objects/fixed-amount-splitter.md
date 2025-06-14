# Fixed Amount Splitter

## Introduction

The **Fixed Amount Splitter** ADO is a smart contract used to split funds to a preset number of addresses. Each of the addresses has a specific amount assigned by the contract owner. Once a [**Send**](fixed-amount-splitter.md#send) message is triggered, the attached funds are distributed to the recipients based on the assigned amounts.

The splitter can be locked for a specified time as a kind of insurance for recipients that their amounts will not be changed for a certain period of time.

{% hint style="info" %}
Splitter works with both native and CW20 tokens.&#x20;

We also have a [percentage based spliiter](https://docs.andromedaprotocol.io/andromeda/andromeda-beta-ados-1/andromeda-digital-objects/splitter), [weighted distribution splitter](https://docs.andromedaprotocol.io/andromeda/andromeda-beta-ados-1/andromeda-digital-objects/weighted-distribution-splitter), and [conditional splitter](https://docs.andromedaprotocol.io/andromeda/andromeda-beta-ados-1/andromeda-digital-objects/conditional-splitter).
{% endhint %}

#### Splitting CW20 Tokens

To setup a CW20 split, you need to define the denom of the [Coin](../platform-and-framework/common-types.md#coin) as the contract address of the CW20 ADO whose token you are splitting. In case this address is not present yet (You are creating the CW20 ADO and Splitter ADO at the same time), you need to:

1. Initialized the ADO with any recipient list.
2. Update the recipient list by calling UpdateRecipients.
3. Specify the coin denom field as the CW20 contract address.&#x20;

#### Example Uses

Let’s say you’re running a small team and want to pay monthly salaries. You can set up the **Fixed Amount Splitter** once, assigning fixed amounts to each team member’s address (e.g., Alice: 2,000 USDC, Bob: 3,000 USDC, etc.). Then at the end of each month, you simply trigger a single Send, and everyone receives their salary in one transaction.

This ADO is also ideal for managing fixed recurring costs. For example, if your application needs to pay fixed amounts monthly (e.g., hosting, data, analytics tools), you can configure it once and distribute those payments on demand with just one click.

**Ado\_type**: fixed-amount-splitter

**Version: 1.2.0**

## InstantiateMsg

{% hint style="warning" %}
A maximum of 100 recipients can be set.&#x20;

The recipient addresses need to be unique.

A maximum of 2 Coin types can be used.&#x20;

The minimum lock\_time that can be set is 1 day.

The maximum lock\_time that can be set is 1 year.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub recipients: Vec<AddressAmount>,
    pub lock_time: Option<Expiry>,
    pub default_recipient: Option<Recipient>,
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
"recipients":[
                {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "coins":[{"denom":"uandr","amount":"10000"}]
                  },
                  {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "coins":[{"denom":"uandr","amount":"20000"}]
                  },
                  {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "coins":[{"denom":"uandr","amount":"60000"}]
                  }
              ],
"lock_time":{
      "from_now": 100000000
      },
"default_recipient":"andr1...",
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="249.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipients</code></td><td>Vec&#x3C;<a href="fixed-amount-splitter.md#addressamount">AddressAmount</a>></td><td>The recipient list of the splitter. Can be updated after instantiation if there is no current lock time.</td></tr><tr><td><code>lock_time</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#expiry">Expiry</a>></td><td>How long the splitter is locked. When locked, no recipients can be added/changed.</td></tr><tr><td><code>default_recipient</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#recipient">Recipient</a>></td><td>An optional recipient to receive any leftover funds in case the split is not exactly distributed or there are any leftover funds. Defaults to the sender if not specified.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the kernel contract to be used for AMP messaging. Kernel contract address can be found in our deployed contracts.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

{% hint style="warning" %}
Anytime a [`Send`](fixed-amount-splitter.md#send) execute message is sent, the amount sent will be divided amongst the recipients depending on their assigned amounts.
{% endhint %}

#### AddressAmount

The splitter uses a basic array of structs to determine recipients and how the funds are divided.&#x20;

{% hint style="warning" %}
For the [Coin](../platform-and-framework/common-types.md#coin) struct, use the contract address as the denom in case you are using CW20 tokens.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub struct AddressAmount {
    pub recipient: Recipient,
<strong>    pub coins: Vec&#x3C;Coin>,
</strong>}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "recipient":{
        "address":"andr1..."
     },
    "coins":[{"denom":"uandr","amount":"10000"}]
}
```
{% endtab %}
{% endtabs %}

Read more about the Recipient struct [here](../platform-and-framework/common-types.md#recipient).

## ExecuteMsg

### Receive

Receives CW20 tokens sent from a CW20 ADO by performing a  [Send](cw20.md#send). These tokens are then automatically split to the defined recipients of the splitter.

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

The `msg` in the `Cw20ReceiveMsg`should be a base64 encoded `Cw20HookMsg`.

### Cw20HookMsg

```rust
pub enum Cw20HookMsg {
    Send { config: Option<Vec<AddressAmount>> },
}
```

### Send (CW20)

Distributes sent CW20 funds amongst the recipients list based on their assigned amounts.

{% hint style="warning" %}
Make sure you are sending CW20 tokens from the CW20 ADO defined in the denom field of the [Coin](../platform-and-framework/common-types.md#coin) struct.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw20HookMsg {
    Send {
         config: Option<Vec<AddressAmount>> 
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
"send": {}
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>Name</th><th width="268">Type </th><th>Description</th></tr></thead><tbody><tr><td><code>config</code></td><td>Option&#x3C;Vec&#x3C;<a href="fixed-amount-splitter.md#addressamount">AddressAmount</a>>></td><td>An optional set of recipients/amounts to use. If not defined, then the default configuration will be used.</td></tr></tbody></table>

***

### UpdateRecipients

Updates the recipients of the splitter contract. Only executable by the contract owner when the contract is not locked.

{% hint style="warning" %}
Only available to the contract owner when the contract is not locked.

A maximum of 100 recipients can be set.&#x20;

The recipients should be unique.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateRecipients { 
        recipients: Vec<AddressAmount> 
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_recipients": {
        "recipients":[
                {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "coins":[{"denom":"uandr","amount":"10000"}]
                  },
                  {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "coins":[{"denom":"uandr","amount":"20000"}]
                  },
                  {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "coins":[{"denom":"uandr","amount":"60000"}]
                  }
              ],
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                         | Description                                 |
| ------------ | ------------------------------------------------------------ | ------------------------------------------- |
| `recipients` | Vec<[AddressAmount](fixed-amount-splitter.md#addressamount)> | The new list of addresses to receive funds. |

### UpdateLock

Used to lock the contract for a certain period of time making it unmodifiable in any way. This can serve as a way to ensure for recipients that their weights from the splitter are fixed for a certain amount of time. The time is calculated in seconds.

{% hint style="warning" %}
Only available to the contract owner when the contract is not already locked.

The minimum time that can be set is 86,400 which is 1 day.

The maximum time that can be set is 31,536,000 which is 1 year.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateLock {
        lock_time: Expiry,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_lock": {
        "lock_time":{
            "from_now": 6000000
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                                                                       |
| ----------- | ------ | --------------------------------------------------------------------------------- |
| `lock_time` | Expiry | How long the splitter is locked. When locked, no recipients can be added/changed. |

### **Send (Native)**

Divides any attached funds to the message amongst the recipients list based on the assigned amounts.

{% hint style="warning" %}
Make sure to attach funds when executing a Send.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Send {
        config: Option<Vec<AddressAmount>>   
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
"send": {}
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>Name</th><th width="268">Type </th><th>Description</th></tr></thead><tbody><tr><td><code>config</code></td><td>Option&#x3C;Vec&#x3C;<a href="fixed-amount-splitter.md#addressamount">AddressAmount</a>>></td><td>An optional set of recipients/amounts to use. If not defined, then the default configuration (List defined at instantiation) will be used.</td></tr></tbody></table>

### UpdateDefaultRecipient

Updates the set default recipient.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  UpdateDefaultRecipient {
        recipient: Option<Recipient>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_default_recipient": {
        "recipient":{
            "address":"andr1..."
            }
        }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                                                                     | Description                                                                                                                                                                                                                                                                  |
| ----------- | ------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `recipient` | Option<[Recipient](../platform-and-framework/common-types.md#recipient)> | The new recipient to receive any leftover funds in case the split is not exactly distributed. For example if a user sets 40% to one user, and 50% to another, and forgets about the last 10%, they would go this default recipient. Defaults to the sender if not specified. |

### Base Executes

The rest of the execute messages can be found in the ADO Base section.

## QueryMsg

### GetSplitterConfig

The current config of the Splitter contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(GetSplitterConfigResponse)]
    GetSplitterConfig {},
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
"get_splitter_config": {}
}
```
{% endtab %}
{% endtabs %}

#### GetSplitterConfigResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct GetSplitterConfigResponse {
    pub config: Splitter,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "config": {
        "recipients": [
            {
                "recipient":{
                   "addr":"andr1..."
                    },
                "coin":[{"denom":"uandr","amount":"100000"}]
            },
            ...
        ],
        "lock": 12384395739430    
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="275.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>config</code></td><td><a href="fixed-amount-splitter.md#splitter">Splitter</a></td><td>The Splitter config struct.</td></tr></tbody></table>

#### Splitter

The splitter's config is stored in a basic struct.

```rust
pub struct Splitter {
    pub recipients: Vec<AddressAmount>,
    pub lock: MillisecondsExpiration,
}
```

<table><thead><tr><th>Name</th><th width="266.3333333333333">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipients</code></td><td>Vec&#x3C;<a href="fixed-amount-splitter.md#addressamount">AdressAmount</a>></td><td>The vector of the assigned recipients to receive the funds along with their amounts. </td></tr><tr><td><code>lock</code></td><td>MillisecondsExpiration</td><td>Returns the timestamp in milliseconds of the end date for the lock.</td></tr></tbody></table>

### Base Queries

The rest of the query messages can be found in the ADO Base section.
