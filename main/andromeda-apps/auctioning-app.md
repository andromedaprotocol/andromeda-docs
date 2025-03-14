---
description: Deploying an auctioning App using the Andromeda CLI.
---

# Auctioning App

{% hint style="warning" %}
If any of the messages in this example do not work, you might want to cross reference the messages with the ADO specific section which always contains the latest ADO versions to make sure they are correct. Other than that the logic will remain the same.

Make sure to read the [Introduction to Apps](introduction-to-apps.md) before going through building an app.
{% endhint %}

## Defining our App

{% hint style="info" %}
You can also use a CW20 token as the bidding token. You ca read more about the [Auction ADO](../andromeda-digital-objects/auction.md) if you are interested in testing out variations on this App.
{% endhint %}

For this example, we will be building a simple auction app that holds auction sales on NFTs using a native token for bidding. We only need two components/ADOs:

* [Token/Cw721](../andromeda-digital-objects/cw721.md)
* [Auction](../andromeda-digital-objects/auction.md)

&#x20;We will perform the following steps:

* Mint an NFT&#x20;
* Send it to auction starting an auction on it&#x20;
* Place bids on the NFT
* End the auction
* Claim the funds and transfer the NFT to the new owner

## The Steps of Deploying an App

We already have an ADODB contract with saved code IDs of the ADOs and Kernel ADO deployed. These addresses can be found in [Deployed Contracts](../platform-and-framework/deployed-contracts.md) section. This means that to deploy an App we only need to instantiate it through the CLI.

{% hint style="warning" %}
You will need the deployed Kernel contract address as it needs to be specified when instantiating any ADO.
{% endhint %}

## Defining the Instantiation Messages

For this app we would need only 2 components:&#x20;

* [A token/cw721](../andromeda-digital-objects/cw721.md)
* [Auction](../andromeda-digital-objects/auction.md)

{% hint style="warning" %}
Although not necessary, if you are unfamiliar with these ADOs, it is suggested to read through each of the them before deploying an app.&#x20;
{% endhint %}

Let us first start by representing the instantiation message for each of our ADOs:

{% hint style="warning" %}
Keep in mind that the app takes these messages as base64 encoded.
{% endhint %}

### CW721

{% hint style="warning" %}
Make sure to set the minter as your own address. If you do not change the minter, you will not be able to mint any tokens.&#x20;
{% endhint %}

```json
{
"name":"AuctionNFTs",
"symbol":"ANFT",
"minter":"<your-address>",
"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"
}  
```

As base64:

```
eyJuYW1lIjoiQXVjdGlvbk5GVHMiLCJzeW1ib2wiOiJBTkZUIiwibWludGVyIjoiYW5kcjFnemsybTBrNDBlMzB3bjZ3djlqOWtjcng0bGpsazRrczh3aHJncSIsImtlcm5lbF9hZGRyZXNzIjoiYW5kcjE0aGoydGF2cThmcGVzZHd4eGN1NDRydHkzaGg5MHZodWpydmNtc3RsNHpyM3R4bWZ2dzlzaHB0a3FsIn0=
```

### Auction

Here we specify which CW721 is permitted to send NFTs to the auction. We reference the CW721 of our app through the name.

```json
{
"authorized_token_addresses":["./tokens"],
"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"
}
```

As base64:

```
eyJhdXRob3JpemVkX3Rva2VuX2FkZHJlc3NlcyI6WyIuL3Rva2VucyJdLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9
```

### App

{% hint style="warning" %}
The Kernel address used here might be outdated in the future. Check our [deployed contracts](../platform-and-framework/deployed-contracts.md) to get the latest Kernel or run "chain config" in CLI which will also display the address.

The `instantiate_msg` for the cw721 will not be the same for you as you should specify the minter address as your own. Make sure to replace it with the base64 message that you got.
{% endhint %}

