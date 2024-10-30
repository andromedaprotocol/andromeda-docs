---
description: This document is a guide to start using the Andromeda CLI.
---

# Introduction

## Installation

To install the Andromeda CLI, run the following command to download our [npm package](https://www.npmjs.com/package/@andromedaprotocol/cli):

{% hint style="warning" %}
Make sure you have npm installed.
{% endhint %}

<pre><code><strong>sudo npm i -g @andromedaprotocol/cli
</strong></code></pre>

{% hint style="warning" %}
We use sudo since we are installing globally using the -g flag.
{% endhint %}

If you have the package already installed, you can run the following to update to the latest version:

```
sudo npm update -g @andromedaprotocol/cli
```

Then run `andr`  to open the CLI:

![Andromeda CLI ](../.gitbook/assets/ANDROMEDA-CLI.png)

{% hint style="warning" %}
You can run "`version"` in the CLI to check the CLI version you have downloaded.
{% endhint %}

### CLI Commands

Now that we have the CLI open, we can start exploring some of its functions which include:

* Generating keys and managing wallets
* Swap between chains with one command
* Interact with the chain of your choice
* Query using our GraphQL server

{% hint style="warning" %}
The Andromeda CLI is built to work with any of the chains that Andromeda is deployed on. This would remove the need for users to learn how to deploy contracts on each of the chains allowing them to use this CLI to interact with ADOs anywhere on the Cosmos ecosystem.&#x20;
{% endhint %}

### Deployed Chains

To get the list of chains that are integrated with the Andromeda system, run:

```
chain list
```

<figure><img src="../.gitbook/assets/Screenshot 2024-07-23 at 5.56.07 PM.png" alt="" width="563"><figcaption></figcaption></figure>

### Getting Started

#### Selecting the Environment&#x20;

The first thing to do in the CLI is to choose which environment to use. There are three main environments:

* **Mainnet:** Connects to the mainnet chain configs.
* **Testnet:** Connects to testnet chain configs.
* **Devnet:** Connects to devnet chain configs. Used mainly in Andromeda dev events such as hackathons and beta testing.

You can run the following command and then select from the displayed envs:

```
env use
```

#### Selecting the Chain

Next, you need to select the chain config and wallets to use.&#x20;

**Run:**&#x20;

```
chain use 
```

You will be prompted to choose a chain config from a list:

{% hint style="info" %}
The chain Ids are the same as the ones for the testnets they correspond to.

You can also create your own chain config using the [chain new](chain.md#new) command.
{% endhint %}

```
? Input the chain ID to use: (Use arrow keys or type to search, tab to autocomplete)
❯ andromeda 
  terra 
  archway  
```

Choose the chain config that you want to use. Then add a wallet:

{% hint style="warning" %}
This creates a new wallet address. If you already have an address, you can import it to the CLI using the "wallet recover" command.
{% endhint %}

```
wallet generate
```

{% hint style="warning" %}
Make sure to save the **seed phrase** somewhere private and secure as it is the only way to recover your wallet.
{% endhint %}

You will be prompted to select the wallet name.&#x20;

{% hint style="warning" %}
If you are using a testnet config make sure to send some tokens to your new address from the corresponding testnet faucet.
{% endhint %}

### CLI Display

You will notice something similar to `? user@galileo-3>` in the CLI. This signifies which wallet and chain-Id are currently in use. It is formatted in the following way:

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

<table><thead><tr><th width="234.5">Command</th><th>Description</th></tr></thead><tbody><tr><td><a href="ado.md"><strong>ado</strong></a></td><td>Query and execute messages on an ADO.</td></tr><tr><td><a href="bank.md"><strong>bank</strong></a></td><td>Send tokens or query address balances.</td></tr><tr><td><a href="chain.md"><strong>chain</strong></a></td><td>Manage the chains and their configs.</td></tr><tr><td><a href="clear-and-exit.md"><strong>clear</strong></a></td><td>Clears the terminal.</td></tr><tr><td><a href="env.md"><strong>env</strong></a></td><td>Manage your environments.</td></tr><tr><td><a href="clear-and-exit.md"><strong>exit</strong></a></td><td>Exits the CLI.</td></tr><tr><td><a href="gql.md"><strong>gql</strong></a></td><td>Query Apps and assets using the Andromeda GraphQL service.</td></tr><tr><td><a href="help-and-shortcuts.md"><strong>help</strong></a></td><td>Lists all valid commands.</td></tr><tr><td><a href="tx.md"><strong>tx</strong></a></td><td>Query transactions information.</td></tr><tr><td><a href="os.md"><strong>os</strong></a></td><td>Execute and Query messages on the OS ADOs like the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">Kernel</a>, <a href="../platform-and-framework/andromeda-messaging-protocol/virtual-file-system.md">VFS</a>, and <a href="../platform-and-framework/andromeda-messaging-protocol/andromeda-factory.md">ADODB</a>.</td></tr><tr><td><a href="help-and-shortcuts.md#version"><strong>version</strong></a></td><td>Prints the current CLI version.</td></tr><tr><td><a href="wallet.md"><strong>wallet</strong></a></td><td>Manage wallets.</td></tr><tr><td><a href="wasm.md"><strong>wasm</strong></a></td><td>Send messages to the chain using wasm.</td></tr></tbody></table>

{% hint style="warning" %}
It is important to note that the CLI has been designed to prompt the user for the required fields in each command. This means that it is sufficient to write the command without the fields and the CLI will prompt you to fill in the inputs one by one instead of throwing an error.&#x20;
{% endhint %}
