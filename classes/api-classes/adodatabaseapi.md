# ADODatabaseAPI

## Class

API for ADODB specific messages.

```typescript
export default class ADODatabaseAPI extends ADOAPI {
  constructor(client: AndromedaClient, public address: string = "") {
    super(client, address);
  }
```

### getCodeIdQuery

Provides a message object for the factory's `GetCodeId` query.

```typescript
  getCodeIdQuery(name: string) {
    return {
      code_id: {
        key: name,
      },
    };
  }
```

<table><thead><tr><th>Name</th><th width="83">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>name</code></td><td>string</td><td>The key to get the code Id for.</td></tr></tbody></table>

### getCodeId

Gets the code ID for an ADO type from the ADO DB&#x20;

```typescript
 async getCodeId(name: string, address?: string) {
    if (!this.address && !address)
      throw new Error("No provided ADO DB address to retrieve code ID");

    const msg = this.getCodeIdQuery(name);

    return this.client.queryContract<number>(address ?? this.address, msg);
  }
```

| Name      | Type   | Description                     |
| --------- | ------ | ------------------------------- |
| `name`    | string | The key to get the code Id for. |
| `address` | string | The factory address.            |

### getADOType

Retrieves the type of ADO from the provided code ID and ADO DB contract address.

```typescript
async getAdoType(codeId: number, address?: string) {
    if (!this.address && !address)
      throw new Error("No provided ADO DB address to retrieve code ID");

    const msg = this.getAdoTypeQuery(codeId);

    return this.client.queryContract<string>(address ?? this.address, msg);
  }
```

| Name      | Type   | Description                |
| --------- | ------ | -------------------------- |
| `codeId`  | number | The code Id.               |
| `address` | string | The address of the ADO DB. |

### getAllADOQuery

Provides a message object for the ADO DB's 'getAllADO' query.

```typescript
  getAllADOQuery(startAfter = '', limit = 100) {
    return {
      all_ado_types: {
        limit,
        start_after: startAfter
      },
    };
  }
```

| Name         | Type   | Description                                                                |
| ------------ | ------ | -------------------------------------------------------------------------- |
| `startAfter` | string | The ADO to start after. Defaults to empty meaning all ADOs will be fetched |
| `limit`      | string | Limit to the number of fetched ADOs. Defaults to 100.                      |

### getAllADO

```typescript
  async getAllADO(startAfter = '', limit = 100, address?: string) {
    if (!this.address && !address)
      throw new Error("No provided ADO DB address to retrieve code ID");

    const msg = this.getAllADOQuery(startAfter, limit);

    return this.client.queryContract<string[]>(address ?? this.address, msg);
  }
}
```

| Name         | Type   | Description                                                                |
| ------------ | ------ | -------------------------------------------------------------------------- |
| `startAfter` | string | The ADO to start after. Defaults to empty meaning all ADOs will be fetched |
| `limit`      | string | Limit to the number of fetched ADOs. Defaults to 100.                      |
| `address`    | string | The contract address of the ADODB.                                         |
