---
description: The wallets handler allows you to manage your locally stored wallet keys
---

# Wallet

### Available Commands

| Command                            | Description                     |
| ---------------------------------- | ------------------------------- |
| [**add**](wallet.md#wallets-add)   | Adds a new wallet               |
| [**list**](wallet.md#wallets-list) | Lists all added wallets         |
| [**rm**](wallet.md#wallets-rm)     | Removes a wallet by address     |
| [**use**](wallet.md#wallets-use)   | Sets the default wallet to use  |

## Wallet Add

Creates and adds a new wallet to the list of wallets.

#### Usage

```typescript
wallet add <name>
```

{% hint style="warning" %}
You cannot have two wallets with the same name including wallets on different chain configs.
{% endhint %}

#### Example

```
wallet add test
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

Use the specific chain faucet to send tokens to the address for **test-net** wallets..
{% endhint %}

You have created a new wallet address. To view the address we would need to list our wallets.

#### Recover an existing key from seed phrase

If you forget your password or lose your key, or you would like to use your key in another place, you can recover your key by using the `--recover` flag.

The following example will recover a key with the seed phrase and store it in the local keystore with the name test:

```
wallet add test --recover
```

You'll be asked to enter and repeat the new password for your key, and enter the seed phrase. Then you get your key back.

#### Flags

<table><thead><tr><th>Flag</th><th width="369">Description</th><th>Usage</th></tr></thead><tbody><tr><td><strong>recover</strong></td><td>Recovers a wallet by mnemonic.</td><td>--recover</td></tr><tr><td><strong>help</strong></td><td>Displays info about the current command.</td><td>--help</td></tr></tbody></table>

## Wallet List

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
wallet add test2
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

## Wallets rm

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

## Wallet Use

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
