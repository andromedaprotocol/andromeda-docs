---
description: The wallets handler allows you to manage your locally stored wallet keys
---

# Wallet

### Available Commands

| Command                                        | Description                           |
| ---------------------------------------------- | ------------------------------------- |
| [**autosave**](wallet.md#wallet-autosave)      | Save the wallet password.             |
| [**generate**](wallet.md#wallet-generate)      | Generate a new wallet                 |
| [**recover**](wallet.md#recover)               | Import an existing wallet to the CLI. |
| [**rename**](wallet.md#rename)                 | Reassign the name of a wallet.        |
| [**reveal**](wallet.md#reveal)                 | Reveal the mnemonic for a wallet.     |
| [**list**](wallet.md#wallets-list)             | Lists all added wallets               |
| [**migrate-legacy**](wallet.md#migrate-legacy) | Migrate wallets from old CLI (V1)     |
| [**rm**](wallet.md#wallets-rm)                 | Removes a wallet by address           |
| [**use**](wallet.md#wallets-use)               | Sets the default wallet to use        |

## Autosave

Store the wallet password in keychain, no longer requiring a password when switching to the wallet.

{% hint style="warning" %}
Autosave is enabled by default.
{% endhint %}

**Usage**

```
wallet autosave
```

Then select the wallet:

<figure><img src="../.gitbook/assets/Screenshot 2024-09-30 at 9.07.18 PM.png" alt=""><figcaption></figcaption></figure>

Then you can choose to enable or disable this feature:

<figure><img src="../.gitbook/assets/Screenshot 2024-09-30 at 9.07.31 PM.png" alt=""><figcaption></figcaption></figure>

When disabled, then you will be required to enter your password every time you switch back to this wallet.

## Generate

Creates and adds a new wallet to the list of wallets.

#### Usage

```typescript
wallet generate <name>
```

{% hint style="warning" %}
You cannot have two wallets with the same name including wallets on different chains.
{% endhint %}

#### Example

```
wallet generate test
```

The above command will prompt you to enter a passphrase for your account and then generate an output similar to:

```
Wallet added!

Your seed phrase is:
"Your seed phrase will be displayed here" 

Do not share this with anyone. Please make sure to store this for future reference, without it you cannot recover your wallet.
```

{% hint style="warning" %}
Note: The output of the above command contains a _**seed phrase**_. It's recommended to save the _seed phrase_ in a safe place to avoid losing it.

Use the specific chain faucet to send tokens to the address for **testnet** wallets..
{% endhint %}

You have created a new wallet address. To view the address we would need to list our wallets.

## Recover

Import an existing wallet using the mnemonic.

**Usage**

```
wallet recover <wallet-name>
```

**Example**

```
? user@galileo-4> wallet recover testing-2
```

You will be prompted to add a passphrase for the wallet. Finally input the mnemonic of the wallet being imported. You will now be connected to the imported wallet:

```
? testing-2@galileo-4> () 
```

## Rename

Assign a new name to the connected wallet.

**Usage**

```
wallet rename 
```

**Example**

```
? new-name@galileo-4> wallet rename 
? New Name for wallet: new-name-2

// The name has been changed 
? new-name-2@galileo-4> () 
```

## Reveal

Reveal the mnemonic key for one of your wallets.&#x20;

{% hint style="warning" %}
You will always have to input your passphrase to be able to view the mnemonic.
{% endhint %}

**Usage**

```
wallet reveal <wallet-name>
```

**Example**

<figure><img src="../.gitbook/assets/Screenshot 2024-09-30 at 9.58.06 PM.png" alt=""><figcaption></figcaption></figure>

## List

Lists all the added wallets.

{% hint style="warning" %}
The asterisk will show you which address is currently being used
{% endhint %}

#### Usage

```
wallet list 
```

We get:

```
? main-star@elgafar-1> wallet list
*  main-star stars1dtlndrtpth240retkn8l54w8gg2g4hzpq3ugas
   testing-star stars12xxey4enkcfgv522cxl03xmk7tdpmy6kcvtaql
   uploader stars1zf6avrfjnrztjnvj2as266eeqlfc7kggzedmcc
   user55 stars1x0vwc9j3mzn47zrmgt25kjfzpevyvc5t652vt7
   keplr-star stars105j9m29uk2486rjwawkcucp8e3l52f8wpgyc2c
   guide stars14pmn28jyqgphd5wv0z28ppxe5ryeraqqyhkuas
```

Add another wallet and try again:

```
wallet generate test2
```

```
wallet list
```

Result:

```
*  main-star stars1dtlndrtpth240retkn8l54w8gg2g4hzpq3ugas
   testing-star stars12xxey4enkcfgv522cxl03xmk7tdpmy6kcvtaql
   uploader stars1zf6avrfjnrztjnvj2as266eeqlfc7kggzedmcc
   user55 stars1x0vwc9j3mzn47zrmgt25kjfzpevyvc5t652vt7
   keplr-star stars105j9m29uk2486rjwawkcucp8e3l52f8wpgyc2c
   guide stars14pmn28jyqgphd5wv0z28ppxe5ryeraqqyhkuas
   //new wallet created
   test2 stars1frjemlcs0s7n23swxx43g5a20nzymqxpgpwwht
```

## Migrate Legacy

Migrates a wallet from an older version of CLI.&#x20;

{% hint style="warning" %}
This command might be removed in the future after migration by users have been successfully completed.&#x20;

Migrating is from v1 to v2 of CLI. You can check the version of your CLI by running "version".
{% endhint %}

**Usage**

```
wallet migrate-legacy
```

Then select the wallet to migrate to the new CLI version:

<figure><img src="../.gitbook/assets/Screenshot 2024-09-30 at 10.07.58 PM.png" alt=""><figcaption></figcaption></figure>

You will then need to provide the passphrase for the wallet. After that the wallet will be added to your list.

## rm

Remove a specified wallet from the list.

#### Usage

```
wallet rm <wallet-name>
```

or just:&#x20;

```
wallet rm 
```

which will prompt you to chose which wallet you want to remove.

#### Example

```
? main-star@elgafar-1> wallet list
*  main-star stars1dtlndrtpth240retkn8l54w8gg2g4hzpq3ugas
   testing-star stars12xxey4enkcfgv522cxl03xmk7tdpmy6kcvtaql
   uploader stars1zf6avrfjnrztjnvj2as266eeqlfc7kggzedmcc
   user55 stars1x0vwc9j3mzn47zrmgt25kjfzpevyvc5t652vt7
   keplr-star stars105j9m29uk2486rjwawkcucp8e3l52f8wpgyc2c
   guide stars14pmn28jyqgphd5wv0z28ppxe5ryeraqqyhkuas
   test2 stars1frjemlcs0s7n23swxx43g5a20nzymqxpgpwwht
```

```
wallet rm test2 
```

You will be promted to confirm this:

```
Are you sure you want to remove wallet test2? (Y/n) y
```

test2 wallet has been deleted:

```
? main-star@elgafar-1> wallet list
*  main-star stars1dtlndrtpth240retkn8l54w8gg2g4hzpq3ugas
   testing-star stars12xxey4enkcfgv522cxl03xmk7tdpmy6kcvtaql
   uploader stars1zf6avrfjnrztjnvj2as266eeqlfc7kggzedmcc
   user55 stars1x0vwc9j3mzn47zrmgt25kjfzpevyvc5t652vt7
   keplr-star stars105j9m29uk2486rjwawkcucp8e3l52f8wpgyc2c
   guide stars14pmn28jyqgphd5wv0z28ppxe5ryeraqqyhkuas
```

## Use

Set the default wallet to be used.

#### Usage

```
wallet use <wallet-name>
```

#### Example

```
//list the wallets

? guide@elgafar-1> wallet list 
   main-star stars1dtlndrtpth240retkn8l54w8gg2g4hzpq3ugas
   testing-star stars12xxey4enkcfgv522cxl03xmk7tdpmy6kcvtaql
   uploader stars1zf6avrfjnrztjnvj2as266eeqlfc7kggzedmcc
   user55 stars1x0vwc9j3mzn47zrmgt25kjfzpevyvc5t652vt7
   keplr-star stars105j9m29uk2486rjwawkcucp8e3l52f8wpgyc2c
*  guide stars14pmn28jyqgphd5wv0z28ppxe5ryeraqqyhkuas
   
//Chose to use another wallet 

? guide@elgafar-1> wallet use main-star

//Check if the in use wallet has changed 

? main-star@elgafar-1> wallet list
*  main-star stars1dtlndrtpth240retkn8l54w8gg2g4hzpq3ugas
   testing-star stars12xxey4enkcfgv522cxl03xmk7tdpmy6kcvtaql
   uploader stars1zf6avrfjnrztjnvj2as266eeqlfc7kggzedmcc
   user55 stars1x0vwc9j3mzn47zrmgt25kjfzpevyvc5t652vt7
   keplr-star stars105j9m29uk2486rjwawkcucp8e3l52f8wpgyc2c
   guide stars14pmn28jyqgphd5wv0z28ppxe5ryeraqqyhkuas
```
