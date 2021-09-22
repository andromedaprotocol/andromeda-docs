---
description: A module used to append a tax payment to any agreed transfer.
---

# Tax

This module defines a tax payment that will be attached to any **agreed** ADO transfers undertaken within the contract. **This payment is not required when the owner of the ADO is executing a transfer and is only required on an agreed transfer.** **Any rounding caused by the module favours the tax receiver.**

Any payments generated by the module will be recorded in the response event and are **added to the agreed transfer amount**. A separate payment will be generated for each receiver and will be the same amount for each receiver and not divided amongst them.

## Definition

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Taxable {
    pub rate: Rate,
    pub receivers: Vec<String>,
}

```
{% endtab %}

{% tab title="Javascript" %}
```javascript
{
    "taxable": {
        "tax": 2,
        "receivers": ["terra1...", "terra1..."],
        "description": "Some tax payment to be made to..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| rate | Rate | The amount to be paid to each receiver upon an agreed transfer. |
| receivers | Vec&lt;String&gt; | An address list of recipients for the royalty fee |

## Methods

### `calculate_fee`

Used to calculate the required payment for a given amount. **Returns a `Coin` fee amount.**

```rust
pub fn calculate_fee(&self, payment: Coin) -> Coin {
    let fee_amount = payment
        .amount
        .multiply_ratio(Uint128::from(self.fee), 100 as u128);

    coin(fee_amount.u128(), payment.denom)
}
```

| Name | Type | Description |
| :--- | :--- | :--- |
| payment | Coin | The amount to calculate the fee against |

## Hooks

The Tax module implements the following hooks:

### `on_agreed_transfer`

Used to append the required royalty payments to the agreed transfer payments and deduct the funds from the payment being made to the seller.

```rust
fn on_agreed_transfer(
    &self,
    deps: &DepsMut,
    info: MessageInfo,
    env: Env,
    payments: &mut Vec<BankMsg>,
    owner: String,
    purchaser: String,
    amount: Coin,
) -> StdResult<bool> {
    let fee_payment = self.calculate_fee(amount);
    for receiver in self.receivers.to_vec() {
        deduct_payment(payments, owner.clone(), fee_payment.clone())?;
        add_payment(payments, receiver, fee_payment.clone());
    }

    Ok(true)
}
```

## Validation

The Tax module does not have any validation requirements.
