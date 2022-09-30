# Bank

### Available Commands

| Command     | Description                      |
| ----------- | -------------------------------- |
| **balance** | Gets the balance for an address. |
| **send**    | Sends tokens to another address. |

## Bank

Gets the balance for an address.

#### Usage

```
bank balance <denom?> <address>
```

#### Example

```
? $main@uni-5> bank balance ujunox juno1zkpthqsz3ud97fm6p4kxcra8ae99jgzauugyem

Balance
ujunox: 3598667
? $main@uni-5> () 
```

## Send

Sends tokens to another address.

#### Usage:

```
bank send <recipient address> <amount>
```

{% hint style="warning" %}
Make sure to include the denom with the amount.&#x20;
{% endhint %}

#### **Example:**

```
bank send juno12x7efvfn94ynqkyvdcgdndh6rqrz6u6x20jes2 6000ujunox
```
