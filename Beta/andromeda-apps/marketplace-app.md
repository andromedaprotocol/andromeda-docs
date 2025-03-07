---
description: Deploying an NFT Marketplace App
---

# Marketplace App

If unfamiliar with the steps of deploying an app, go back to the [first example](crowdfunding-app.md) where we explain in detail all the different parts of deploying an App.

{% hint style="warning" %}
If any of the messages in this example do not work, you might want to cross reference the messages with the ADO specific section which always contains the latest ADO versions to make sure they are correct. Other than that the logic will remain the same.

Make sure to read the [Introduction to Apps](introduction-to-apps.md) before going through building an app.
{% endhint %}

## Defining our App

{% hint style="info" %}
You can also use CW20 tokens as the purchasing tokens for the NFTs in the marketplace.
{% endhint %}

For this App, we will be selling NFTs using the [Marketplace ADO](../andromeda-digital-objects/marketplace.md) for native tokens. The steps that will be performed:

* Mint NFTs&#x20;
* Setup the marketplace&#x20;
* Send the NFTs to the marketplace to be sold
* Buy the NFTs&#x20;

## Defining the Messages

The components/ADOs we would need:

* [CW721 ](../andromeda-digital-objects/cw721.md)(NFT contract)
* [Marketplace](../andromeda-digital-objects/marketplace.md)

{% hint style="warning" %}
Although not necessary, if you are unfamiliar with these ADOs, it is suggested to read through each of the them before deploying an app.&#x20;
{% endhint %}

Let us first start by representing the instantiation message for each of our ADOs:

{% hint style="warning" %}
Keep in mind that the app takes these messages as base64 encoded.
{% endhint %}

### CW721

{% hint style="warning" %}
Make sure to set the minter as your own address.
{% endhint %}

```json
{
"name":"MarketplaceNFTs",
"symbol":"MNFT",
"minter":"<your-address>",
"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"
}  
```

As base64:

```
eyJuYW1lIjoiTWFya2V0cGxhY2VORlRzIiwic3ltYm9sIjoiTU5GVCIsIm1pbnRlciI6ImFuZHIxZ2Z6c3UwdmVmcnYydjlnNWdrdm1oazNjZWd1MjA4cGV2dXQ2dWgiLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9
```

### Marketplace&#x20;

{% hint style="info" %}
You can specify the authorized CW20 addresses here if you plan to use CW20 tokens to purchase the NFTs. In our cases we are using native so we only have to specify the kernel address.
{% endhint %}

```json
{
"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"
}
```

As base64:

```
eyJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9
```

### App

{% hint style="warning" %}
The Kernel address used here might be outdated in the future. Check our [deployed contracts](../platform-and-framework/deployed-contracts.md) to get the latest Kernel or run "chain config" in CLI which will also display the address.
{% endhint %}

```json
{
  "name": "Marketplace App",
  "app_components": [
    {
      "name": "NFTs",
      "ado_type": "cw721",
      "component_type":{
      "new":"eyJuYW1lIjoiTWFya2V0cGxhY2VORlRzIiwic3ltYm9sIjoiTU5GVCIsIm1pbnRlciI6ImFuZHIxZ2Z6c3UwdmVmcnYydjlnNWdrdm1oazNjZWd1MjA4cGV2dXQ2dWgiLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"
       }
     },
    {
      "name": "marketplace",
      "ado_type": "marketplace",
      "component_type":{ 
      "new":"eyJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"
        }
    }
  ],
  "kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"
  }
```

There are several components found here. A simple name for the app, the components of the app itself and a reference to the “Kernel”. Each component of the app provides two values: a name used for referencing for other components within the app, and the base64 encoded instantiation message.&#x20;

## Instantiating the App

If you do not have the CLI downloaded, go to the [Introduction to Apps](introduction-to-apps.md) section to get the latest version.

First, let us open the CLI by running `andr` in our terminal. We then need to choose the chain we want to deploy on. Run `"chain use"` in the CLI and select the testnet that you want to deploy on. For this example I will be using the Andromeda test-net Galileo-3.

{% hint style="warning" %}
If this is the first time using the CLI make sure to run `"wallet add <wallet-name>"`in order to create a wallet. Then go to that chain's faucet (usually located in their discord) and request tokens.
{% endhint %}

We have already uploaded the App ADO to the Andromeda testnet. A simple way to check the latest code id for the App ADO is to query it from the ADODB using the chain you want to use.&#x20;

In the CLI, while connected to the chain of choice, run:

```
os adodb getcodeid app-contract
```

The code Id to use will be returned.

&#x20;Now we can instantiate our App. We will be using our wasm command to instantiate our app:

{% hint style="warning" %}
Do not copy and paste the command below as it will not work. Make sure to make the appropriate changes before running the command.&#x20;

