# EtherWallet

Class to generate an Ethereum wallet to to be used on the Injective chain.&#x20;

## Class

```typescript
export default class EtherWallet extends Wallet 
```

## Methods

### fromMnemonic

Generates a new wallet from a given mnemonic.

```typescript
export default class EtherWallet extends Wallet {
  static async fromMnemonic(
    name: string,
    mnemonic: string,
    passphrase: string
  ): Promise<Wallet> {
    // Injective wallets use Ethers wallet for generation
    const wallet = HDNodeWallet.fromPhrase(mnemonic, undefined, DEFAULT_DERIVATION_PATH);
    const key = Crypto.AES.encrypt(wallet.privateKey, passphrase).toString();
    return new EtherWallet(name, key, "inj");
  }
```

| Name         | Type   | Description                                       |
| ------------ | ------ | ------------------------------------------------- |
| `name`       | string | The name to assign for the wallet.                |
| `mnemonic`   | string | The mnemonic used to generate the wallet address. |
| `passphrase` | string | The passphrase for the wallet.                    |

### getWallet

Gets the wallet associated with the provided passphrase.

```typescript
async getWallet(passphrase: string) {
    const storedKey = Crypto.AES.decrypt(this.key, passphrase).toString(
      Crypto.enc.Utf8
    );
    const privKeyArray = Uint8Array.from(
      Buffer.from(storedKey.replace("0x", ""), "hex")
    );
    return await DirectEthSecp256k1Wallet.fromKey(privKeyArray);
  }
```

| Name         | Type   | Description                          |
| ------------ | ------ | ------------------------------------ |
| `passphrase` | string | The passphrase of the wallet to get. |
