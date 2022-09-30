---
description: >-
  The wallets module allows you to manage your local tendermint keystore
  ("wallets") for the juno network.
---

# Wallets

### Available Commands

| Command  | Description                     |
| -------- | ------------------------------- |
| **add**  | Adds a new wallet               |
| **list** | Lists all added wallets         |
| **rm**   | Removes a wallet by address     |
| **use**  | Sets the default wallet to use  |

## Wallets Add

Creates and adds a new wallet to the list of wallets.

#### Usage

```typescript
wallets add <name>
```

#### Example

```
wallets add test
```

The above command will generate an output similar to:

```
Wallet added!

Your seed phrase is:
tortoise height lake subject scene always parent circle illness swift input quality jeans fall fragile year mind broccoli believe sugar page horse deliver case

Do not share this with anyone. Please make sure to store this for future reference, without it you cannot recover your wallet.
```

{% hint style="warning" %}
Note: The output of the above command contains a _**seed phrase**_. It's recommended to save the _seed phrase_ in a safe place to avoid losing it.

Use the specific chain faucet to send tokens to the address.
{% endhint %}

You have created a new wallet address. To view the address we would need to list our wallets.

#### Flags:

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
test@uni-3> wallets list
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

? $test@uni-3> wallets list
*  test juno1ygp0dvp7gccyk2vja6p7lr7f9ulasr2h4shhx5
   test2 juno14vdzdygwg4p0l8d6ezrcsczsjy5eql79z57mvn
   
//Chose to use second wallet

? $test@uni-3> wallets use test2

//Check if the in use wallet has changed 

? $test2@uni-3> wallets list
   test juno1ygp0dvp7gccyk2vja6p7lr7f9ulasr2h4shhx5
*  test2 juno14vdzdygwg4p0l8d6ezrcsczsjy5eql79z57mvn
```
