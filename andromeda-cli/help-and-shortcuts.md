# Help and Shortcuts

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

You will be prompted to select the wallet name. Finally send some tokens to your new address from the corresponding test-net faucet on discord.

### CLI Display

You will notice something similar to "`? $test@uni-5>"` in the CLI. This is not random but signifies which wallet and chain-Id are currently in use. It is formatted in the following way:

`? $<Wallet>@<ChainId>>`

### Help Flag

The help flag can be used after any command to get information on the current command.

#### Example:

```
? $test@uni-3> wallets --help
Usage:
   wallets [cmd]

Valid commands:
   add Adds a new wallet
   list Lists all added wallets
   rm Remove a wallet by address
   use Sets the default wallet to use

? $test@uni-3> wallets add --help
Adds a new wallet

Usage:
wallets add <name?>

Valid flags:
recover Recovers a wallet by mnemonic           
help    Displays info about the current command 

```

### Tab Based Suggestions&#x20;

In addition to the help flag, you can you the "tab" button on your keyboard to list the available commands or even autocomplete  a command.

#### Example:

To test this out, write any of the commands available but do not press enter to run it. Instead press tab and the Available commands should pop up.

```
? $main@elgafar-1> () wallets

//Press "tab" button
>> Available commands:
add     rm      use     list         
```

In this example, write the first letter of the command you want to run and see how the CLI will autocomplete it for you.&#x20;

```
? $main@elgafar-1> () wallets a

//Press "tab" button to autocomplete the command
? $main@elgafar-1> () wallets add
```

### Parent Shell Input

Our CLI accepts inputs from the parent shell using the andr keyword.

**Usage**

```
andr <command> 
```

#### Example

On my terminal I run:&#x20;

```
andr chain list 
```

#### Result

<pre><code><strong>⠂ Loading config...
</strong>⠂ Loading wallets...
– Connecting client...
Name        Chain ID 
junotestnet uni-5    
elgafar1    elgafar-1
galileo2    galileo-2
pisco1      pisco-1  </code></pre>

The command runs and the result is displayed but we are still in the parent shell.&#x20;
