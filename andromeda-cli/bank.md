# Bank

### Available Commands

| Command                        | Description                      |
| ------------------------------ | -------------------------------- |
| [**balance**](bank.md#balance) | Gets the balance for an address. |
| [**send**](bank.md#send)       | Sends tokens to another address. |

## Balance

Gets the balance for an address.

#### Usage

```
bank balance <denom> <address>
```

{% hint style="warning" %}
Leaving the address field empty when prompted for it will get the balance of the current wallet being used.
{% endhint %}

#### Example

<pre><code>? keplr-g3@galileo-3> bank balance uandr <a data-footnote-ref href="#user-content-fn-1">andr105j9m29uk2486rjwawkcucp8e3l52f8wdlm8ay</a>

Balance
uandr: 9048072
? keplr-g3@galileo-3> () 

<a data-footnote-ref href="#user-content-fn-2">/// Not specifying the address will automatically take the connected address</a>
guides@galileo-3> bank balance
? Input Denom: uandr
? Input Address: 
Balance
uandr: 101771266
</code></pre>

## Send

Sends tokens to another address.

#### Usage

```
bank send <recipient address> <amount>
```

{% hint style="warning" %}
Make sure to include the denom with the amount.&#x20;
{% endhint %}

#### **Example**

```
? keplr-g3@galileo-3> bank send andr1g0c0y8prkhwrnffrw8afwdplv9wm5wgt9hdv7u 500uandr
? Are you sure you want to send 500uandr to andr1g0c0y8prkhwrnffrw8afwdplv9wm5wgt9hdv7u? Yes
— Sending tokens...

Sent 500uandr to andr1g0c0y8prkhwrnffrw8afwdplv9wm5wgt9hdv7u!
https://testnets.cosmosrun.info/andromeda-galileo-3/tx/81C2F2BF1A900D2BE4DC82CB11E5B5ADB4559F8C67DDF9539C6C7DE6CBA85996
? keplr-g3@galileo-3> () 
```

[^1]: 

[^2]: 
