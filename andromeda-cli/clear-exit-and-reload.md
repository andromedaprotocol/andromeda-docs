# Clear, Exit and Reload

## Clear

Clears the terminal.

#### Usage

```
clear
```

## Exit

Exits the CLI.

#### Usage

```
exit
```

#### Exit or Quit

exit and quit can be used to abort the current command.&#x20;

{% hint style="warning" %}
Any request inputs can be exited using one of the following inputs: .exit, .quit, .e, .q, exit
{% endhint %}

#### Example

```
 $main@uni-5> ado create cw721
- Fetching schema...
Input Minter: exit
Prompt exited
Use the --help flag for help
? $main@uni-5> () 
```

## Reload

Prints the previous commands.

#### Usage

```
relaod
```

#### Example

```
? $test@uni-3> reload
? Select from recent commands (or Close to exit)
 (Use arrow keys)
❯ wallets list  
  chain config 
  chain use uni3 
  cancel 
  exit 
```

{% hint style="warning" %}
Choose exit to exit the CLI entirely or cancel to cancel the command.
{% endhint %}
