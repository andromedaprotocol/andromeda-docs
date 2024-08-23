# API Classes

## ADOAPI Class

API to interact with Andromeda ADOs.

```typescript
export default class ADOAPI {
  constructor(
    protected client: AndromedaClient,
    protected address: string = ""
  ) {}
```

## Execute Messages

### andromedaReceive

Converts a message object to an Andromeda Execute message.

```typescript
 protected andromedaReceive(msg: Msg) {
    return { andr_receive: msg };
  }

```

| Name  | Type          | Description             |
| ----- | ------------- | ----------------------- |
| `msg` | [Msg](./#msg) | The message to convert. |

#### Msg

```typescript
export type Msg = Record<string, unknown>
```

### updateOwnerMsg

Returns an update owner message.

```typescript
updateOwnerMsg(newOwner: string, expiration?: Expiration) {
    return {
      ownership: {
        update_owner: {
          new_owner: newOwner,
          expiration
        }
      }
    };
  }
```

| Name         | Type                        | Description                                                       |
| ------------ | --------------------------- | ----------------------------------------------------------------- |
| `newOwner`   | string                      | The address to assign as the new owner.                           |
| `expiration` | [Expiration](./#expiration) | Optional expiration for the ownership request. Defaults to never. |

#### Expiration

{% code overflow="wrap" %}
```typescript
export type Expiration = ExpirationAtHeight | ExpirationAtTime | ExpirationNever;

// 
 
export interface ExpirationAtHeight {
  at_height: number
}
//Timestamp
export interface ExpirationAtTime {
  at_time: string
}
//Never Expire
export interface ExpirationNever {
  never: {}
}
```
{% endcode %}

### updateOwner

Updates the owner for the given ADO.

{% hint style="warning" %}
Only accessible to the current owner.
{% endhint %}

```typescript
 async updateOwner(
    newOwner: string,
    addr: string = this.address,
    expiration?: Expiration,
    fee?: Fee,
    memo?: string
  ) {
    const msg = this.updateOwnerMsg(newOwner, expiration);
    const resp = await this.client.execute(addr, msg, fee, memo);

    return resp;
  }
```

<table><thead><tr><th width="245">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>newOwner</code></td><td>string</td><td>The address of the new owner.</td></tr><tr><td><code>addr</code></td><td>string</td><td>The address of the contract we are executing on.</td></tr><tr><td><code>expiration</code></td><td><a href="./#expiration">Expiration</a></td><td>Optional expiration. Defaults to never.</td></tr><tr><td><code>fee</code></td><td><a href="./#fee">Fee</a></td><td>Optional fee attached.</td></tr><tr><td><code>memo</code></td><td>string</td><td>Optional memo to attached.</td></tr></tbody></table>

#### Fee

```typescript
export type Fee = number | StdFee | "auto"
```

Check [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html).

### updateAppContractMsg

Returns an update app contract message.

```typescript
updateAppContractMsg(appContract: string) {
    return this.andromedaReceive({
      update_app_contract: { address: appContract },
    });
  }
```

| Name          | Type   | Description           |
| ------------- | ------ | --------------------- |
| `appContract` | string | The new app contract. |

### updateAppContract

Updates the app contract for a given ADO.

{% hint style="warning" %}
Only accessible to the current owner.
{% endhint %}

```typescript
async updateAppContract(
    appContract: string,
    addr: string = this.address,
    fee?: Fee,
    memo?: string
  ) {
    const msg = this.updateAppContractMsg(appContract);
    const resp = await this.client.execute(addr, msg, fee, memo);

    return resp;
  }
```

| Name          | Type          | Description                                      |
| ------------- | ------------- | ------------------------------------------------ |
| `appContract` | string        | The new app contract.                            |
| `addr`        | string        | The address of the contract we are executing on. |
| `fee`         | [Fee](./#fee) | Optional fee to attach.                          |
| `memo`        | string        | Optional memo to attach.                         |

### registerModuleMsg

Returns a register module message.

```typescript
registerModuleMsg(module: Module) {
    return { register_module: { module } };
  }
```

| Name   | Type   | Description             |
| ------ | ------ | ----------------------- |
| module | Module | The module to register. |

#### Module

```typescript
export interface Module {
  name?: string;
  /** The address of the module */
  address: AndrAddress;
  /** Whether the module is mutable */
  is_mutable: boolean;
  /** The module idx (if it is already stored within a contract) */
  idx?: number;
}
```

#### AndrAddress

Object used to define an address used with the Andromeda ecosystem.

```typescript
export type AndrAddress = string;
```

### registerModule

Register a module with an ADO.

{% hint style="warning" %}
Only accessible by the contract owner.

Will error if the ADO does not implement modules.
{% endhint %}

```typescript
async registerModule(
    module: Module,
    addr: string = this.address,
    fee?: Fee,
    memo?: string
  ) {
    const msg = this.registerModuleMsg(module);
    const resp = await this.client.execute(addr, msg, fee, memo);

    return resp;
  }
```

| Name     | Type                | Description                                      |
| -------- | ------------------- | ------------------------------------------------ |
| `module` | [Module](./#module) | The module to register.                          |
| `addr`   | string              | The address of the contract we are executing on. |
| `fee`    | [Fee](./#fee)       | Optional fee to attach.                          |
| `memo`   | string              | Optional memo to attach.                         |

### deregisterModuleMsg

Returns a deregister module message.

```typescript
 deregisterModuleMsg(id: number) {
    return {
      deregister_module: { module_idx: `${id}` },
    };
  }
```

| Name | Type     | Description                                        |
| ---- | -------- | -------------------------------------------------- |
| `id` | `number` | The id number (index) of the module to deregister. |

### deregisterModule

Deregisters a module with an ADO.

{% hint style="warning" %}
Only accessible by the contract owner.

Will error if the ADO does not implement modules.
{% endhint %}

```typescript
 async deregisterModule(
    id: number,
    addr: string = this.address,
    fee?: Fee,
    memo?: string
  ) {
    const msg = this.deregisterModuleMsg(id);
    const resp = await this.client.execute(addr, msg, fee, memo);

    return resp;
  }
```

| Name   | Type          | Description                                        |
| ------ | ------------- | -------------------------------------------------- |
| `id`   | number        | The id number (index) of the module to deregister. |
| `addr` | string        | The address of the contract we are executing on.   |
| `fee`  | [Fee](./#fee) | Optional fee to attach.                            |
| `memo` | string        | Optional memo to attach.                           |

### alterModuleMsg

Returns am alter module message.

```typescript
alterModuleMsg(id: number, module: Module) {
    return {
      alter_module: { module, module_idx: `${id}` },
    };
  }
```

| Name     | Type                | Description                                   |
| -------- | ------------------- | --------------------------------------------- |
| `id`     | number              | The id number (index) of the module to alter. |
| `module` | [Module](./#module) | The new module to replace the old one.        |

### alterModule

Alters a module within an ADO.

{% hint style="warning" %}
Only accessible by the contract owner.

Will error if the ADO does not implement modules.
{% endhint %}

```typescript
  async alterModule(
    id: number,
    module: Module,
    addr: string = this.address,
    fee?: Fee,
    memo?: string
  ) {
    const msg = this.alterModuleMsg(id, module);
    const resp = await this.client.execute(addr, msg, fee, memo);

    return resp;
  }
```

| Name     | Type                | Description                                      |
| -------- | ------------------- | ------------------------------------------------ |
| `id`     | number              | The id number (index) of the module to alter.    |
| `module` | [Module](./#module) | The new module to replace the old one.           |
| `addr`   | string              | The address of the contract we are executing on. |
| `fee`    | [Fee](./#fee)       | Optional fee to attach.                          |
| `memo`   | string              | Optional memo to attach.                         |

## Query Messages

### ownerQuery

Returns an owner query message.

```typescript
ownerQuery() {
    return { owner: {} };
  }
```

### getOwner

Gets the owner address for a provided ADO.

```typescript
async getOwner(addr: string = this.address) {
    const query = this.ownerQuery();
    const resp = await this.client.queryContract<{ owner: string }>(
      addr,
      query
    );
    return resp.owner;
  }
```

| Name   | Type   | Description                                  |
| ------ | ------ | -------------------------------------------- |
| `addr` | string | The address of the contract we are querying. |

### isOwner

Validates if a given address is an owner for the given ADO.

```typescript
  async isOperatorOrOwner(addr: string, contractAddr: string = this.address) {
    const owner = await this.getOwner(contractAddr);
    return operators.includes(addr) || owner === addr;
  }

```

| Name           | Type   | Description                                   |
| -------------- | ------ | --------------------------------------------- |
| `addr`         | string | The address we are checking.                  |
| `contractAddr` | string | The address of the contract we are querying.  |

### typeQuery

Returns an ADO type query message.

```typescript
typeQuery() {
    return { type: {} };
  }
```

### getType

Gets the ADO type for the provided ADO.

```typescript
 async getType(addr: string = this.address) {
    const query = this.typeQuery();
    const resp = await this.client.queryContract<{ ado_type: string }>(
      addr,
      query
    );

    return resp.ado_type;
  }
```

| Name   | Type   | Description                                   |
| ------ | ------ | --------------------------------------------- |
| `addr` | string | The address of the contract we are querying.  |

### publisherQuery

Returns a publisher query message.

```typescript
 publisherQuery() {
    return { original_publisher: {} };
  }
```

### getPublisher

Gets the original publisher of the given ADO.

```typescript
  async getPublisher(addr: string = this.address) {
    const query = this.publisherQuery();
    const resp = await this.client.queryContract<{
      original_publisher: string;
    }>(addr, query);

    return resp.original_publisher;
  }
```

| Name   | Type   | Description                                   |
| ------ | ------ | --------------------------------------------- |
| `addr` | string | The address of the contract we are querying.  |

### createdHeightQuery

Returns a block height creation query.

```typescript
createdHeightQuery() {
    return { block_height_upon_creation: {} };
  }
```

### getCreatedHeight

Gets the block height at which the given ADO was created.

```typescript
 async getCreatedHeight(addr: string = this.address) {
    const query = this.createdHeightQuery();
    const resp = await this.client.queryContract<{ block_height: number }>(
      addr,
      query
    );

    return resp.block_height;
  }
```

<table><thead><tr><th width="241">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>addr</code></td><td>string</td><td>The address of the contract we are querying. </td></tr></tbody></table>

### versionQuery

Returns a version query.

```typescript
 versionQuery() {
    return { version: {} };
  }
```

### getVersion

Gets the version for a given ADO.

```typescript
  async getVersion(addr: string = this.address) {
    const query = this.versionQuery();
    const resp = await this.client.queryContract<{ version: string }>(
      addr,
      query
    );

    return resp.version;
  }
```

<table><thead><tr><th width="241">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>addr</code></td><td>string</td><td>The address of the contract we are querying. </td></tr></tbody></table>

### moduleQuery

Returns a module query.

```typescript
moduleQuery(id: string | number) {
    return { module: { id } };
  }
```

<table><thead><tr><th width="241">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>id</code></td><td>string or number</td><td>The id of the module to query.</td></tr></tbody></table>

### getModule

Gets the module of the specified id for a given ADO.

{% hint style="warning" %}
Will error if the ADO does not implement modules.
{% endhint %}

```typescript
  async getModule(id: string | number, addr: string = this.address) {
    const query = this.moduleQuery(id);
    const resp = await this.client.queryContract<Module>(addr, query);

    return resp;
  }
```

<table><thead><tr><th width="241">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>id</code></td><td>string or number</td><td>The id of the module to query.</td></tr><tr><td><code>addr</code></td><td>string</td><td>The address of the contract we are querying. </td></tr></tbody></table>

### moduleIdsQuery

Returns a module IDs query.

```typescript
  moduleIdsQuery() {
    return { module_ids: {} };
  }
```

### getModuleIds

Gets all module IDs for a given ADO.

{% hint style="warning" %}
Will error if the ADO does not implement modules
{% endhint %}

```typescript
  async getModuleIds(addr: string = this.address) {
    const query = this.moduleIdsQuery();
    const resp = await this.client.queryContract<string[]>(addr, query);

    return resp;
  }
```

<table><thead><tr><th width="241">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>addr</code></td><td>string</td><td>The address of the contract we are querying. </td></tr></tbody></table>

### getModules

Gets all modules for a given ADO.&#x20;

{% hint style="warning" %}
Uses several queries so response may be slow.
{% endhint %}

```typescript
  async getModules(addr: string = this.address) {
    const ids = await this.getModuleIds(addr);
    const modulePromises = [];

    for (let i = 0; i < ids.length; i++) {
      modulePromises.push(this.getModule(ids[i], addr));
    }

    const modules = await Promise.all(modulePromises);

    return modules;
  }
}

```

<table><thead><tr><th width="241">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>addr</code></td><td>string</td><td>The address of the contract we are querying. </td></tr></tbody></table>
