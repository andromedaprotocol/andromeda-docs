# WalletStore Class

## Import

```typescript
import {WalletStore} from "@andromedaprotocol/andromeda.js"
```

## Class

Used to store wallets based on Chain IDs and Mnemonics.

```typescript
export default class WalletStore {
  wallets: Record<string, Wallet[]> = {};
  defaultWallets: Record<string, Wallet> = {};
  /* methods */
}
```

| Name             | Type                                          | Description                 |
| ---------------- | --------------------------------------------- | --------------------------- |
| `wallets`        | Record\<string, [Wallet](wallet-class.md)\[]> | The stored wallets.         |
| `defaultWallets` | Record\<string, [Wallet](wallet-class.md)>    | The stored default wallet.  |

## Methods

### addWallet

Adds a wallet by mnemonic and chain ID.

```typescript
  addWallet(chainId: string, name: string, mnemonic?: string) {
    const trimmedChainId = chainId.trim();
    if (trimmedChainId.length === 0) throw new Error("Invalid Chain ID");

    const newWallet = new Wallet(name, mnemonic);
    if (this.wallets[trimmedChainId]) {
      this.wallets[trimmedChainId] = [
        ...this.wallets[trimmedChainId],
        newWallet,
      ];
    } else {
      this.wallets[trimmedChainId] = [newWallet];
    }

    return newWallet;
  }
```

| Name       | Type   | Description                                                                            |
| ---------- | ------ | -------------------------------------------------------------------------------------- |
| `chainId`  | string | The chain Id of the chain to add the wallet on.                                        |
| `name`     | string | The name of the wallet.                                                                |
| `mnemonic` | string | Optional mnemonic for the wallet. If not specified, random mnemonic will be generated. |

### removeWallet

Removes a wallet by its name for a given Chain ID.

```typescript
removeWallet(name: string, chainId: string) {
    const trimmedIdentifier = name.trim();

    if (trimmedIdentifier.length === 0)
      throw new Error("Invalid Wallet Identifier");

    const wallets = this.getWallets(chainId);
    if (!wallets.find((wallet) => wallet.name === trimmedIdentifier))
      throw new Error(`No wallet found by name ${trimmedIdentifier}`);

    this.wallets[chainId.trim()] = wallets.filter(
      (wallet) => wallet.name !== trimmedIdentifier
    );

    const defaultWallet = this.getDefaultWallet(chainId);
    if (defaultWallet.name === name) {
      this.removeDefaultWallet(chainId);
    }
  }
```

| Name      | Type   | Description                                      |
| --------- | ------ | ------------------------------------------------ |
| `name`    | string | The name of the wallet to remove.                |
| `chainId` | string | The chain Id of the chain that the wallet is on. |

### removeWalletByIndex

Removes a wallet by index and chain ID.

```typescript
removeWalletByIndex(index: number, chainId: string) {
    const wallets = this.getWallets(chainId);

    const removed = wallets.splice(index, 1);

    this.wallets[chainId.trim()] = wallets;

    const defaultWallet = this.getDefaultWallet(chainId);
    if (defaultWallet.name === removed[0].name) {
      this.removeDefaultWallet(chainId);
    }
  }
```

| Name      | Type   | Description                                 |
| --------- | ------ | ------------------------------------------- |
| `index`   | number | The index of the wallet to remove.          |
| `chainId` | string | The chain Id of the chain the wallet is on. |

### ChainIDs

Gets an array of all currently stored chain IDs.

```typescript
get ChainIDs() {
    return Object.keys(this.wallets);
  }
```

### getWallets

Gets all wallets for a given chain ID.

```typescript
 getWallets(chainId: string) {
    const trimmedChainId = chainId.trim();
    if (trimmedChainId.length === 0) throw new Error("Invalid Chain ID");
    return this.wallets[trimmedChainId] ?? [];
  }
```

| Name      | Type   | Description                                       |
| --------- | ------ | ------------------------------------------------- |
| `chainId` | string | The chain Id of the chain to get the wallets for. |

Returns an array of wallets for the given chain Id.

### getAllWallets

