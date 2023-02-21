# Help and Shortcuts

### Help Flag

The help flag can be used after any command to get information on it.

#### Example

```
? user@galileo-3> wallets add --help
Adds a new wallet. Can be used with the --recover flag to add a wallet by mnemonic.

Usage:
wallets add <name>

Valid flags:
recover Recovers a wallet by mnemonic           
help    Displays info about the current command 

Any request inputs can be exited using one of the following inputs: .exit, .quit, .e, .q, exit
Documentation can be found here: https://docs.andromedaprotocol.io/andromeda/andromeda-cli/


```

### Autofill Suggestions&#x20;

In addition to the help flag, you can you the "tab" button on your keyboard to list the available commands or even autocomplete  a command.

#### Example

To test this out, write any of the commands available but do not press enter to run it. Instead press tab and the "Available commands" should pop up.

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

### Direct Shell Input

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

```
– Connecting client...
Name        Chain ID     
elgafar1    elgafar-1    
pisco1      pisco-1      
sei-network atlantic-1   
injective   injective-888
galileo3    galileo-3    
junotestnet uni-6        
```

The command runs and the result is displayed but we are still in the parent shell.&#x20;

### Version

Prints the current CLI version

#### Example

```
? user@galileo-3> version
Version: 0.1.9
? user@galileo-3> () 
```
