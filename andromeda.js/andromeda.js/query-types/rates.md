---
description: The GraphQL queries that can be performed on the Rates ADO.
---

# Rates

### queryPayments

Query the rates used by the specified rates contract.

{% hint style="info" %}
Contract query can be found [here](../../../smart-contracts/modules/rates.md#payments).
{% endhint %}

```typescript
export async function queryPayments(
  contractAddress: string
): Promise<RateInfo[]> {
  const resp = await query<QueryRatesPayments, QueryRatesPaymentsResponse>(
    QUERY_RATES_PAYMENTS,
    { contractAddress }
  );

  return resp.payments;
}
```

| Name              | Type   | Description                                                 |
| ----------------- | ------ | ----------------------------------------------------------- |
| `contractAddress` | string | The contract address of the rates ADO to get the rates for. |

#### Response

```typescript
export interface QueryRatesPaymentsResponse {
  payments: RateInfo[];
}
```

| Name       | Type        | Description                                                                     |
| ---------- | ----------- | ------------------------------------------------------------------------------- |
| `payments` | RateInfo\[] | Vector of RateInfo containing information on the rates applied by the contract. |

#### RateInfo

```typescript
export interface RateInfo {
  description: string;
  is_additive: boolean;
  rate: Rate;
  receivers: Recipient[];
}
```

| Name          | Type                         | Description                                                                         |
| ------------- | ---------------------------- | ----------------------------------------------------------------------------------- |
| `description` | string                       | Description for the rate.                                                           |
| `is_additive` | boolean                      | An indicator to whether the rate being taken is tax. If tax, `is_additive` is true. |
| `rate`        | [Rate](rates.md#undefined)   | The type of rate being taken.                                                       |
| `receivers`   | [Recipient](./#recipient)\[] | The addresses to receive the `rate` specified.                                      |

#### Rate

```typescript
export interface Rate {
  external: ADORate;
  flat: Coin;
  percent: DecimalRate;
}
export interface ADORate {
  address: string;
  key: string;
}
export interface DecimalRate {
  decimal: number;
}
```

The Rate can be one of the three option seen above:

* Flat: A fixed amount to be taken ([Coin](crowdfund.md#coin)).&#x20;
* Percent: A percentage based rate.&#x20;
* External: This refers to a rate that is saved in a primitive contract.&#x20;
