---
description: This document is a guide to start using the Andromeda CLI.
---

# Introduction

{% hint style="warning" %}
The CLI is still in beta phase. It may contain bugs and upgrades will probably be released weekly.
{% endhint %}

## Installation

To install the Andromeda CLI, run the following command:

{% hint style="warning" %}
Make sure you have npm installed.
{% endhint %}

```
npm install -g @andromedaprotocol/andromeda.js
```

Then run `andr`  to open the CLI:

![Andromeda CLI ](../.gitbook/assets/ANDROMEDA-CLI.png)

### CLI Commands

Now that we have the CLI open, we can start exploring some of its functions which include:

* Generating Keys and managing wallets.
* Swap between chains with one command.
* Interact with the set chain (Deploy, instantiate, execute ...)
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

You will be prompted to select the wallet name. Send some tokens to your new address from the corresponding test-net faucet on discord.

{% hint style="warning" %}
If you already have an address, you can import it to the CLI using the [recover flag. ](wallets.md#flags)
{% endhint %}

### CLI Display

You will notice something similar to "`? $test@uni-5>"` in the CLI. This is not random but signifies which wallet and chain-Id are currently in use. It is formatted in the following way:

`? $<Wallet>@<ChainId>>`

### Starting Exploring

We can start off by running `andr --help` to see what are the available commands.&#x20;

```
andr --help 
```

Or just:

```
help
```

You will get a list of available commands to use:

| Command                                   | Description                               |
| ----------------------------------------- | ----------------------------------------- |
| ****[**ado**](ado.md)****                 | Query and execute on an ADO               |
| ****[**chain**](chain.md)****             | Manage Chain Config                       |
| ****[**clear**](clear-and-exit.md)****    | Clears the terminal                       |
| ****[**exit**](clear-and-exit.md)****     | Exits the CLI                             |
| ****[**gql**](gql.md)****                 | Query using the Andromeda GraphQL service |
| ****[**help**](help-and-shortcuts.md)**** | Lists all valid commands                  |
| ****[**tx**](tx.md)****                   | Query transactions                        |
| ****[**wallets**](wallets.md)****         | Manage wallets                            |
| ****[**wasm**](wasm.md)****               | Send CosmWasm messages to the chain       |

{% hint style="warning" %}
It is important to note that the CLI has been designed to prompt the user for the required fields in each command. This means that it is sufficient to write the command without the fields and the CLI will prompt you to fill in the inputs one by one instead of throwing an error.&#x20;
{% endhint %}
