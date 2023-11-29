# User

### Available Commands <a href="#available-commands" id="available-commands"></a>

| Command                                | Description                                 |
| -------------------------------------- | ------------------------------------------- |
| [**getusername**](user.md#getusername) | Gets the username of the connected address. |
| [**register**](user.md#register)       | Registers a username for an address.        |

## GetUsername

Retrieves the username of the connected address.

#### Usage

```
user getusername
```

#### Example

```
? guides@galileo-3> user getusername
"dany"
```

## Register

Registers a username for the connected address.

#### Usage

```
user register
```

#### Example

```
? user@galileo-3> user register 
You already have "andr1g0c0y8prkhwrnffrw8afwdplv9wm5wgt9hdv7u" registered for your account
? Input the username: andromeda
? Would you like to add funds to this message? No
– Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 155251
Fee estimates:
   3882uandr

? Do you want to proceed? Yes
– Executing Tx...

Username claimed!

https://testnet-ping.wildsage.io/andromeda/tx/3DACC8DEFC3A01491D4B84E74837BE646A5547065C030861509D9874E077039C
? user@galileo-3> () 
```
