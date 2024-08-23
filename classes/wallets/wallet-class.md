# Wallet Class

## Class

Used to generate a client wallet by Mnemonic.

<pre class="language-typescript"><code class="lang-typescript"><strong>export default class Wallet {
</strong>  constructor(
    public name: string,
    public key: string,
    public prefix?: string
  ) {}
</code></pre>

| Name     | Type    | Description                                                           |
| -------- | ------- | --------------------------------------------------------------------- |
| `name`   | string  | The name of the wallet.                                               |
| `key`    | string  | The key of the wallet which is the encrypted password for the wallet. |
| `prefix` | string  | Optional prefix for the wallet.                                       |

## Methods

### fromMnemonic

Generates a new wallet from a given mnemonic.

```typescript
static async fromMnemonic(
    name: string,
    mnemonic: string,
    passphrase: string,
    prefix: string
  ) {
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, {
      prefix,
    });
    const key = await wallet.serialize(passphrase);
    return new Wallet(name, key, prefix);
  }
```

| Name         | Type   | Description                                       |
| ------------ | ------ | ------------------------------------------------- |
| `name`       | string | The name to assign for the wallet.                |
| `mnemonic`   | string | The mnemonic used to generate the wallet address. |
| `passphrase` | string | The passphrase for the wallet.                    |
| `prefix`     | string | The bech32 address prefix.                        |

### getWallet

Gets the wallet associated with the provided passphrase.

```typescript
async getWallet(
    passphrase: string
  ): Promise<DirectSecp256k1HdWallet | DirectEthSecp256k1Wallet> {
    return await DirectSecp256k1HdWallet.deserialize(this.key, passphrase);
  }
```

| Name         | Type   | Description                          |
| ------------ | ------ | ------------------------------------ |
| `passphrase` | string | The passphrase of the wallet to get. |

### getAccounts

Get all accounts associated with the wallet.

```typescript
async getAccounts(passphrase: string) {
    const wallet = await this.getWallet(passphrase);
    return await wallet.getAccounts();
  }
```

| Name         | Type   | Description                                       |
| ------------ | ------ | ------------------------------------------------- |
| `passphrase` | string | The passphrase of the wallet to get accounts for. |

### getAddress

Provides the first OfflineSigner object associated with the wallet. Can be used for signing messages.

```typescript
 async getAddress(passphrase: string) {
    const [firstAccount] = await this.getAccounts(passphrase);
    return firstAccount.address;
  }
```

| Name         | Type   | Description                                          |
| ------------ | ------ | ---------------------------------------------------- |
| `passphrase` | string | The passphrase of the wallet to get the address for. |
