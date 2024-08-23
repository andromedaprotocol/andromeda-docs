# ADOSchemaAPI

Schema base URL:

```typescript
'https://api.andromedaprotocol.io/v1/schema'
```

## Class

```typescript
export default class ADOSchemaAPI extends ADOAPI {
  constructor(client: AndromedaClient, public address: string = "") {
    super(client, address);
  }
```

### getSchemaFromVersion

Gets the schema for the key in the schemadb

{% code overflow="wrap" %}
```typescript
 private async getSchemaFromVersion<T = ContractSchema>(adoVersion: string, subSchema = 'default') {
    const [adoType, version] = adoVersion.split('@');
    const subPath = subSchema === 'default' ? adoType : subSchema;
    const schema: T = await axios.get(`${ADOSchemaAPI.SCHEMA_BASE_URL}/raw/${adoType}/${version}/${subPath}`).then(res => res.data);
    return {
      schema,
      adoVersion: adoVersion
    }
  }
```
{% endcode %}

| Name         | Type   | Description             |
| ------------ | ------ | ----------------------- |
| `adoVersion` | string | The version of the ADO. |

### getSchemaFromCodeId

Gets the schema for the provided code Id

{% code overflow="wrap" %}
```typescript
 async getSchemaFromCodeId<T = ContractSchema>(codeId: number, subSchema = 'default', fallbackType?: string) {
    let schema = await axios.get(`${ADOSchemaAPI.SCHEMA_BASE_URL}/raw/code_id/${codeId}/${await this.client.chainClient?.queryClient?.getChainId()}/${subSchema}`).then(res => res.data as T).catch(() => undefined);
    if (!schema) {
      schema = await this.client.os.adoDB?.getAdoType(codeId).then(adoVersion => this.getSchemaFromVersion<T>(adoVersion, subSchema)).then(data => data.schema).catch(() => undefined);
    }
    // If we still don't have schema, try to get ado type from ado type query
    if (!schema && fallbackType) {
      schema = await this.getSchemaFromVersion<T>(fallbackType, subSchema).then(data => data.schema).catch(() => undefined);
    }
    if (!schema) throw new Error("Schema not found!");
    return {
      schema: schema!,
      codeId,
    }
  }
```
{% endcode %}

| Name     | Type   | Description                                     |
| -------- | ------ | ----------------------------------------------- |
| `codeId` | number | The code Id of the ADO to fetch the schema for. |

### getSubSchemaFromCodeId

Gets the sub schema, like receive schema, from the codeId. Similar to getSchemaFromCodeId

```typescript
async getSubSchemaFromCodeId(codeId: number, subKey: string) {
    return this.getSchemaFromCodeId<Schema>(codeId, subKey);
  }

}
```

| Name     | Type   | Description                                     |
| -------- | ------ | ----------------------------------------------- |
| `codeId` | number | The code Id of the ADO to fetch the schema for. |
| `subKey` | string | The subkey for the subschema.                   |
