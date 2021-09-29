---
description: An ADO contract to split sent funds amongst predefined addresses.
---

# Andromeda Splitter

### InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub use_whitelist: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "use_whitelist": true
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| use\_whitelist | bool | Whether or not to use a whitelist for interacting with the splitter. |

### ExecuteMsg

#### UpdateRecipients

Updates the recipients of the splitter contract.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    UpdateRecipient {
        recipient: Vec<AddressPercent>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_recipient": {
        "recipient": ["terra1...", "terra1..."]
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| recipient | Vec&lt;String&gt; | The new list of recipient addresses |

#### UpdateRecipients

Sets a lock on the contract, disallowing editing of any other config fields.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    UpdateLock {
        lock: bool,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_lock": {
        "lock": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| lock | bool | Whether the contract should be locked. |

#### UpdateUseWhitelist

Updates the `use_whitelist` config field.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    UpdateUseWhitelist {
        use_whitelist: bool,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_use_whitelist": {
        "use_whitelist": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| use\_whitelist | bool | Whether the contract should use a whitelist or not. |

**UpdateTokenList**

Updates the `token_list` config field which defines which tokens the splitter accepts.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    UpdateTokenList {
        accepted_tokenlist: Vec<TokenId>
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_use_whitelist": {
        "use_whitelist": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| accepted\_tokenlist | Vec&lt;String&gt; | Whether the contract should use a whitelist or not. |

**UpdateSenderWhitelist**

Updates the whitelist for the splitter.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    UpdateSenderWhitelist {
        sender_whitelist: Vec<String>
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_use_whitelist": {
        "use_whitelist": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| sender\_whitelist | Vec&lt;String&gt; | The new vector of whitelisted addresses. |

**Send**

Send funds to the contract to be split amongst the recipients. 

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    Send {}
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_use_whitelist": {
        "use_whitelist": true
    }
}
```
{% endtab %}
{% endtabs %}

### QueryMsg

#### GetSplitterConfig

Query the address of a given symbol.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    GetSplitterConfig{},
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "get_splitter_config": {
    }
}
```
{% endtab %}
{% endtabs %}

#### GetConfigResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Splitter {
    pub recipient: Vec<AddressPercent>,   //Map for address and percentage
    pub is_lock: bool,                     //Lock
    pub use_whitelist: bool,               //Use whitelist
    pub sender_whitelist: Whitelist,       //Address list allowing to receive funds
    pub accepted_tokenlist: Vec<TokenId>,  //Token list allowing to accept
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct AddressResponse {
    pub config: Splitter
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "config": {
        "recipient": ["terra1...", "terra1..."],
        "is_lock": true,
        "use_whitelist": true,
        "sender_whitelist": {
            "moderators": ["terra1...", "terra1..."],
        },
        "accepted_tokenlist": ["terra1..."],
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| config | Splitter | The Splitter config struct |

#### IsWhitelisted

Query the address of a given symbol.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
   IsWhitelisted { address: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "is_whitelisted": {
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| address | String | The address for which to check the whitelist status |

#### IsWhitelisted

#### IsWhitelistedResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct IsWhitelistedResponse {
    pub whitelisted: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "whitelisted": true
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| whitelisted | bool | Whether the address is whitelisted |

