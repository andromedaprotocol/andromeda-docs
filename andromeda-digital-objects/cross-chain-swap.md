# Cross-Chain Swap

The **Cross-Chain-Swap** ADO allows swapping of tokens across cosmos chains. It utilizes already existing dexes to do so. The swapped funds can automatically be forwarded to an address specified by the user.&#x20;

Curently, [Osmosis](https://docs.osmosis.zone) is the only available dex to use.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum InstantiateMsg {
<strong>    pub kernel_address: String,
</strong>    pub owner: Option&#x3C;String>,
    }
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"kernel_address":"andr1...,
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name             | Type            | Description                                                                                                                                                                                                                                                                                                                   |
| ---------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `kernel_address` | String          | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>). |
| `owner`          | Option\<String> | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                                   |

## ExecuteMsg

### SwapAndForward

Swapps the attached funds to the specified funds and then forwards the swapped funds to the specified `forward_addr`.&#x20;

{% hint style="warning" %}
Currently, only osmosis dex is available to use for swapping.&#x20;

Make sure to attach one type of funds only.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    SwapAndForward {
        dex: String,
        to_denom: String,
        forward_addr: AndrAddr,
        forward_msg: Option<Binary>,
        slippage_percentage: Decimal,
        window_seconds: Option<u64>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"swap_and_forward"{
    "dex":"osmo",
    "to_denom":"ustars",
    "forward_addr":"andr1...",
    "slippage_percentage": "0.05",
    "window_seconds": 600
    }
}
```
{% endtab %}
{% endtabs %}

| Name                  | Type                                                           | Description                                                                                                                      |
| --------------------- | -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `dex`                 | String                                                         | The dex to use for swapping. Currently, only "osmo" can be specified.                                                            |
| `to_denom`            | String                                                         | The denom to swap to.                                                                                                            |
| `forward_addr`        | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | The address to send the funds to after a swap.                                                                                   |
| `forward_msg`         | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | An optional message to attach and send to the `forward_addr` along  with the funds. Can be used if the `forward_addr` is an ADO. |
| `slippage_percentage` | Decimal                                                        | The amount of [slippage](cross-chain-swap.md#slippage) allowed for the transaction.                                              |
| `window_seconds`      | Option\<u64>                                                   | Optional amount of time specified in seconds to get the required slippage. Defaults to one hour if not specified.                |

#### Slippage

Slippage refers to the difference between the expected price of a trade and the price at which the trade is executed. Low-liquidity pools result in a phenomenon called **slippage**, in which the cost of a trade is either much higher or lower than the trader expects.&#x20;

## QueryMsg

### &#x20;Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
