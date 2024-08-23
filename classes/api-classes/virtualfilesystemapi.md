# VirtualFileSystemAPI

## Class

Class to interact with the VFS aOS contract.

```typescript
export default class VirtualFileSystemAPI extends ADOAPI {
  constructor(public client: AndromedaClient, public address: string) {
    super(client, address);
  }
```

### resolvePathMsg

Generates a 'ResolvePath' query message for the VFS.

```typescript
resolvePathMsg(path: string) {
    return {
      resolve_path: {
        path,
      },
    };
  }
```

| Name   | Type   | Description          |
| ------ | ------ | -------------------- |
| `path` | string | The path to resolve. |

### resolvePath

Resolves the specified path to its corresponding address.

```typescript
 async resolvePath(path: string) {
    this.preMessage();
    if (!path || path.length === 0)
      throw new Error("Cannot resolve an empty path using the VFS");
    return this.client.queryContract(this.address, this.resolvePathMsg(path));
  }
```

| Name   | Type   | Description          |
| ------ | ------ | -------------------- |
| `path` | string | The path to resolve. |

### registerUserMsg

Generates a 'RegisterUser' message for the VFS.

```typescript
registerUserMsg(username: string) {
    return {
      register_user: {
        username,
      },
    };
  }
```

| Name       | Type   | Description               |
| ---------- | ------ | ------------------------- |
| `username` | string | The username to register. |

### registerUser

Registers a username for the currently used signing address.

```typescript
 async registerUser(
    username: string,
    msgParams?: OptionalExecuteParams
  ) {
    this.preMessage();
    if (!username || username.length === 0)
      throw new Error("Cannot register an empty username");

    return this.client.execute(
      this.address,
      this.registerUserMsg(username),
      msgParams?.fee,
      msgParams?.memo,
      msgParams?.funds
    );
  }
```

| Name        | Type                                                                   | Description                                                                   |
| ----------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `username`  | string                                                                 | The username to register.                                                     |
| `msgParams` | [OptionalExecuteParams](virtualfilesystemapi.md#optionalexecuteparams) | Optional parameters to add to the execute message such as funds, memo or fee. |

#### OptionalExecuteParams

```typescript
export interface OptionalExecuteParams {
  funds?: readonly Coin[] | Coin[];
  memo?: string;
  fee?: StdFee;
}

```

### addPathMsg

Generates a 'AddPath' message for the VFS.

```typescript
 addPathMsg(name: string, address: string) {
    return {
      add_path: {
        name,
        address,
      },
    };
  }
```

| Name      | Type   | Description                                      |
| --------- | ------ | ------------------------------------------------ |
| `name`    | string | The name of the path to register the address to. |
| `address` | string | The address to register the path to.             |

### addPath

Registers a path for the currently used signing address.

```typescript
  async addPath(
    name: string,
    address: string,
    msgParams?: OptionalExecuteParams
  ) {
    this.preMessage();
    if (!name || name.length === 0)
      throw new Error("Cannot register an empty path");
    if (!address || address.length === 0 || !validateAddress(address))
      throw new Error(
        "Cannot register an invalid address for a path within the VFS"
      );

    return this.client.execute(
      this.address,
      this.addPathMsg(name, address),
      msgParams?.fee,
      msgParams?.memo,
      msgParams?.funds
    );
  }
```

| Name        | Type                                                                   | Description                                                                   |
| ----------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `name`      | string                                                                 | The name of the path to register the address to.                              |
| `address`   | string                                                                 | The address to register the path to.                                          |
| `msgParams` | [OptionalExecuteParams](virtualfilesystemapi.md#optionalexecuteparams) | Optional parameters to add to the execute message such as funds, memo or fee. |

### getUsernameMsg

Generates a 'GetUsername' query message for the VFS.

```typescript
 getUsernameMsg(address: string) {
    return {
      get_username: {
        address,
      },
    };
  }
```

| Name      | Type   | Description                          |
| --------- | ------ | ------------------------------------ |
| `address` | string | The address to get the username for. |

### getUsername

Resolves the username for the given address

```typescript
async getUsername(address: string) {
    this.preMessage();
    return this.client.queryContract(this.address, this.getUsernameMsg(address));
  }
```

| Name      | Type   | Description                          |
| --------- | ------ | ------------------------------------ |
| `address` | string | The address to get the username for. |

### subDirMsg

Generates a 'SubDir' query message for the VFS.

```typescript
 subDirMsg(path: string) {
    return {
      sub_dir: {
        path,
      },
    };
  }
```

| Name   | Type   | Description                    |
| ------ | ------ | ------------------------------ |
| `path` | string | The path to get sub paths for. |

### subDir

Resolves the sub dir for a given path.

```typescript
async subDir(path: string) {
    this.preMessage();
    if (!path || path.length === 0)
      throw new Error("Cannot resolve an empty path using the VFS");
    return this.client.queryContract(this.address, this.subDirMsg(path));
  }
```

| Name   | Type   | Description                    |
| ------ | ------ | ------------------------------ |
| `path` | string | The path to get sub paths for. |

### pathsMsg

Generates a 'paths' query message for the VFS.

```typescript
pathsMsg(addr: string) {
    return {
      paths: {
        addr,
      },
    };
  }
```

| Name   | Type   | Description                    |
| ------ | ------ | ------------------------------ |
| `addr` | string | The addr to get the paths for. |

### paths

Resolves the paths for a given address

```typescript
  async paths(addr: string) {
    this.preMessage();
    if (
      !addr ||
      addr.length === 0 ||
      !validateAddress(addr)
    )
      throw new Error(
        "Cannot resolve an invalid address using the VFS"
      );
    return this.client.queryContract<string[]>(this.address, this.pathsMsg(addr));
  }
}
```

| Name   | Type   | Description                    |
| ------ | ------ | ------------------------------ |
| `addr` | string | The addr to get the paths for. |
