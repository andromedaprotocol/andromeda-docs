# Coin

A struct used to store the denom and amount of funds.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct Coin {
    pub denom: String,
    pub amount: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"denom":"ujuno",
"amount":"1000000"
}
```
{% endtab %}
{% endtabs %}

| Name     | Type    | Description                    |
| -------- | ------- | ------------------------------ |
| `denom`  | String  | The denomination of the funds. |
| `amount` | Uint128 | The amount of funds.           |
