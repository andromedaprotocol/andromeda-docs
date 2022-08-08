---
description: The GraphQL queries that can be performed on the Vault ADO.
---

# Vault

### queryBalance

{% hint style="info" %}
Contract query can be found [here](../../../smart-contracts/ecosystem/vault.md#balance).
{% endhint %}

```typescript
export async function queryBalance(
  contractAddress: string,
  address: string
): Promise<Coin[]> {
  const resp = await query<QueryVaultBalance, QueryVaultBalanceResponse>(
    QUERY_VAULT_BALANCE,
    { contractAddress, address }
  );

  return resp.balance;
}
```

| Name             | Type   | Description                                          |
| ---------------- | ------ | ---------------------------------------------------- |
| `contactAddress` | string | The contract address of the vault contract to query. |
| `address`        | string | The address to check the balance for.                |

#### Response

```typescript
export interface QueryVaultBalanceResponse {
  balance: Coin[];
}
```

| Name      | Type                         | Description                                         |
| --------- | ---------------------------- | --------------------------------------------------- |
| `balance` | [Coin](crowdfund.md#coin)\[] | The balance of the address that has been specified. |
