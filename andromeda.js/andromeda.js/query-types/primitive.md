---
description: The GraphQL queries that can be performed on the Primitive ADO.
---

# Primitive

### queryPrimitiveValue

Query the value stored for the specified key.

{% hint style="info" %}
Contract query can be found [here](../../../smart-contracts/data-storage/primitive.md#querymsg).
{% endhint %}

```typescript
export async function queryPrimitiveValue(
  contractAddress: string,
  key: string
) {
  return query<QueryPrimitiveValue, QueryPrimitiveValueResponse>(
    QUERY_PRIMITIVE_VALUE,
    {
      contractAddress,
      key,
    }
  );
}
```

| Name              | Type   | Description                                         |
| ----------------- | ------ | --------------------------------------------------- |
| `contractAddress` | string | The contract address of the primitive ADO to query. |
| `key`             | string | The key we want to get the value for.               |

#### Response

```typescript
export interface QueryPrimitiveValueResponse {
  getValue: PrimitiveResponse;
}
```

| Name       | Type                                        | Description             |
| ---------- | ------------------------------------------- | ----------------------- |
| `getValue` | [PrimitiveResponse](primitive.md#tokeninfo) | The key and value pair. |

#### PrimitiveResponse

```typescript
export interface PrimitiveResponse {
  key: string;
  value: PrimitiveValue;
}
```

| Name    | Type                                          | Description |
| ------- | --------------------------------------------- | ----------- |
| `key`   | string                                        | The key.    |
| `value` | [PrimitiveValue](primitive.md#primitivevalue) | The value.  |

#### PrimitiveValue

```typescript
export type PrimitiveValue =
  | StringPrimitive
  | Uint128Primitive
  | DecimalPrimitive
  | CoinPrimitive
  | BoolPrimitive
  | VecPrimitive;
```

Return the appropriate value based on its type.