```json
{
  "name": "AuctionApp",
  "app_components": [
    {
      "name": "auction",
      "ado_type": "auction",
      "component_type":{
       "new":"eyJhdXRob3JpemVkX3Rva2VuX2FkZHJlc3NlcyI6WyIuL3Rva2VucyJdLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"
       }
     },
    {
      "name": "tokens",
      "ado_type": "cw721",
      "component_type":{
      "new":"eyJuYW1lIjoiQXVjdGlvbk5GVHMiLCJzeW1ib2wiOiJBTkZUIiwibWludGVyIjoiYW5kcjFnemsybTBrNDBlMzB3bjZ3djlqOWtjcng0bGpsazRrczh3aHJncSIsImtlcm5lbF9hZGRyZXNzIjoiYW5kcjE0aGoydGF2cThmcGVzZHd4eGN1NDRydHkzaGg5MHZodWpydmNtc3RsNHpyM3R4bWZ2dzlzaHB0a3FsIn0="
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
Note that the instantiation message will  not be the exact same for you since you will be using a different recipient address.

Do not copy and paste the command below as it will not work. Make sure to make the appropriate changes before running the command.&#x20;

Replace the \<code\_id> with the returned code-id for the app ADO.&#x20;
{% endhint %}

```
 wasm instantiate <app-code-id> '{"name": "AuctionApp","app_components": [{"name": "auction","ado_type": "auction","component_type":{"new":"eyJhdXRob3JpemVkX3Rva2VuX2FkZHJlc3NlcyI6WyIuL3Rva2VucyJdLCJrZXJuZWxfYWRkcmVzcyI6ImFuZHIxNGhqMnRhdnE4ZnBlc2R3eHhjdTQ0cnR5M2hoOTB2aHVqcnZjbXN0bDR6cjN0eG1mdnc5c2hwdGtxbCJ9"}},{"name": "tokens","ado_type": "cw721","component_type":{"new":"eyJuYW1lIjoiQXVjdGlvbk5GVHMiLCJzeW1ib2wiOiJBTkZUIiwibWludGVyIjoiYW5kcjFnemsybTBrNDBlMzB3bjZ3djlqOWtjcng0bGpsazRrczh3aHJncSIsImtlcm5lbF9hZGRyZXNzIjoiYW5kcjE0aGoydGF2cThmcGVzZHd4eGN1NDRydHkzaGg5MHZodWpydmNtc3RsNHpyM3R4bWZ2dzlzaHB0a3FsIn0="}}],"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"}'
```

[Transaction](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/0F86D12C7243B7F0F33417E4C13C8740DFA3B3FC32EA97DF6618F0B3DEDC86FF)

{% hint style="warning" %}
You will be using the contract addresses that were instantiated for you instead of the ones in this tutorial.&#x20;
{% endhint %}

## Interacting with the App

### Mint Tokens

We will only mint one NFT to auction:

{% hint style="warning" %}
Make sure to replace \<your-address> with the address you are using for the owner field.
{% endhint %}

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
wasm execute <cw721-contract-address> '{"mint": {"token_id":"1","owner":"andr1gzk2m0k40e30wn6wv9j9kcrx4ljlk4ks8whrgq","token_uri":"https://gateway.pinata.cloud/ipfs...","extension": {"publisher": "Andromeda"}}}'
```

[Mint Token](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/5DD7632C3D4E8DE1557AAE9DC99B287A1C3C8568B1332D3992AFC3221B730C81)&#x20;

### Start Auction

In order to start an auction on our token, we need to send it to the auction contract with the required message.

```json
{
    "send_nft": {
        "contract": "<auction-contract-address>",
        "token_id": "1",
        "msg": "eyJzdGFydF9hdWN0aW9uIjogeyJlbmRfdGltZSI6IDE3MTYyMjgwNDczMDgsInVzZXNfY3cyMCI6IGZhbHNlLCJjb2luX2Rlbm9tIjogInVhbmRyIiwibWluX2JpZCI6IjUwMCIsInJlY2lwaWVudCI6eyJhZGRyZXNzIjoiYW5kcjFnemsybTBrNDBlMzB3bjZ3djlqOWtjcng0bGpsazRrczh3aHJncSJ9fX0="
    }
}
```

The attached message is a `start_auction` message to start the auction when the NFT is sent.

{% hint style="warning" %}
You can also add a start time field to specify when the auction should start. Ommitting is means that the auction will begin as soon as the Start Sale message is executed on chain.
{% endhint %}

