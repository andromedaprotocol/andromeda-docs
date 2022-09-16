# Wrapped-NFT App

If unfimailar with the steps of deploying an app, go back to the [first example](crowdfunding-app.md) where we explain in detail all the different parts of deploying an App.

{% hint style="warning" %}
If any of the messages in this example do not work, you might want to cross reference the messages with the ADO specific section which always contains the latest ADO versions to make sure they are correct. Other than that the logic will remain the same.
{% endhint %}

### **Defining our App**

For this example, we will be utilizing our [wrapped-nft ADO](../andromeda-digital-objects/wrapped-cw721.md) to wrap a cw721-base nft and leverage the andromeda [TransferAgreement](../andromeda-digital-objects/andromeda-digital-object.md#transferagreement-1) functionality to sell the token.&#x20;

{% hint style="info" %}
We will not build this example using the App contract.
{% endhint %}

The steps we will perform:&#x20;

1. Mint a token using the cw721-base contract found in [cw-nfts repo.](https://github.com/CosmWasm/cw-nfts)
2. Send it to wrapper ADO to have it wrapped.
3. Apply a TransferAgreement for the token.&#x20;
4. Buy the token using another address.&#x20;
5. Unwrap the token to get the original back.&#x20;

We will need to use the following andromeda contracts in addition to the base-nft contract:

* [Andromeda wrapped-nft ](../andromeda-digital-objects/wrapped-cw721.md)
* [Andromeda cw721](../andromeda-digital-objects/andromeda-digital-object.md)

### Instantiating the CW-721 base NFT

First we need to mint a regular nft from the cw-nft repo. To do this, we first need to clone the repo, build the contract and upload it to juno uni-3 testnet. I have done this and the code-id you can use is 2240.&#x20;

### Instantiate

The instantiation message for cw-721 base nft is the following:

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub name: String,
    pub symbol: String,
    pub minter: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"name":"wrapped token",
"symbol":"WT",
"minter":"juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll"
}
```
{% endtab %}
{% endtabs %}

#### Instantiate

```
junod tx wasm instantiate 2240 '{"name":"test","symbol":"TST","minter":"juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll"}' --label "wrapped" --from  juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y --no-admin
```

[Instantiate nft contract](https://testnet.mintscan.io/juno-testnet/txs/6F0EC3F75CCA38AE9B1D7E881A96FA798DBD2D09AD6D9AB2BD9848AC65EE58B8)

#### Mint an NFT&#x20;

We will mint an NFT with token\_id specified as 1.

```json
{
"mint":{
"token_id":"1",
"owner":"juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll"
   }
}
```

```
junod tx wasm execute juno134hgkjxzftmttgg5w5v79wmm0mdwn0y04s5pyy4lwekkk8fsucqsk3xtqc '{"mint":{"token_id":"1","owner":"juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll"}}'  --from juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y
```

[Mint](https://testnet.mintscan.io/juno-testnet/txs/75BF010F64DDDB1C53B0A838DBAF2C58989EC56A5938CFE5683FCB0E3CED22C6)

### Instantiate Wrapped-NFT ADO.

We already have uploaded a wrapped-nft ado with code-id 2196. We can instantiate it using the message below.

```json
{
"primitive_contract":"juno144r95jrnk5erqhpuepe5mwz33e5mns7yuyhmxw7fmfx8w0duqwws9qd36x",
"cw721_instantiate_type":{
    "new":{
        "name":"test-wrap",
        "symbol":"TST"
        }
    },
"can_unwrap": true
}
```

```
junod tx wasm instantiate 2196 '{"primitive_contract":"juno144r95jrnk5erqhpuepe5mwz33e5mns7yuyhmxw7fmfx8w0duqwws9qd36x","cw721_instantiate_type":{"new":{"name":"test-wrap","symbol":"TST"}},"can_unwrap": true}' --label "wrapped-nft" --from  juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y --no-admin
```

[Instantiate wrapped](https://testnet.mintscan.io/juno-testnet/txs/8D4E8728843C26C7C5048958624718441E8297B4895396C1A542ED4F0710D9F0)

{% hint style="info" %}
When we instantiate the wrapped-nft contract, we specified the cw721 type as new which means that we have also instantiated an andromeda cw721 contract.
{% endhint %}

#### Contracts:

Let us list the contracts thus far to avoid confusion:

* **cw721-base:** juno134hgkjxzftmttgg5w5v79wmm0mdwn0y04s5pyy4lwekkk8fsucqsk3xtqc
* **wrapped-ado:** juno1u4jg78rt9ruulptgk5xluz3r2gpt9uurnuvv9h6zedszk6y6qu4qwp43jj
* **ado-cw721:**juno1q2m6f79j6wpcfw7v4lzlwe5x5u0utptjd28lsvdtl6pslwhur8yqzyvyzj

Now we have everything set up, we can send the token we minted earlier to get wrapped by our ADO.&#x20;

#### Wrap the NFT

```json
{
    "send_nft": {
        "contract": "juno1u4jg78rt9ruulptgk5xluz3r2gpt9uurnuvv9h6zedszk6y6qu4qwp43jj",
        "token_id": "1",
        "msg":"eyJ3cmFwIjp7fX0="
    }
}
```

Here the base64 encoded message is the following:

```json
{"wrap":{}}
```

We run:

```
junod tx wasm execute juno134hgkjxzftmttgg5w5v79wmm0mdwn0y04s5pyy4lwekkk8fsucqsk3xtqc '{"send_nft":{"contract":"juno1u4jg78rt9ruulptgk5xluz3r2gpt9uurnuvv9h6zedszk6y6qu4qwp43jj","token_id":"1","msg":"eyJ3cmFwIjp7fX0="}}'  --from juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y
```

[Wrap NFT](https://testnet.mintscan.io/juno-testnet/txs/BF5F8D7B0584DD319573560F162AC8E5E20BECF3513CB20D53C82A7165C1EB27)\


We have wrapped the nft, this means that the andromeda cw721 should have minted a new token for us. Let us check by querying my tokens in the andromeda nft contract.

```
junod query wasm contract-state smart juno1q2m6f79j6wpcfw7v4lzlwe5x5u0utptjd28lsvdtl6pslwhur8yqzyvyzj '{"tokens":{"owner":"juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll"}}' 
```

Result:

data:&#x20;

&#x20;   tokens:

\-"1"

### TransferAgreement

Now since we have our andromeda NFT, we want to add a transferagreement to it in order to sell the token.

{% hint style="info" %}
We have specified the purchaser as "\*" which means any address is allowed to purchase the token.
{% endhint %}

```json
{
"transfer_agreement":{
    "token_id":"1",
    "agreement":{
        "amount":{
            "raw":{
                "amount":"100000",
                "denom":"ujunox"
                }
            },
         "purchaser":"*"
            }
         }
     }
```

```
junod tx wasm execute juno1q2m6f79j6wpcfw7v4lzlwe5x5u0utptjd28lsvdtl6pslwhur8yqzyvyzj '{"transfer_agreement":{"token_id":"1","agreement":{"amount":{"raw":{"amount":"100000","denom":"ujunox"}},"purchaser":"*"}}}'  --from juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y
```

[Add TransferAgreement ](https://testnet.mintscan.io/juno-testnet/txs/96FB769ED7B1A73E0D35ECB084BF7755DC23B0076D31D70052E34D99720C567D)

Let us query the balances of the seller and purchaser before making the purchase to make sure that correct funds are sent:

**Seller**

```
juno query bank balances juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll
```

Result:

amount: "10492823" denom: ujunox

**Buyer**

```
junod query bank balances juno179rfqj225d5crspna2uujau8003eqtdq2q68hh
```

Result:

amount: "1228107361" denom: ujunox

### Buy the NFT&#x20;

If not familiar with our TransferAgreement message, it allows any buyer to transfer the token to themselves as long as the required funds (Price we set) is attached to the message.&#x20;

```json
{
"transfer_nft":{
    "recipient":"juno179rfqj225d5crspna2uujau8003eqtdq2q68hh",
    "token_id":"1"
    }
}
```

We attach 100000 ujunox to the message:

```
junod tx wasm execute juno1q2m6f79j6wpcfw7v4lzlwe5x5u0utptjd28lsvdtl6pslwhur8yqzyvyzj '{"transfer_nft":{"recipient":"juno179rfqj225d5crspna2uujau8003eqtdq2q68hh","token_id":"1"}}'  --from juno179rfqj225d5crspna2uujau8003eqtdq2q68hh --amount 100000ujunox --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y
```

[Buy the nft using transfer](https://testnet.mintscan.io/juno-testnet/txs/8AB7F25AA53626B398E36C1CA9547157FD9B6C9FECE9C829D27681CA896E9AF1)

Let us check the balances again:

Seller: 10592823ujunox

Buyer: 1227984518ujunox

As we can see 100000 ujunox have been transfered and the token is now owned by the buyer address.&#x20;

### Unwrap the Token

We have minted a cw721-base nft, wrapped it using the andromeda ADOs and sold it using the custom TransferAgreement. Now we will unwrap the token and get the original back.

{% hint style="info" %}
The buyer of the token is now the owner, so he would be unwrapping the token.&#x20;
{% endhint %}

Let us see our tokens first in the cw721-base contract:

```
junod query wasm contract-state smart juno134hgkjxzftmttgg5w5v79wmm0mdwn0y04s5pyy4lwekkk8fsucqsk3xtqc '{"tokens":{"owner":"juno179rfqj225d5crspna2uujau8003eqtdq2q68hh"}}' 
```

Result: tokens: \[].&#x20;

As we can see we have no tokens. Now let us unwrap our token:

```json
{
"send_nft":{
"contract":"juno1u4jg78rt9ruulptgk5xluz3r2gpt9uurnuvv9h6zedszk6y6qu4qwp43jj",
"token_id":"1",
"msg":"eyJ1bndyYXAiOnt9fQo="
   }
}
```

The base64 message is:

```rust
{"unwrap":{}}
```

```
junod tx wasm execute  juno1q2m6f79j6wpcfw7v4lzlwe5x5u0utptjd28lsvdtl6pslwhur8yqzyvyzj '{"send_nft":{"contract":"juno1u4jg78rt9ruulptgk5xluz3r2gpt9uurnuvv9h6zedszk6y6qu4qwp43jj","token_id":"1","msg":"eyJ1bndyYXAiOnt9fQo="}}'  --from juno179rfqj225d5crspna2uujau8003eqtdq2q68hh --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y
```

[Unwrap the token.](https://testnet.mintscan.io/juno-testnet/txs/0BD6A4C3F61CD43F9541025414BA5A71A7CCB48F66682C03F7F4415F28E9EEB9)

Now let us check again our tokens in the cw721-base contract:

```
junod query wasm contract-state smart juno134hgkjxzftmttgg5w5v79wmm0mdwn0y04s5pyy4lwekkk8fsucqsk3xtqc '{"tokens":{"owner":"juno179rfqj225d5crspna2uujau8003eqtdq2q68hh"}}' 
```

Result:

tokens:

\-"1"

As we can see, when we unwrapped the token, the wrapped token is removed and the original token is back in our address.&#x20;

### Conclusion

The Andromeda wrapped ADO is one of many custom contracts that the Andromeda team has built to allow use cases such as the one we have demonstrated above. We can wrap tokens to leverage any of the functionalities that Andromeda has built. For example we can wrap a token and sell it in an auction, offers, and even use the staking nft to allow staking of these tokens. The possibilites are endless and only limited by the user's imagination.&#x20;
