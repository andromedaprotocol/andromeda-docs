# Splitter

## Introduction

The **Splitter** ADO is a smart contract used to split funds to a preset number of addresses. Each of the addresses has a specific percentage assigned by the contract owner. The splitter can be locked for a specified time as a kind of insurance for recipients that their percentages will not be changed for a certain period of time.

{% hint style="info" %}
We also have a [set amount splitter](fixed-amount-splitter.md), [weighted distribution splitter](weighted-distribution-splitter.md), and [conditional splitter](conditional-splitter.md).
{% endhint %}



**Ado\_type**: splitter

**Version: 2.2.0-beta.1**

## InstantiateMsg

{% hint style="warning" %}
A maximum of 100 recipients can be set.&#x20;

The recipient addresses need to be unique.

The minimum lock\_time that can be set is 1 day.

The maximum lock\_time that can be set is 1 year.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub recipients: Vec<AddressPercent>,
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
    "recipients": [
               {
                "recipient":{
                    "address":"andr1..."
                },
                "percent":"0.2"
     },
     ...
    ],
"default_recipient":{
        "address":"andr1..."
        },
"kernel_address":"andr1...",
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="249.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipients</code></td><td>Vec&#x3C;<a href="splitter.md#addresspercent">AddressPercent</a>></td><td>The recipient list of the splitter. Can be updated after instantiation if there is no current lock time.</td></tr><tr><td><code>lock_time</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#expiry">Expiry</a>></td><td>How long the splitter is locked. When locked, no recipients can be added/changed.</td></tr><tr><td><code>default_recipient</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#recipient">Recipient</a>></td><td>An optional recipient to receive any leftover funds in case the split is not exactly distributed. For example if a user sets 40% to one user, and 50% to another, and forgets about the last 10%, they would go this default recipient. Defaults to the sender if not specified.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">kernel contract</a> to be used for <a href="../platform-and-framework/andromeda-messaging-protocol/">AMP</a> messaging. Kernel contract address can be found in our <a href="../platform-and-framework/deployed-contracts.md">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

{% hint style="warning" %}
Anytime a [`Send`](splitter.md#send) execute message is sent, the amount sent will be divided amongst the recipients depending on their assigned percentage.
{% endhint %}

#### AddressPercent

The splitter uses a basic array of structs to determine recipients and how the funds are divided.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AddressPercent {
    pub recipient: Recipient,
    pub percent: Decimal,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "recipient":{
        "address":"andr1..."
     },
    "percent": "0.5"
}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
To be a valid recipient list the array of `AddressPercent` structs must meet the following requirements:

* Be non-empty
* Have percentage amounts less than or equaling 1
{% endhint %}

Read more about the Recipient struct [here](../platform-and-framework/common-types.md#recipient).

## ExecuteMsg

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
        recipients: Vec<AddressPercent> 
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_recipients": {
        "recipients": [
            {
                "recipient":{
                    "address":"andr1..."
                },
                "percent": "0.5"
            },
            ...
        ]
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                              | Description                                 |
| ------------ | ------------------------------------------------- | ------------------------------------------- |
| `recipients` | Vec<[AddressPercent](splitter.md#addresspercent)> | The new list of addresses to receive funds. |

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
        lock_time: MillisecondsDuration,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_lock": {
        "lock_time": 200000
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                                                                           | Description                                                                       |
| ----------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------- |
| `lock_time` | [MillisecondsDuration](../platform-and-framework/common-types.md#milliseconds) | How long the splitter is locked. When locked, no recipients can be added/changed. |

### **Send**

Divides any attached funds to the message amongst the recipients list.&#x20;

{% hint style="warning" %}
You cannot send more than 5 coins with one Send.

Make sure to attach funds when executing a Send.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Send {
    config: Option<Vec<AddressPercent>>,
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

<table><thead><tr><th>Name</th><th width="268">Type </th><th>Description</th></tr></thead><tbody><tr><td><code>config</code></td><td>Option&#x3C;Vec&#x3C;<a href="splitter.md#addresspercent">AddressPercent</a>>></td><td>An optional set of recipients to split the funds to. If not defined, then the default recipient list (List defined at instantiation) will be used.</td></tr></tbody></table>

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

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

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
                "percent": "0.5"
            },
            ...
        ],
        "locked": {
            "at_time": "1655212973"
        }
            
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="275.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>config</code></td><td><a href="splitter.md#splitter">Splitter</a></td><td>The Splitter config struct.</td></tr></tbody></table>

#### Splitter

The splitter's config is stored in a basic struct.

```rust
pub struct Splitter {
    pub recipients: Vec<AddressPercent>, 
    pub lock: MillisecondsExpiration,                   
}
```

<table><thead><tr><th>Name</th><th width="266.3333333333333">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipients</code></td><td>Vec&#x3C;<a href="splitter.md#addresspercent">AdressPercent</a>></td><td>The vector of the assigned recipients to receive the funds along with their percentages. </td></tr><tr><td><code>lock</code></td><td><a href="../platform-and-framework/common-types.md#milliseconds">MillisecondsExpiration</a></td><td>Returns the timestamp in milliseconds of the end date for the lock.</td></tr></tbody></table>

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