```json
{
"start_auction": {
        "end_time": 1716228047308,
        "uses_cw20": false,
        "coin_denom": "uandr",
        "min_bid":"500"
        "recipient":{
                "address":"<Your-wallet-address>"
                }
        }
   }
```

{% hint style="warning" %}
The end\_time above will be in the past for you and will give an **Error**. Make sure to check the [current epoch time in milliseconds](https://www.epochconverter.com/clock) to enter a valid end  time.
{% endhint %}

```
wasm execute <cw721-contract-address> '{"send_nft": {"contract": "andr1tg80hn4luwa8mn9xs0lspaujfume55m269cykut6nlahc8asww7s0xga6h","token_id": "1","msg": "eyJzdGFydF9hdWN0aW9uIjogeyJzdGFydF90aW1lIjogMTcwMDgzODU1MDE0MSwiZHVyYXRpb24iOiA2MDAwMDAsImNvaW5fZGVub20iOiAidWFuZHIiLCJtaW5fYmlkIjoiNTAwIn19"}}' 
```

[Start Auction](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/E2A38BD6DCAF8BFA6A4F0AF4E4CB3E52C907289C3D1E3543A550D1980A214C13)

### Place Bids

Now it is time to start placing some bids on our NFT.&#x20;

{% hint style="warning" %}
You can only place bids once the auction started which in our case is right away.
{% endhint %}

```json
{
    "place_bid": {
        "token_id": "1",
        "token_address":"<cw721-contract-address>"
    }
}
```

{% hint style="warning" %}
Dont forget to switch wallets when bidding since the owner cannot bid on their own auctioned NFT. To switch wallets run `"wallets use"` then chose the wallet to bid with.

In order to bid on a token, you will need to attach funds to the message equal to the bid amount you want to place. When you run the message, you will be asked "Would you like to add funds to this message?" select yes and input the bid amount you want. An example would be 600uandr .&#x20;
{% endhint %}

```
wasm execute <auction-contract-address> '{ "place_bid": { "token_id": "1","token_address":"<cw721-contract-address>"}}'
```

[Bid 600uandr](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/E558080BF884D67B5426BE853A0D20CC3AC0B33E6CF912E0733AB9AF242BD26E)

[Bid 2000uandr](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/9D2644BFEAB4778A9C6AAE653CE837A6E4E43ED2F27320ED9393BED33D18A661)

Let us query the Bids of the auction to see if they match:

```json
{
 "bids":{
    "auction_id": "1"
    }
 }
```

```
wasm query <auction-contract-address> '{"bids":{"auction_id": "1"}}' 
```

#### Result

```
{
  "bids": [
    {
      "bidder": "andr1v0an0ar6js3kwmgwr6pp5kuswm6wh0gwdnjska",
      "amount": "600",
      "timestamp": "1716227571751876738"
    },
    {
      "bidder": "andr1dzrcm9swj22v2d05sqfktlwrz3u8hskgujyqhk",
      "amount": "2000",
      "timestamp": "1716227601915954109"
    }
  ]
}
```

After the Auction has concluded, it is time to claim the NFT for the buyer and funds for the seller.

### Claim the NFT

```json
{
    "claim": {
        "token_id": "1",
        "token_address":"<cw721-contract-address>"
    }
}
```

```
wasm execute <auction-contract-address> '{"claim": {"token_id": "1","token_address":"<cw721-contract-address>"}}'  
```

[Transaction](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/D10B39D4629CABA4C7F8F98E7D0FE169067BA913B4178DB5C993FC38FB0011AD)

The funds will be sent to the seller and the NFT to the winning bid. We can check the NFT owner after claiming by querying our NFT contract:&#x20;

```
wasm query <cw721-contract-address> '{"owner_of": {"token_id": "1","include_expired": false}}'
```

The returned owner should be the highest bidder.

### Conclusion

Congratulations, we have built a fully functioning auctioning App that allows you to auction off your NFTs. Keep in mind that all the apps we build in these examples are highly customizable to satisfy user needs. We can change the accepted funds, add/remove whitelists, and even add more components to our App if needed.&#x20;
