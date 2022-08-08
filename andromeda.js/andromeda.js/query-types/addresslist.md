---
description: The GraphQL queries that can be performed on the AddressList ADO.
---

# AddressList

### queryAddressListIncludesAddress

Queries if the specified address is included in the contract's AddressList.&#x20;

{% hint style="info" %}
You can find the contract query [here](../../../smart-contracts/modules/address-list.md#includesaddress).
{% endhint %}

```typescript
export async function queryAddressListIncludesAddress(
  contractAddress: string,
  address: string
): Promise<boolean> {
  const resp = await query<
    QueryAddressListIncludesAddress,
    QueryAddressListIncludesAddressResponse
  >(QUERY_ADDRESS_LIST_CONTAINS_ADDRESS, { address, contractAddress });

  return resp.includesAddress.included;
}
```

| Name              | Type   | Description                                                         |
| ----------------- | ------ | ------------------------------------------------------------------- |
| `contractAddress` | string | The contract address to query.                                      |
| `address`         | string | The address to check. Returns true if included and false otherwise. |

#### Response

```typescript
export interface QueryAddressListIncludesAddressResponse {
  includesAddress: {
    included: boolean;
  };
}
```

| Name       | Type    | Description                             |
| ---------- | ------- | --------------------------------------- |
| `included` | boolean | Whether the address is included or not. |
