# Splitter V1.0.0

## Introduction

The **Splitter** ADO is a smart contract used to split funds to a preset number of addresses. Each of the addresses has a specific percentage assigned by the contract owner. The splitter can be locked for a specified time as a kind of insurance for recipients that their percentages will not be changed for a certain period of time.

**Ado\_type**: splitter

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
    pub lock_time: Option<MillisecondsDuration>,
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
"kernel_address":"andr1...",
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="249.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipients</code></td><td>Vec&#x3C;<a href="splitter-v1.0.0.md#addresspercent">AddressPercent</a>></td><td>The recipient list of the splitter. Can be updated after instantiation if there is no current lock time.</td></tr><tr><td><code>lock_time</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#milliseconds">MillisecondsDuration</a>></td><td>How long the splitter is locked. When locked, no recipients can be added/changed. Specified in milliseconds.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">kernel contract</a> to be used for <a href="../platform-and-framework/andromeda-messaging-protocol/">AMP</a> messaging. Kernel contract address can be found in our <a href="../platform-and-framework/deployed-contracts (1).md">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

{% hint style="warning" %}
Anytime a [`Send`](splitter-v1.0.0.md#send) execute message is sent, the amount sent will be divided amongst the recipients depending on their assigned percentage.
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

| Name         | Type                                                     | Description                                 |
| ------------ | -------------------------------------------------------- | ------------------------------------------- |
| `recipients` | Vec<[AddressPercent](splitter-v1.0.0.md#addresspercent)> | The new list of addresses to receive funds. |

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

Divides any attached funds to the message amongst the recipients list.

{% hint style="warning" %}
You cannot send more than 5 coins with one Send.

Make sure to attach funds when executing a Send.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Send {}
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

<table><thead><tr><th width="275.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>config</code></td><td><a href="splitter-v1.0.0.md#splitter">Splitter</a></td><td>The Splitter config struct.</td></tr></tbody></table>

#### Splitter

The splitter's config is stored in a basic struct.

```rust
pub struct Splitter {
    pub recipients: Vec<AddressPercent>, 
    pub lock: MillisecondsExpiration,                   
}
```

<table><thead><tr><th>Name</th><th width="266.3333333333333">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipients</code></td><td>Vec&#x3C;<a href="splitter-v1.0.0.md#addresspercent">AdressPercent</a>></td><td>The vector of the assigned recipients to receive the funds along with their percentages. </td></tr><tr><td><code>lock</code></td><td><a href="../platform-and-framework/common-types.md#milliseconds">MillisecondsExpiration</a></td><td>Returns the timestamp in milliseconds of the end date for the lock.</td></tr></tbody></table>

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
