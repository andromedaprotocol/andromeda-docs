# OperatingSystemAPI

## Class

Class to interact with teh aOS contracts.

```typescript
export default class OperatingSystemAPI extends ADOAPI {
  public vfs?: VirtualFileSystemAPI;
  public adoDB?: ADODatabaseAPI;

  constructor(client: AndromedaClient, public address: string = "") {
    super(client, address);
  }
```

| Name  | Type                                            | Description    |
| ----- | ----------------------------------------------- | -------------- |
| vfs   | [VirtualFileSystemAPI](virtualfilesystemapi.md) | The VFS API.   |
| adoDB | [ADODatabaseAPI](adodatabaseapi.md)             | The ADODB API. |

### assignKernelAddress

Assigns the kernel address before fetching any key addresses

```typescript
async assignKernelAddress(address: string) {
    this.address = address;
    await this.fetchVFSAddress();
    await this.fetchADODBAddress();
  }
```

| Name      | Type   | Description                          |
| --------- | ------ | ------------------------------------ |
| `address` | string | The address of the kernel to assign. |

### fetchVFSAddress

Fetches the current VFS address from the kernel

```typescript
async fetchVFSAddress() {
    const vfsKey = "vfs";
    const vfsAddress = await this.fetchKeyAddress(vfsKey);

    this.vfs = new VirtualFileSystemAPI(this.client, vfsAddress);
  }
```

### fetchADODBAddress

Fetches the current ADODB address from the kernel.

```typescript
async fetchADODBAddress() {
    const adoDBKey = "adodb";
    const adoDBAddress = await this.fetchKeyAddress(adoDBKey);
    this.adoDB = new ADODatabaseAPI(this.client, adoDBAddress);
  }
```

### getKeyAddressMessage

Generates a 'KeyAddress' kernel query for a given key

```typescript
getKeyAddressMessage(key: string) {
    return {
      key_address: {
        key,
      },
    };
  }
```

| Name  | Type   | Description                     |
| ----- | ------ | ------------------------------- |
| `key` | string | The key to get the address for. |

### fetchKeyAddress

Fetches a key address for a given key from the currently assigned kernel

```typescript
 async fetchKeyAddress(key: string) {
    this.preMessage();
    if (!key || key.length === 0)
      throw new Error("Cannot fetch empty key address from kernel");

    return this.client.queryContract(
      this.address,
      this.getKeyAddressMessage(key)
    );
  }
}
```

| Name      | Type   | Description                          |
| --------- | ------ | ------------------------------------ |
| `address` | string | The address of the kernel to assign. |
