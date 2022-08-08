# Query Types

Andromeda has a large variety of queries, each of which require a speific set of parameters.In this section we will go over some of the interfaces in Andromeda.js to set up for these queries.

### ContractAddressQuery

Queries that require the contract address to be specified.

```typescript
export interface ContractAddressQuery {
  contractAddress: string;
}
```

| Name              | Type   | Description                    |
| ----------------- | ------ | ------------------------------ |
| `contractAddress` | string | The contract address to query. |

### AndrSearchOptions

Options to setup pagination for queries.

```typescript
export interface AndrSearchOptions {
  limit?: number;
  orderBy?: "Asc" | "Desc";
  startAfter?: number;
}
```

| Name          | Type   | Description                                                                                                                                                |
| ------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `limit?`      | number | Optional limit to the number of results for a query. For example if I query user accounts and set the limit to 10, then 10 user accounts would be queried. |
| `orderBy?`    | string | Optional way to order the results. Can be set to ascending order "Asc" or descending order "Desc".                                                         |
| `startAfter?` | number | Optional id to start after. Used for pagination.                                                                                                           |

### PaginatedQuery

Queries that have optional pagination.

```typescript
export interface PaginatedQuery {
  options?: AndrSearchOptions;
}
```

| Name       | Type                                      | Description                                                     |
| ---------- | ----------------------------------------- | --------------------------------------------------------------- |
| `options?` | [AndrSearchOptions](./#andrsearchoptions) | Optional parameters for the query that has optional pagination. |

### PaginatedRequiredQuery

Queries that have required paginations.

```typescript
export interface PaginatedRequiredQuery {
  options: AndrSearchOptions;
}
```

| Name      | Type                                      | Description                                        |
| --------- | ----------------------------------------- | -------------------------------------------------- |
| `options` | [AndrSearchOptions](./#andrsearchoptions) | Parameters for the query that requires pagination. |

### Expiry

Queries that use[ Expiration ](../../../common-types/expiration.md)(block height or block time).

```typescript
export interface Expiry {
  at_time?: number;
  at_height?: number;
}
```

| Name         | Type   | Description                                |
| ------------ | ------ | ------------------------------------------ |
| `at_time?`   | number | The timestamp to be used in the query.     |
| `at_height?` | number | The block\_height to be used in the query. |

### AndrAddress

Queries that require an [AndrAddress](../../../common-types/recipient.md#andraddress).

```typescript
export interface AndrAddress {
  identifier: string;
}
```

| Name         | Type   | Description                                                 |
| ------------ | ------ | ----------------------------------------------------------- |
| `identifier` | string | Can be either an address or identifier of an ADO in an app. |

### RecipientADO

Queries with [ADO recipients.](../../../common-types/recipient.md#adorecipient)

```typescript
export interface RecipientADO {
  a_d_o: {
    address: AndrAddress;
    msg?: string;
  };
}
```

| Name      | Type                          | Description                                        |
| --------- | ----------------------------- | -------------------------------------------------- |
| `address` | [AndrAddress](./#andraddress) | The anddress of the ado defined using AndrAddress. |
| `msg?`    | string                        | Optional message to attach to the ADO.             |

### Recipient

Type that contains all recipient options which are either ADOs or Addresses.

```typescript
export type Recipient = RecipientADO | RecipientAddress;
```
