---
description: Deploying a wrapped NFT App using the Andromeda CLI.
---

# Wrapped-NFT App

If unfimailar with the steps of deploying an app, go back to the [first example](crowdfunding-app.md) where we explain in detail all the different parts of deploying an App.

{% hint style="warning" %}
If any of the messages in this example do not work, you might want to cross reference the messages with the ADO specific section which always contains the latest ADO versions to make sure they are correct. Other than that the logic will remain the same.

Make sure to read the [Introduction to Apps](introduction-to-apps.md) before going through building an app.
{% endhint %}

### **Defining our App**

For this example, we will be utilizing our [wrapped-nft ADO](../andromeda-digital-objects/wrapped-cw721.md) to wrap a CW721-base nft and leverage the andromeda [TransferAgreement](broken-reference) functionality to sell the token. We will build our project using our CLI.

{% hint style="info" %}
We will not build this example using the App contract.
{% endhint %}

The steps we will perform:&#x20;

1. Mint a token using the CW721-base contract found in [cw-nfts repo.](https://github.com/CosmWasm/cw-nfts)
2. Send it to wrapper ADO to have it wrapped.
3. Apply a TransferAgreement for the token.&#x20;
4. Buy the token using another address.&#x20;
5. Unwrap the token to get the original back.&#x20;

We will need to use the following andromeda contracts in addition to the base-nft contract:

* [Andromeda wrapped-nft ](../andromeda-digital-objects/wrapped-cw721.md)
* [Andromeda cw721](broken-reference)

### Instantiating the CW-721 base NFT

First we need to mint a regular nft from the cw-nft repo. To do this, we first need to clone the repo, build the contract and upload it to the chain we want to deploy on. To do so, we perform the following steps:

* Start a new project in your IDE
* In the terminal:

```
git clone https://github.com/CosmWasm/cw-nfts.git
cd cw-nfts
```

Then [compile](https://github.com/CosmWasm/rust-optimizer) the CW721 base contract. When done, the contract .wasm file should appear in the artifacts directory.&#x20;

Let us open the CLI by running `andr` in our terminal from artifacts directory. We then need to choose the chain we want to deploy on:

{% hint style="warning" %}
Feel free to use any of the chains available
{% endhint %}

```
chain use 
```

Then select the chain to deploy on.

{% hint style="warning" %}
If this is the first time using the CLI make sure to run `"wallet add <wallet-name>"`

in order to create a wallet. Then go to chain's faucet to request some tokens (Usually the faucet will be in the Chain's discord).
{% endhint %}

#### Uploading the Contract

To upload the contract, we run:

```
wasm upload cw721_base.wasm
```

The contract will be uploaded to chain and the code Id will be returned.&#x20;

The instantiation message for CW721 base nft is the following:

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
"minter":"<your-address>"
}
```
{% endtab %}
{% endtabs %}

#### Instantiate

Now that we have our contract uploaded we can instantiate a CW721:

{% hint style="warning" %}
Use the code Id that you got when uploading the contract instead of 201.
{% endhint %}

```
wasm instantiate <cw721-code-id> '{"name":"test","symbol":"TST","minter":"<your-address>"}' 
```

#### Mint an NFT&#x20;

We will mint an NFT with token\_id specified as 1.

```json
{
"mint":{
"token_id":"1",
"owner":"<your-address>"
   }
}
```

```
 wasm execute <cw721-contract-address> '{"mint":{"token_id":"1","owner":"<your-address>"}}'
```

### Instantiate Wrapped-NFT ADO.

A simple way to check the latest code Id for the App ADO is to query it from the ADODB using the the chain you want to use.&#x20;

In the CLI, while connected to the chain of choice, run:

```
ado db getcodeid wrapped-cw721
```

The code Id to use will be returned.

{% hint style="warning" %}
The kernel address used here might be outdated in the future. Check our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>) to get the latest kernel address.
{% endhint %}

```json
{
"cw721_instantiate_type":{
    "new":{
        "name":"test-wrap",
        "symbol":"TST"
        }
    },
"can_unwrap": true,
"kernel_address":"andr159llw7xkpt20llxqmjegsegfn548q5l6jr2x7rh7sy7sal39syfs3tlvmm"
}
```

```
 wasm instantiate <wrapped-code-id> '{"cw721_instantiate_type":{"new":{"name":"test-wrap","symbol":"TST"}},"can_unwrap": true,"kernel_address":"andr159llw7xkpt20llxqmjegsegfn548q5l6jr2x7rh7sy7sal39syfs3tlvmm"}' 
