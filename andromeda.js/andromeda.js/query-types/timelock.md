---
description: The GraphQL queries that can be performed on the Timelock ADO.
---

# Timelock

### queryRecipientLockedFunds

Query the locked funds for the specified recipient.

{% hint style="info" %}
Contract query can be found [here](../../../smart-contracts/finance/timelock.md#getlockedfundsforrecipient).
{% endhint %}

```typescript
export async function queryRecipientLockedFunds(
  contractAddress: string,
  recipient: string,
  options: AndrSearchOptions
): Promise<Escrow[]> {
  const resp = await query<
    QueryTimelockRecipientLockedFunds,
    QueryTimelockRecipientLockedFundsResponse
  >(QUERY_TIMELOCK_RECIPIENT_LOCKED_FUNDS, {
    contractAddress,
    options,
    recipient,
  });

  return resp.getLockedFundsForRecipient;
}

```

| Name              | Type                                      | Description                                                            |
| ----------------- | ----------------------------------------- | ---------------------------------------------------------------------- |
| `contractAddress` | string                                    | The contract address of the  timelock we want to query.                |
| `recipient`       | string                                    | The address of the recipient of funds.                                 |
| `options?`        | [AndrSearchOptions](./#andrsearchoptions) | Optional additional options for the query. Mostly used for pagination. |

#### Response

```typescript
export interface QueryTimelockRecipientLockedFundsResponse {
  getLockedFundsForRecipient: Escrow[];
}
```

| Name                         | Type      | Description                                         |
| ---------------------------- | --------- | --------------------------------------------------- |
| `getLockedFundsForRecipient` | Escrow\[] | Escrow with the held funds and related information. |

#### Escrow

```typescript
export interface Escrow {
  coins: Coin[];
  condition: EscrowCondition;
  recipient: Recipient;
}
```

| Name        | Type                         | Description                                                                      |
| ----------- | ---------------------------- | -------------------------------------------------------------------------------- |
| `coins`     | [Coin](crowdfund.md#coin)\[] | Funds being held within the Escrow.                                              |
| `condition` | EscrowCondition              | The condition to release the funds. Can be either a set time or amount of funds. |
| `recipient` | [Recipient](./#recipient)    | The recipients of the locked funds                                               |
