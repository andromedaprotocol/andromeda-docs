---
description: This document is a guide to start using the Andromeda CLI.
---

# Introduction

{% hint style="warning" %}
The CLI is still in beta phase. It may contain bugs and upgrades will probably be released weekly.
{% endhint %}

## Installation

To install the Andromeda CLI, run the following command to download our[ npm package](https://www.npmjs.com/package/@andromedaprotocol/andromeda.js/v/0.1.3):

{% hint style="warning" %}
Make sure you have npm installed.
{% endhint %}

```
sudo npm install -g @andromedaprotocol/andromeda.js@0.1.6
```

{% hint style="warning" %}
We use sudo since we are installing globally using the -g flag.
{% endhint %}

If you have the package already installed, you can the following to update to the lates version:

```
sudo npm update -g @andromedaprotocol/andromeda.js
```

Then run `andr`  to open the CLI:

![Andromeda CLI ](../.gitbook/assets/ANDROMEDA-CLI.png)

{% hint style="warning" %}
You can run "`version"` in the CLI to check the CLI version you have downloaded.
{% endhint %}

### CLI Commands

Now that we have the CLI open, we can start exploring some of its functions which include:

* Generating keys and managing wallets.
* Swap between chains with one command.
* Interact with the chain of your choice
* Query using our GraphQL server.

{% hint style="info" %}
The Andromeda CLI is built to work with any of the chains that Andromeda is deployed on. This would remove the need for users to learn how to deploy contracts on each of the chains allowing them to use this CLI to interact with ADOs anywhere on the Cosmos ecosystem.&#x20;
{% endhint %}

### Deployed Chains

Andromeda is currently deployed on the following chains:

* Andromeda
* Juno
* Stargaze
* Terra

### Getting Started

The first thing to do in the CLI is to choose the chain config and wallets to use.&#x20;

Run:&#x20;

```
chain use 
```

You will be prompted to choose a chain config from a list:

{% hint style="info" %}
The chain Ids are the same as the ones for the testnets they correspond to.

You can also create your own chain config using the [chain new](chain.md#new) command.
{% endhint %}

```
 Input the chain ID to use: (Use arrow keys)
❯ uni-5 
  elgafar-1 
  galileo-2 
  pisco-1 
  exit 
```

Choose the chain config that you want to use.

Then we need to add a wallet. We run:

```
wallets add 
```

{% hint style="warning" %}
Make sure to save the **seed phrase** somewhere private and secure as it is the only way to recover your wallet.
{% endhint %}

You will be prompted to select the wallet name.&#x20;

{% hint style="warning" %}
If you are using a test-net config make sure to send some tokens to your new address from the corresponding test-net faucet on discord.

If you already have an address, you can import it to the CLI using the [recover flag. ](wallets.md#flags)
{% endhint %}

### CLI Display

You will notice something similar to "`? $test@uni-5>"` in the CLI. This signifies which wallet and chain-Id are currently in use. It is formatted in the following way:

`? $<Wallet>@<ChainId>>`

### Start Exploring

We can start off by running `andr --help` to see what are the available commands.&#x20;

```
andr --help 
```

Or just:

```
help
```

{% hint style="warning" %}
The --help flag  can be used after any command to get information on the command.
{% endhint %}

You will get a list of available commands to use:

| Command                                              | Description                               |
| ---------------------------------------------------- | ----------------------------------------- |
| ****[**ado**](ado.md)****                            | Query and execute on an ADO               |
| ****[**chain**](chain.md)****                        | Manage Chain Config                       |
| ****[**clear**](clear-and-exit.md)****               | Clears the terminal                       |
| ****[**exit**](clear-and-exit.md)****                | Exits the CLI                             |
| ****[**gql**](gql.md)****                            | Query using the Andromeda GraphQL service |
| ****[**help**](help-and-shortcuts.md)****            | Lists all valid commands                  |
| ****[**tx**](tx.md)****                              | Query transactions                        |
| ****[**version**](help-and-shortcuts.md#version)**** | Prints the current CLI version.           |
| ****[**wallets**](wallets.md)****                    | Manage wallets                            |
| ****[**wasm**](wasm.md)****                          | Send CosmWasm messages to the chain       |

{% hint style="warning" %}
It is important to note that the CLI has been designed to prompt the user for the required fields in each command. This means that it is sufficient to write the command without the fields and the CLI will prompt you to fill in the inputs one by one instead of throwing an error.&#x20;
{% endhint %}
