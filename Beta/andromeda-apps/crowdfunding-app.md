---
description: Deploying a crowdfunding App using the Andromeda CLI.
---

# Crowdfunding App

We have covered [earlier](../andromeda-digital-objects/app.md) the App contract and its messages. We briefly discussed the steps to deploying an App. In this section we will be providing an example of how to deploy an App using the Andromeda CLI.

{% hint style="warning" %}
If any of the messages in this example do not work, you might want to cross reference the messages with the ADO specific section which always contains the latest ADO versions to make sure they are correct. Other than that the logic will remain the same.

Make sure to read the [Introduction to Apps](introduction-to-apps.md) before going through building an app.
{% endhint %}

## The Steps of Deploying an App

We already have an ADODB contract with saved code IDs of the ADOs and Kernel ADO deployed. These addresses can be found in [Deployed Contracts](../platform-and-framework/deployed-contracts.md) section. This means that to deploy an App we only need to instantiate it through the CLI.

{% hint style="warning" %}
You will need the deployed Kernel contract address as it needs to be specified when instantiating any ADO.
{% endhint %}

## Defining our App

For this example we will be building a simple crowdfunding app. We will be creating NFTs that are sold in a crowdfund and then the proceeds are distributed to a sepcified address. If the crowdfund does not reach the threshold of minimum tokens sold, the money is refunded to the buyers.&#x20;

## Defining the Instantiation Messages

For this example, our App needs to have 5 components/ADOs:

* [CW721](../andromeda-digital-objects/cw721.md)&#x20;
* Crowdfund&#x20;

{% hint style="warning" %}
Although not necessary, if you are unfamiliar with these ADOs, it is suggested to read through each of the them before deploying an app.&#x20;
{% endhint %}

Let us first start by representing the instantiation message for each of our ADOs:

{% hint style="warning" %}
Keep in mind that the app takes these messages as base64 encoded.

Some of your base64 messages will not be the exact same as the ones in the example as you will be using your own addresses.&#x20;
{% endhint %}

### CW721&#x20;

{% hint style="warning" %}
Don't forget to set the minter as the crowdfund ADO.&#x20;

You can get the Kernel address by running "chain config" in CLI.

When referencing a component in an app, you need to put "./\<component-name>" similar to what we do here with the minter field. &#x20;
{% endhint %}

```json
{
"name":"Example App",
"symbol":"SMMT",
"minter":"./crowdfund",
"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"
}  
```

The JSON message in our case is the following as Base64:

```
eyJuYW1lIjoiRXhhbXBsZSBBcHAiLCJzeW1ib2wiOiJTTU1UIiwibWludGVyIjoiLi9jcm93ZGZ1bmQiLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9
```

### Crowdfund

```json
{
"token_address":"./tokens",
"can_mint_after_sale":true,
"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"
}   
```

As Base64:

```
eyJ0b2tlbl9hZGRyZXNzIjoiLi90b2tlbnMiLCJjYW5fbWludF9hZnRlcl9zYWxlIjp0cnVlLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9
```

### App

{% hint style="warning" %}
The Kernel address used here might be outdated in the future. Check our [deployed contracts](../platform-and-framework/deployed-contracts.md) to get the latest Kernel or run "chain config" in CLI which will also display the address.
{% endhint %}

```json
{
  "name": "Crowdfunding",
  "app_components": [
    {
      "name": "crowdfund",
      "ado_type": "crowdfund",
      "component_type":{
       "new":"eyJ0b2tlbl9hZGRyZXNzIjoiLi90b2tlbnMiLCJjYW5fbWludF9hZnRlcl9zYWxlIjp0cnVlLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"
       }
     },
    {
      "name": "tokens",
      "ado_type": "cw721",
      "component_type":{
      "new":"eyJuYW1lIjoiRXhhbXBsZSBBcHAiLCJzeW1ib2wiOiJTTU1UIiwibWludGVyIjoiLi9jcm93ZGZ1bmQiLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"
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
os wadodb getcodeid app-contract
```

The code Id to use will be returned.

&#x20;Now we can instantiate our App. We will be using our wasm command to instantiate our app:

