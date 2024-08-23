# TerraWallet

Class to generate a wallet to to be used on the Terra chain.&#x20;

## Class

```typescript
export default class TerraWallet extends Wallet 
```

## Methods

### fromMnemonic

Generates a new wallet from a given mnemonic.

```typescript
static async fromMnemonic(
    name: string,
    mnemonic: string,
    passphrase: string
  ): Promise<Wallet> {
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, {
      hdPaths: [stringToPath("m/44'/330'/0'/0/0")],
      prefix: "terra",
    });
    const key = await wallet.serialize(passphrase);
    return new Wallet(name, key, "terra");
  }
```

| Name         | Type   | Description                                       |
| ------------ | ------ | ------------------------------------------------- |
| `name`       | string | The name to assign for the wallet.                |
| `mnemonic`   | string | The mnemonic used to generate the wallet address. |
| `passphrase` | string | The passphrase for the wallet.                    |
