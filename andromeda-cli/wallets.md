---
description: The wallets handler allows you to manage your locally stored wallet keys
---

# Wallets

### Available Commands

| Command                                     | Description                     |
| ------------------------------------------- | ------------------------------- |
| ****[**add**](wallets.md#wallets-add)****   | Adds a new wallet               |
| ****[**list**](wallets.md#wallets-list)**** | Lists all added wallets         |
| ****[**rm**](wallets.md#wallets-rm)****     | Removes a wallet by address     |
| ****[**use**](wallets.md#wallets-use)****   | Sets the default wallet to use  |

## Wallets Add

Creates and adds a new wallet to the list of wallets.

#### Usage

```typescript
wallets add <name>
```

{% hint style="warning" %}
You cannot have two wallets with the same name including wallets on different chain configs.
{% endhint %}

#### Example

```
wallets add test
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
wallets add test --recover
```

You'll be asked to enter and repeat the new password for your key, and enter the seed phrase. Then you get your key back.

#### Flags

| Flag        | Description                              | Usage     |
| ----------- | ---------------------------------------- | --------- |
| **recover** | Recovers a wallet by mnemonic.           | --recover |
| **help**    | Displays info about the current command. | --help    |

## Wallets List

Lists all the added wallets.

#### Usage

```
wallets list 
```

We get:

```
*  test juno12x7efvfn94ynqkyvdcgdndh6rqrz6u6x20jes2
```

Add another wallet and try again:

```
wallets add test2
```

```
wallets list
```

Result:

```
*  test juno12x7efvfn94ynqkyvdcgdndh6rqrz6u6x20jes2
   test2 juno1sra207nk6uua39dg7g5nfscnfptjmdhyzexycj
```

{% hint style="warning" %}
The asterisk will show you which address is currently being used
{% endhint %}

## Wallets rm

Remove a specified wallet from the list.

#### Usage

```
wallet rm <wallet-name>
```

or just:&#x20;

```
wallets rm 
```

which will prompt you to chose which wallet you want to remove.

#### Example

```
test@uni-6> wallets list
*  test juno12x7efvfn94ynqkyvdcgdndh6rqrz6u6x20jes2
   test2 juno1sra207nk6uua39dg7g5nfscnfptjmdhyzexycj
```

```
wallets rm test2 
```

You will be promted to confirm this:

```
Are you sure you want to remove wallet test2? (Y/n) y
```

test2 wallet has been deleted:

```
$test@uni-3> wallets list
*  test juno12x7efvfn94ynqkyvdcgdndh6rqrz6u6x20jes2
```

## Wallets Use

Set the default wallet to be used.

#### Usage

```
wallets use <wallet-name>
```

#### Example

```
//list the wallets

? $test@uni-6> wallets list
*  test juno1ygp0dvp7gccyk2vja6p7lr7f9ulasr2h4shhx5
   test2 juno14vdzdygwg4p0l8d6ezrcsczsjy5eql79z57mvn
   
//Chose to use second wallet

? $test@uni-6> wallets use test2

//Check if the in use wallet has changed 

? $test2@uni-6> wallets list
   test juno1ygp0dvp7gccyk2vja6p7lr7f9ulasr2h4shhx5
*  test2 juno14vdzdygwg4p0l8d6ezrcsczsjy5eql79z57mvn
```
