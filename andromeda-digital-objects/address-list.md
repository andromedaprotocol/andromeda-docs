# Address List

## Introduction

The **Address List** ADO is a smart contract that can be used to store a list of addresses. It is one of the Andromeda [modules](../modules/module-definitions.md) that can be attached to ADOs that accept modules.&#x20;

These addresses can be set as one of the following:

* **Whitelist:** The addresses are given whitelist privilege, allowing them to  interact/execute with the ADO that has the module attached . If a whitelist of addresses is set, then any other address is considered as a blacklist.
* **Blacklist:** The addresses are given blacklist restriction, preventing them to interact/execute with the ADO that has the module attached. If a blacklist of addresses is set, then any other address is considered as a whitelist.

Blacklisted addresses are restricted from performing any execute message, but they can still query from the ADO.&#x20;

**Ado\_type:** address-list

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub is_inclusive: bool,
    pub kernel_address: String,
    pub owner: Option<String>

}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
"is_inclusive": false,
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="234.54210587473648">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>is_inclusive</code></td><td>bool</td><td>Whether or not the address list is inclusive. If <code>true</code> the address list is a whitelist, if false the address list is a blacklist.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">kernel contract</a> to be used for <a href="../platform-and-framework/andromeda-messaging-protocol/">AMP</a> messaging. Kernel contract address can be found in our <a href="../platform-and-framework/deployed-contracts (1).md">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

## ExecuteMsg

### AddAddress

Adds an address to the address list.

{% hint style="warning" %}
Only Owner/Operators can execute AddAddress.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    AddAddress { address: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "add_address": {
        "address": "andr1..."
    }    
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="155.33333333333331">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>address</code></td><td>String</td><td>The address to add to the list.</td></tr></tbody></table>

### RemoveAddress

Removes an address from the address list.

{% hint style="warning" %}
Only Owner/Operators can execute RemoveAddress.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    RemoveAddress { address: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "remove_address": {
        "address": "andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="155.33333333333331">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>address</code></td><td>String</td><td>The address to remove from the list.</td></tr></tbody></table>

### AddAddresses

Add multiple addresses to the address list.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    AddAddresses { addresses: Vec<String> },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_addresses":{
    "addresses":["andr1...","andr1...",...]
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="155.33333333333331">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>addresses</code></td><td>Vec&#x3C;String></td><td>A vector containing all the addresses to add to the address list.</td></tr></tbody></table>

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base.md) section.

## QueryMsg

### IncludesAddress

Checks if an address is included in the address list.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(IncludesAddressResponse)]
    IncludesAddress{
        address: String
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "includes_address": {
        "address": "andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                               |
| --------- | ------ | ----------------------------------------- |
| `address` | String | The address for which to query inclusion. |

#### IncludesAddressResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct IncludesAddressResponse {
    pub included: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
"included": false
}
```
{% endtab %}
{% endtabs %}

| Name       | Type | Description                      |
| ---------- | ---- | -------------------------------- |
| `included` | Bool | Whether the address is included. |

### IsInclusive

Checks if the address list is a whitelist or a blacklist.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(bool)]
    IsInclusive {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"is_inclusive":{}
}
```
{% endtab %}
{% endtabs %}

Returns a bool with the result. If true, the address list is a whitelist. Otherwise the address list is a blacklist.

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base.md) section.
