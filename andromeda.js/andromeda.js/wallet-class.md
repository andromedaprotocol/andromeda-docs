# Wallet Class

## Import

```typescript
import {Wallet} from "@andromedaprotocol/andromeda.js"
```

## Class

Used to generate a client wallet by Mnemonic.

```typescript
export default class Wallet {

  mnemonic: string;
  name: string;

  constructor(name: string, mnemonic?: string) {
    this.mnemonic =
      mnemonic && mnemonic.length > 0 ? mnemonic : generateMnemonic(256);
    this.name = name;
  }
  /* methods */ 
}
```

| Name       | Type    | Description                 |
| ---------- | ------- | --------------------------- |
| `mnemonic` | string  | The mnemonic of the wallet. |
| `name`     | string  | The name of the wallet.     |

## Methods

### getWallet

Get wallet associated with the provided mnemonic.

```typescript
async getWallet(chainId: string) {
    const config = getConfigByChainID(chainId);
    if (!config) throw new Error("No config for provided chain ID");
    return await DirectSecp256k1HdWallet.fromMnemonic(this.mnemonic, {
      prefix: config.addressPrefix,
    });
  }
```

| Name      | Type   | Description                                            |
| --------- | ------ | ------------------------------------------------------ |
| `chainId` | string | The chainId that specifies the chain the wallet is on. |

### getAccounts

Get all accounts associated with the wallet.

```typescript
 async getAccounts(chainId: string) {
    const wallet = await this.getWallet(chainId);
    return await wallet.getAccounts();
  }
```

| Name      | Type   | Description                                            |
| --------- | ------ | ------------------------------------------------------ |
| `chainId` | string | The chainId that specifies the chain the wallet is on. |

### getFirstOfflineSigner

Provides the first OfflineSigner object associated with the wallet. Can be used for signing messages.

```typescript
 async getFirstOfflineSigner(chainId: string) {
    const [firstAccount] = await this.getAccounts(chainId);
    return firstAccount.address;
  }
}
```

| Name      | Type   | Description                                            |
| --------- | ------ | ------------------------------------------------------ |
| `chainId` | string | The chainId that specifies the chain the wallet is on. |
