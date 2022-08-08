# ADO Class

## Import

```typescript
import {ADO} from "@andromedaprotocol/andromeda.js"
```

## Class

```typescript
export default class ADO {
  constructor(
    public client: AndromedaClient,
    public schemas: PackageSchemas,
    public name: string = ""
  ) {
    this.client = client;
    this.name = name;
    this.schemas = schemas;
   }
  /* methods */
  }
```

| Name      | Type                                        | Description            |
| --------- | ------------------------------------------- | ---------------------- |
| `client`  | [AndromedaClient](andromedaclient-class.md) | The client of the ado. |
| `schemas` | PackageSchemas                              | The                    |
| `name`    | string                                      | The name of the ado.   |

## Methods

### fetchCodeId

Gets the code Id of the ADO.

```typescript
 async fetchCodeId(factoryAddress?: string) {
    if (!this.name || this.name.length === 0)
      throw new Error("No name assigned to ADO type");

    const codeId = await this.client.ado.factory.getCodeId(
      this.name,
      factoryAddress
    );

    return codeId;
  }
```

| Name             | Type   | Description                                       |
| ---------------- | ------ | ------------------------------------------------- |
| `factoryAddress` | string | Optional factory address to get the code Id from. |

### create

Creates a new ADO using the defined instantiation message. Instantiate the ADO using the [instantiate ](andromedaclient-class.md#instantiate)method of the AndromedaClient class.

```typescript
async create(
    instantiationMsg: Record<string, any>,
    fee: Fee,
    label = `Instantiate ${this.name}`,
    options?: InstantiateOptions,
    factoryAddress?: string
  ) {
    const codeId = await this.fetchCodeId(factoryAddress);
    const validator = new Validator();
    validator.validate(instantiationMsg, this.schemas.instantiate);

    return this.client.instantiate(codeId, instantiationMsg, label, fee, {
      admin: this.client.signer,
      ...options,
    });
  }
```

| Name             | Type                                                                                                              | Description                                                                 |
| ---------------- | ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `instantiateMsg` | Record\<string, any>                                                                                              | The instantiation message of the ADO.                                       |
| `fee`            | [Fee](andromedaclient-class.md#fee)                                                                               | The fee to pay.                                                             |
| `label`          | string                                                                                                            | The label given which consists of " Instantiate \<ADO name> ".              |
| `options`        | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | Options for the instantiate call. Includes optional admin, funds, and memo. |
| `factoryAddress` | string                                                                                                            | The factory address of the used to instantiate the ADO.                     |

### fromPackageDefinition

```typescript
static async fromPackageDefinition(
    client: AndromedaClient,
    definition: PackageDefinition
  ) {
    const execute = await fetchSchema(definition.schemas.execute);
    const instantiate = await fetchSchema(definition.schemas.instantiate);
    const query = await fetchSchema(definition.schemas.query);
    const schemas = {
      execute,
      instantiate,
      query,
    };
    return new ADO(client, schemas, definition.name);
  }
}
```

| Name         | Type                                        | Description  |
| ------------ | ------------------------------------------- | ------------ |
| `client`     | [AndromedaClient](andromedaclient-class.md) | The client.  |
| `definition` | PackageDefinition                           | ///          |