{% hint style="warning" %}
Note that the instantiation message will  not be the exact same for you since you will be using a different recipient address.

Do not copy and paste the command below as it will not work. Make sure to make the appropriate changes before running the command.&#x20;

Replace the \<code\_id> with the returned code-id for the app ADO.&#x20;
{% endhint %}

```
wasm instantiate <code-id> '{"name": "Crowdfunding","app_components": [{"name": "crowdfund","ado_type": "crowdfund","component_type":{"new":"eyJ0b2tlbl9hZGRyZXNzIjoiLi90b2tlbnMiLCJjYW5fbWludF9hZnRlcl9zYWxlIjp0cnVlLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"}},{"name": "tokens","ado_type": "cw721","component_type":{"new":"eyJuYW1lIjoiRXhhbXBsZSBBcHAiLCJzeW1ib2wiOiJTTU1UIiwibWludGVyIjoiLi9jcm93ZGZ1bmQiLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"}}],"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"}'
```

Upon instantiation, we will get a link to the explorer where we can check the transaction. Also the contract address of the App is displayed:

<figure><img src="../.gitbook/assets/Screen Shot 2023-12-11 at 5.41.43 PM.png" alt=""><figcaption></figcaption></figure>

[Transaction](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/D9D2DE69C5B1DC1F6105ECAE41A7D9620028C4DAC6453F3E311942A8A59AE8E2)

Open the explorer link where you can see that the instantiation was a success. Three contracts were instantiated in total which are the App and the 2 components we used.

## Interacting with the App

{% hint style="warning" %}
You will be using the contract addresses that were instantiated for you instead of the ones in this tutorial.&#x20;

You will prompted to add funds when executing. Do not add any funds unless specified in the example.
{% endhint %}

### Crowdfund

Now that the App has been created and we have ownership over all the components, we can proceed with minting the tokens for the crowdfund.

### Minting the NFTs

We will be minting three new tokens using the crowdfund component.