```

{% hint style="warning" %}
When we instantiate the wrapped-nft contract, we specified the cw721 type as new which means that we have also instantiated an andromeda cw721 contract.
{% endhint %}

#### Wrap the NFT

```json
{
    "send_nft": {
        "contract": "<wrapped-NFT-contract-address>",
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
wasm execute <cw721-contract-address> '{"send_nft":{"contract":"<wrapped-NFT-contract-address>","token_id":"1","msg":"eyJ3cmFwIjp7fX0="}}' 
```

We have wrapped the nft, this means that the andromeda cw721 should have minted a new token for us. Let us check by querying our tokens in the andromeda nft contract.

```
wasm query <andromeda-cw721-contract-address> '{"tokens":{"owner":"<your-owner-address>"}}' 
```

**Result:**

```
– Querying contract...
{
  "tokens": [
    "1"
  ]
}
```

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
                "denom":"uandr"
                }
            },
         "purchaser":"*"
            }
         }
     }
```

```
wasm execute <andromeda-cw721-contract-address> '{"transfer_agreement":{"token_id":"1","agreement":{"amount":{"raw":{"amount":"10000","denom":"uandr"}},"purchaser":"*"}}}'  
```

### Buy the NFT&#x20;

If not familiar with our TransferAgreement message, it allows any buyer to transfer the token to themselves as long as the required funds (Price we set) is attached to the message.&#x20;

```json
{
"transfer_nft":{
    "recipient":"<any-recipient-address>",
    "token_id":"1"
    }
}
```

We attach 10000 uandr to the message:

```
wasm execute <andromeda-cw721-contract-address> '{"transfer_nft":{"recipient":"<any-recipient-address>","token_id":"1"}}' --funds 10000uandr  
```

The NFT will be transfered to the recipient and the funds sent to the seller.

### Unwrap the Token

We have minted a cw721-base nft, wrapped it using the andromeda ADOs and sold it using the custom TransferAgreement. Now we will unwrap the token and get the original back.

{% hint style="info" %}
The buyer of the token is now the owner, so he would be unwrapping the token. Make sure you are using the new owner wallet to run the next message.
{% endhint %}

Let us see our tokens first in the cw721-base contract:

```
wasm query <cw721-base-contract-address> '{"tokens":{"owner":"<owner-address>"}}' 
```

**Result**

```
– Querying contract...
{
  "tokens": []
}
```

As we can see we have no tokens. Now let us unwrap our token:

```json
{
"send_nft":{
"contract":"<wrapped-NFT-contract-address>",
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
wasm execute <andromeda-cw721-contract-address> '{"send_nft":{"contract":"<wrapped-NFT-contract-address>","token_id":"1","msg":"eyJ1bndyYXAiOnt9fQo="}}'  
```

Now let us check again our tokens in the cw721-base contract:

```
wasm query <cw721-base-contract-address> '{"tokens":{"owner":"<nft-owner-address>"}}' 
```

**Result**

```
– Querying contract...
{
  "tokens": [
    "1"
  ]
}
```

As we can see, when we unwrapped the token, the wrapped token is removed and the original token is back in our address.&#x20;

### Conclusion

The Andromeda wrapped ADO is one of many custom contracts that the Andromeda team has built to allow use cases such as the one we have demonstrated above. We can wrap tokens to leverage any of the functionalities that Andromeda has built. For example we can wrap a token and sell it in an auction, offers, and even use the staking nft to allow staking of these tokens. The possibilites are endless and only limited by the user's imagination.&#x20;