Replace the \<code\_id> with the returned code-id for the app ADO.&#x20;
{% endhint %}

```
wasm instantiate <app-code-id> '{"name": "Marketplace App","app_components": [{"name": "NFTs","ado_type": "cw721","component_type":{"new":"eyJuYW1lIjoiTWFya2V0cGxhY2VORlRzIiwic3ltYm9sIjoiTU5GVCIsIm1pbnRlciI6ImFuZHIxZ2Z6c3UwdmVmcnYydjlnNWdrdm1oazNjZWd1MjA4cGV2dXQ2dWgiLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"}},{"name": "marketplace","ado_type": "marketplace","component_type":{ "new":"eyJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"}}],"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"}'
```

{% hint style="warning" %}
You will be using the contract addresses that were instantiated for you instead of the ones in this tutorial.
{% endhint %}

## Interacting with the App

### Mint NFTs

{% hint style="warning" %}
We will only mint one NFT in this example as the process for selling more NFTs will be the same. Feel free to mint as many as you want.
{% endhint %}

Now we need to mint an NFT to send to the marketplace.&#x20;

```json
{
"mint": {
            "token_id":"1",
            "owner":"<your-address>",
            "token_uri":"https://gateway.pinata.cloud/ipfs...",
            "extension": {
                "publisher": "Andromeda"
              }            
        }
}
```

```
wasm execute <cw721-contract-address> '{"mint": {"token_id":"1","owner":"andr1gfzsu0vefrv2v9g5gkvmhk3cegu208pevut6uh","token_uri":"https://gateway.pinata.cloud/ipfs...","extension": {"publisher": "Andromeda"}}}'
```

[Transaction](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/C20B530EF4873151696F5E7A188AE913912D429200D89FE28E132D34A101EA98)

### **Send NFT to Marketplace to be Sold**

In order to start a sale on the NFT, we need to send it to the marketplace ADO.

{% hint style="info" %}
The token Id is 1 here since that is what we had assigned to our NFT when minting.&#x20;
{% endhint %}

```json
{
    "send_nft": {
        "contract": "<marketplace-contract-address>",
        "token_id": "1",
        "msg": "eyJzdGFydF9zYWxlIjogeyJwcmljZSI6IjEwMDAwIiwiY29pbl9kZW5vbSI6ICJ1YW5kciIsInVzZXNfY3cyMCI6IGZhbHNlfX0="
    }
}
```

The attached message is a `start_sale` message to start the auction when the NFT is sent.

{% hint style="info" %}
You can add a start time, end time, and a recipient to receive the funds of the sale. By ommitting them, we are specifying that the sale should start immediately, does not expire unless we decide to cancel it, and that the funds from the sale should go to the sender ie. our address.
{% endhint %}

```json
{
"start_sale":{
    "price": "10000",
    "coin_denom":"uandr",
    "uses_cw20": false
    }
}
```

```
wasm execute <cw721-contract-address> '{"send_nft": {"contract": "andr18dacalhasfqs45dz5tmsa38278svz5elvys83rfas9tz7n9eh5vs8gx6cp","token_id": "1","msg": "eyJzdGFydF9zYWxlIjogeyJwcmljZSI6IjEwMDAwIiwiY29pbl9kZW5vbSI6ICJ1YW5kciIsInVzZXNfY3cyMCI6IGZhbHNlfX0="}}'
```

[Start Sale](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/01EFAF4240BC16426D2C0FBF66866814CE66BC72DD813FA25B8464D0401EC620)

### Buy the NFT

Now let us buy the NFT. On the CLI:

{% hint style="warning" %}
Make sure to use a wallet other than the owner of the NFT as the owner cannot buy the NFT.
{% endhint %}

```
wallet use <wallet-name>
```

```json
{
"buy":{
    "token_id":"1",
    "token_address":"<cw721-contract-address>"
    }
}
```

{% hint style="warning" %}
Make sure to attach funds to the message equal to the listed price of the NFT which is 1000uandr in our case.
{% endhint %}

```
wasm execute <marketplace-contract-address> '{"buy":{"token_id":"1","token_address":"<cw721-contract-address>"}}' 
```

[Buy the NFT ](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/2E5ACF33EE9CB4D3536D03B48C00EC5CC5B09086CCE5498E96C0BC67773CA778)

### Conclusion

We have seen in this example how we can easily sell NFTs using our marketplace ADO. You can customize this application as you see fit. Some possible modifications you can make for this application:

* Add start and end times for the NFT sale.
* Use a CW20 token as the purchasing token for the NFTs.
* Add a splitter recipient to the start sale message to directly split all the funds made from the NFT sales to any amount of addresses. You can even have the splitter send some of the tokens to another ADO and execute some message there.
