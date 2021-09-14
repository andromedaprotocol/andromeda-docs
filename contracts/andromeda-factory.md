---
description: The message definitions for the Andromeda Factory contract
---

# Andromeda Factory

### InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub name: String,
    pub symbol: String,
    pub minter: String,
    pub modules: Vec<ModuleDefinition>,
    pub init_hook: Option<InitHook>,
    pub metadata_limit: Option<u64>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "instantiate_msg": {
        "name": "Example Token",
        "symbol": "ET",
        "minter": "terra1...",
        "modules": [
            "whitelist": {
                "moderators": ["terra1..."]
            },
            "taxable": {
                "tax": 4,
                "receivers": ["terra1..."]
            }
        ],
        "metadata_limit": 10000
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| name | String | The name of the NFT |
| symbol | String | The symbol of the NFT |
| minter | String | The address of the token minter |
| modules | Vec&lt;ModuleDefinition&gt; | A vector of Andromeda Module definitions. The module definitions can be found here. |
| init\_hook | Option&lt;InitHook&gt; | An optional instantiation hook message, primarily used by the `andromeda_factory` contract |
| metadata\_limit | Option&lt;u64&gt; | An optional size limit on any metadata assigned to an NFT minted in the contract \(in bytes\) |

