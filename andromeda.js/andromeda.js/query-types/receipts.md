---
description: The GraphQL queries that can be performed on the Receipts ADO.
---

# Receipts

Query the minter of the receipt contract.

{% hint style="info" %}
Contract query can be found [here](../../../smart-contracts/modules/receipt-contract.md#contractinfo).
{% endhint %}

```typescript
export async function queryMinter(contractAddress: string): Promise<string> {
  const resp = await query<QueryReceiptMinter, QueryReceiptMinterResponse>(
    QUERY_RECEIPT_MINTER,
    { contractAddress }
  );

  return resp.minter;
}
```

| Name              | Type   | Description                                                               |
| ----------------- | ------ | ------------------------------------------------------------------------- |
| `contractAddress` | string | The contract address of the receipts ADO we want to query the minter for. |

#### Response

```typescript
export interface QueryReceiptMinterResponse {
  minter: string;
}
```

| Name     | Type   | Description                                 |
| -------- | ------ | ------------------------------------------- |
| `minter` | string | The address authorized to mint new receipts |