{% hint style="warning" %}
Here we use a random token uri. This uri should point to the saved metada of the NFT (IPFS). You can learn more about setting up NFT metadata [here](https://docs.andromedaprotocol.io/guides/guides-and-examples/ado-builder/building-your-first-nft-collection/setting-up-nft-metadata).
{% endhint %}

```json
{
    "mint":[
        {
            "token_id":"1",
            "token_uri":"https://gateway.pinata.cloud/ipfs...",
            "extension": {
                "publisher": "Andromeda"
              }            
        },
         {
            "token_id":"2",
            "token_uri":"https://gateway.pinata.cloud/ipfs...",
            "extension": {
                "publisher": "Andromeda"
              }            
        },
         {
            "token_id":"3",
            "token_uri":"https://gateway.pinata.cloud/ipfs...",
            "extension": {
                "publisher": "Andromeda"
              }            
        }
    ]
}
```

Let us run the execute:

{% hint style="warning" %}
Replace \<crowdfund-contract-address> with your instantiated crowdfund contract address. You can get the addresses of the components by using an [App query](introduction-to-apps.md#getting-component-addresses).
{% endhint %}

```
wasm execute <crowdfund-contract-address> '{"mint":[{"token_id":"1","token_uri":"https://gateway.pinata.cloud/ipfs...","extension": {"publisher": "Andromeda"}},{"token_id":"2","token_uri":"https://gateway.pinata.cloud/ipfs...","extension": {"publisher": "Andromeda"}},{"token_id":"3","token_uri":"https://gateway.pinata.cloud/ipfs...","extension": {"publisher": "Andromeda"}}]}' 
```

[Transaction](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/F3BB1FDDB3F98B4E57A90D5EC2FEA66DA5ACE0D669620F6C0E5A21149271725B)

In this message multiple tokens are minted and their mint messages are proxied to the “tokens” contract found within the app. No owner is provided, although one can be declared, so the crowdfund contract is assigned as the owner of the tokens for the duration of the sale.

Since we have minted our tokens, we can now start the crowdfund sale.

### Starting The Sale

We call the StartSale message to start the crowfunding sale on the NFTs we minted.

{% hint style="warning" %}
You can also add a start time field to specify when the sale should start. Ommitting is means that the sale will begin as soon as the Start Sale message is executed on chain.
{% endhint %}

```json
{"start_sale":{
        "end_time"::1726223089000,
        "price": {
          "amount": "10000",
          "denom": "uandr"
        },
        "min_tokens_sold": "0",
        "max_amount_per_wallet":10,
        "recipient":{
            "address":"<address-to-receive-funds>"
          }
        }
      }
```

{% hint style="warning" %}
Make sure the end\_time you specify is not in the past. You can use an [online converter](https://www.epochconverter.com) to convert a date to a timestamp. It is specified in milliseconds.

Use your own address as the recipient.
{% endhint %}

Let us run the execute:

```
wasm execute <crowdfund-contract-address> '{"start_sale":{"end_time":"11048328053820324","price":{"denom":"uandr","amount":"10000"},"min_tokens_sold":"10000","max_amount_per_wallet":10,"recipient":{"addr":"andr1gzk2m0k40e30wn6wv9j9kcrx4ljlk4ks8whrgq"}}}'
```

[**Transaction** ](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/7BD6208C7769B8B3256F52F4FAC08AFC9F23DFC27D13D009486BC05680D6795B)

This message commences a sale, listing each token for 10000uandr with a minimum of 0 and expires at time 1726223089000 . The recipient defined in this message will be the receiver of the funds after the sale has ended. In this case the recipient is another address.

Let's query the balance of the recipient before purchasing the NFTs. We run the following command::

```
? user-3@galileo-4>  bank balance uandr <recipient-address>
```

Result:

```
{{ denom: 'uandr', amount: '199614441' }

Balance
uandr: 199614441
```



The sale has started, time to purchase our minted tokens.

### Purchase the NFTs

```json
{
"purchase":{
    "number_of_tokens":1
    }
}
```

{% hint style="danger" %}
In order to purchase a token, you will need to attach funds to the message equal to the price of the NFT which is 1000 uandr in our case. When you run the message, you will be asked "Would you like to add funds to this message?". Select yes and input 10000uandr as the amount.
{% endhint %}

```
wasm execute <crowdfund-contract-address> '{"purchase":{"number_of_tokens":1}}'
```

For purchasing the tokens I will be using some addresses representing buyers.

[Purchase token 1](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/8DF5AE5FA2FAA389ABFA0CC54394A1F5FAA67807F2C82C9A7A011D2DE3428875)

[Purchase token 2](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/99126702CF529C9A6E3A9021655CE9D31BE66FF81BE1DEE2779653D936155335)

[Purchase token 3](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/D73644E928DD4E30EB3B3A8374D3027608F45A8B409BCFC1EF94B54CD2D294AE)

### End The Sale

Now that the tokens have been sold the sale can be ended:

{% hint style="warning" %}
You can only end a sale if all the tokens have been sold or the expiration has been reached. In our case, we have bought all the tokens for sale, so we can end the sale.
{% endhint %}

```json
{
"end_sale":{}
}
```

{% hint style="warning" %}
Two messages should be sent due to pagination concerns. One will distribute the tokens to the purchasers and the other sends the funds.
{% endhint %}

```
wasm execute <crowdfund-contract-address> '{"end_sale":{}}'
```

We have successfully:

1. Minted tokens for the crowdfund
2. Started a sale to sell the tokens
3. Purchased the tokens for 1000 uandr each
4. Ended the sale

Since each of the tokens was sold for 10000 uandr we should have  30000uandr sent to the recipient address that we specified in the StartSale message. To make sure of the results, let us check the balance again:

```
? user@galileo-3> bank balance uandr <address>
{ denom: 'uandr', amount: '199644441' }

Balance
uandr: 199644441 
```

We can see that 30000uandr has been added to the balance.

You can also use the [OwnerOf ](../andromeda-digital-objects/cw721.md#ownerof)query to check who owns the NFTs now. It should correspond to your buyers.

### **Conclusion**

This example serves as a small tutorial for building an Andromeda app using the CLI. We will have many more examples like this coming soon representing the different apps that we can build using our ADOs. These tutorials are mostly made for developers looking to use and build using our ADOs. All what we did here and more can be done using our user friendly web-app with a few clicks.