Gets all wallets in the store with their assigned chain ID.

```typescript
  getAllWallets(): { chainId: string; wallet: Wallet }[] {
    const chainIds = this.ChainIDs;
    const wallets: { chainId: string; wallet: Wallet }[] = [];
    for (let i = 0; i < chainIds.length; i++) {
      const chainId = chainIds[i];
      const chainWallets = this.getWallets(chainId);
      wallets.push(...chainWallets.map((wallet) => ({ chainId, wallet })));
    }

    return wallets;
  }
```

### getWallet

Get a wallet by Chain ID/Identifier, identifier being a name or address.

```typescript
async getWallet(chainId: string, identifier: string) {
    const wallet = this.getWalletByName(chainId, identifier);
    if (!wallet) return this.getWalletByAddress(chainId, identifier);
    return wallet;
  }
```

| Name         | Type   | Description                                                           |
| ------------ | ------ | --------------------------------------------------------------------- |
| `chainId`    | string | The chain Id of the chain the wallet is on.                           |
| `identifier` | string | The identifier of the wallet being the name or address of the wallet. |

### getWalletByName

Get a wallet by Chain ID/Name combination.

```typescript
getWalletByName(chainId: string, name: string) {
    const trimmedChainId = chainId.trim();
    if (trimmedChainId.length === 0) throw new Error("Invalid Chain ID");
    const wallet = (this.wallets[trimmedChainId] ?? []).find(
      (wallet) => wallet.name === name
    );
    return wallet;
  }
```

| Name      | Type   | Description                                 |
| --------- | ------ | ------------------------------------------- |
| `chainId` | string | The chain Id of the chain the wallet is on. |
| `name`    | string | The name of the wallet.                     |

### getWalletByAddress

Get a wallet by Chain ID/Address combination.

```typescript
async getWalletByAddress(chainId: string, address: string) {
    const trimmedChainId = chainId.trim();
    if (trimmedChainId.length === 0) throw new Error("Invalid Chain ID");
    const wallets = this.wallets[trimmedChainId];
    for (let i = 0; i < wallets.length; i++) {
      const wallet = wallets[i];
      if ((await wallet.getFirstOfflineSigner(chainId)) === address) {
        return wallet;
      }
    }
    return;
  }
```

| Name      | Type   | Description                                 |
| --------- | ------ | ------------------------------------------- |
| `chainId` | string | The chain Id of the chain the wallet is on. |
| `address` | string | The address of the wallet.                  |

### setDefaultWallet

Sets the default wallet for a given chain ID.

```typescript
setDefaultWallet(chainId: string, wallet: Wallet) {
    this.defaultWallets[chainId] = wallet;
    const wallets = this.getWallets(chainId);
    if (!wallets.map((wallet) => wallet.name).includes(wallet.name)) {
      this.addWallet(wallet.mnemonic, chainId, wallet.name);
    }
  }
```

| Name      | Type                      | Description                                                          |
| --------- | ------------------------- | -------------------------------------------------------------------- |
| `chainId` | string                    | The chain Id of the chain to set the default wallet on.              |
| `wallet`  | [Wallet](wallet-class.md) | The wallet to be used as the default wallet for the specified chain. |

### getDefaultWallet

Gets and returns the default wallet for a given chain ID.

```typescript
getDefaultWallet(chainId: string) {
    return this.defaultWallets[chainId];
  }
```

| Name      | Type   | Description                                                      |
| --------- | ------ | ---------------------------------------------------------------- |
| `chainId` | string | The chain Id of the chain we want to get the default wallet for. |

### removeDefaultWallet

Removes the default wallet for a given chain ID. Sets the first indexed wallet for the chain ID as the new default.

```typescript
removeDefaultWallet(chainId: string) {
    const wallets = this.getWallets(chainId);
    delete this.defaultWallets[chainId];

    if (wallets.length > 0) {
      this.setDefaultWallet(chainId, wallets[0]);
    }
  }
```

| Name      | Type   | Description                                                          |
| --------- | ------ | -------------------------------------------------------------------- |
| `chainId` | string | The chain Id of the chain we want to remove the default wallet from. |
