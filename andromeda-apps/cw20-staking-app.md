---
description: Deploying a cw20 staking App using the Andromeda CLI.
---

# Cw20 Staking App

If unfimailar with the steps of deploying an app, go back to the [first example](crowdfunding-app.md) where we explain in detail all the different parts of deploying an App.

{% hint style="warning" %}
If any of the messages in this example do not work, you might want to cross reference the messages with the ADO specific section which always contains the latest ADO versions to make sure they are correct. Other than that the logic will remain the same.

Make sure to read the [Introduction to Apps](introduction-to-apps.md) before going through building an app.
{% endhint %}

### Defining our App

For this example, we will assume that we have a project that wants to create their own cw-20 token that can be staked for rewards.

The steps we will perform:&#x20;

* Mint some coins to different addresses
* Stake some of the coins for rewards of our choice&#x20;
* Check that all the results are as expected

### Defining the Messages

The components/ADOs we would need:

* [CW20](../andromeda-digital-objects/cw20.md)
* [Cw-20 staking](../andromeda-digital-objects/cw20-staking.md)

{% hint style="warning" %}
Although not necessary, if you are unfamiliar with these ADOs, it is suggested to read through each of the them before deploying an app.&#x20;
{% endhint %}

Let us first start by representing the instantiation message for each of our ADOs:

{% hint style="warning" %}
Keep in mind that the app takes these messages as base64 encoded.
{% endhint %}

#### Cw-20 Token

I will start out by giving 10000 tokens to three addresses. We have not set a minter, meaning no additional tokens can ever be minted. I specify 3 addresses to receive an initial balance.

{% hint style="warning" %}
**DO NOT** use the same addresses for the initial balances. Instead use some of your addresses  (Create new ones if you dont have).
{% endhint %}

```json
{
 "name": "Staking Token",
 "symbol":"STK",
 "decimals":6,
 "initial_balances":[
                {
              "address":"andr1cp9vxvplgjf508thtacvxeh6qxqsx67gseqcd5",
              "amount":"10000"
          },
                   {
              "address":"andr1f9q5dhg5q434jyjes002fvkcuk062tnt0wdzl8",
              "amount":"10000"
          },
                   {
              "address":"andr1e9qgel058tvgryvsu8uhta0u4sswajre3cjhav",
              "amount":"10000"
          }
             
        ]
 }  
```

As Base64:&#x20;

```
eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6ImFuZHIxY3A5dnh2cGxnamY1MDh0aHRhY3Z4ZWg2cXhxc3g2N2dzZXFjZDUiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZjlxNWRoZzVxNDM0anlqZXMwMDJmdmtjdWswNjJ0bnQwd2R6bDgiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZTlxZ2VsMDU4dHZncnl2c3U4dWh0YTB1NHNzd2FqcmUzY2poYXYiLCJhbW91bnQiOiIxMDAwMCJ9XX0gIA==
```

#### Cw-20 Staking

{% hint style="warning" %}
We have not set any additional rewards. This means that the rewards are the same as the staking token as most staking works. If you want, you can add up to 10 different reward tokens to the staking.
{% endhint %}

```json
{
"staking_token":{
          "identifier":"cw20"
              }
     }
```

As Base64:

```
eyJzdGFraW5nX3Rva2VuIjp7ImlkZW50aWZpZXIiOiJjdzIwIn19
```

#### App

{% hint style="warning" %}
The primitive address used here might be outdated in the future. Check our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>) to get the latest registry.

The `instantiate_message` of the cw20 will be different for you since you are using different addresses to receive initial balances.
{% endhint %}

```json
{
  "name": "Staking App",
  "app_components": [
    {
      "name": "cw20",
      "ado_type": "cw20",
      "instantiate_msg": "eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6ImFuZHIxY3A5dnh2cGxnamY1MDh0aHRhY3Z4ZWg2cXhxc3g2N2dzZXFjZDUiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZjlxNWRoZzVxNDM0anlqZXMwMDJmdmtjdWswNjJ0bnQwd2R6bDgiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZTlxZ2VsMDU4dHZncnl2c3U4dWh0YTB1NHNzd2FqcmUzY2poYXYiLCJhbW91bnQiOiIxMDAwMCJ9XX0gIA=="
    },
    {
      "name": "staking",
      "ado_type": "cw20-staking",
      "instantiate_msg": "eyJzdGFraW5nX3Rva2VuIjp7ImlkZW50aWZpZXIiOiJjdzIwIn19"
    }
  ],
  "primitive_contract": "andr1mfxffyrda922hpt23f7hpf8t50sadcwavkt54cuyl3w6p4zwz36sc9hfj6"
  }
```

We have already uploaded the App ADO to the uni-5 testnet. The code I will use is 98. This code Id will most likely be outdated in the future. A simple way to check the latest code Id for the App ADO is to query it from the ADODB using the the chain you want to use.&#x20;

### Instantiating the App

If you do not have the CLI downloaded, go to the [Introduction to Apps](introduction-to-apps.md) section to get the latest version.

First, let us open the CLI by running `andr` in our terminal. We then need to choose the chain we want to deploy on. Run `"chain use"` in the CLI and select the testnet that you want to deploy on. For this example I will be using the Andromeda test-net Galileo-3.

{% hint style="warning" %}
If this is the first time using the CLI make sure to run `"wallet add <wallet-name>"`in order to create a wallet. Then go to that chain's faucet (usually located in their discord) and request tokens.
{% endhint %}

We have already uploaded the App ADO to the Andromeda testnet. The code Id I will use is 55. This code Id will most likely be outdated in the future. A simple way to check the latest code id for the App ADO is to query it from the ADODB using the chain you want to use.&#x20;

In the CLI, while connected to the chain of choice, run:

```
ado db getcodeid app
```

The code Id to use will be returned.

&#x20;Now we can instantiate our App. We will be using our wasm command to instantiate our app:

{% hint style="warning" %}
Do not copy and paste the command below as it will not work. Make sure to make the appropriate changes before running the command.&#x20;

Replace the \<code\_id> with the returned code-id for the app ADO.&#x20;
{% endhint %}

```
wasm instantiate <app-code-id> '{"name": "Staking App","app_components": [{"name": "cw20","ado_type": "cw20","instantiate_msg": "eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6ImFuZHIxY3A5dnh2cGxnamY1MDh0aHRhY3Z4ZWg2cXhxc3g2N2dzZXFjZDUiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZjlxNWRoZzVxNDM0anlqZXMwMDJmdmtjdWswNjJ0bnQwd2R6bDgiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZTlxZ2VsMDU4dHZncnl2c3U4dWh0YTB1NHNzd2FqcmUzY2poYXYiLCJhbW91bnQiOiIxMDAwMCJ9XX0gIA=="},{"name": "staking","ado_type": "cw20-staking","instantiate_msg": "eyJzdGFraW5nX3Rva2VuIjp7ImlkZW50aWZpZXIiOiJjdzIwIn19"}],"primitive_contract": "andr1mfxffyrda922hpt23f7hpf8t50sadcwavkt54cuyl3w6p4zwz36sc9hfj6"}'
```

[Instantiation](https://testnet-ping.wildsage.io/andromeda/tx/B51E814F547547482CE9FCAA8B60DE8366ABF7A2B21E979AD3B62959DD1A9C66)

{% hint style="warning" %}
You will be using the contract addresses that were instantiated for you instead of the ones in this tutorial.
{% endhint %}

### Interacting with the App

Andromeda contracts assign the original instantiator as contract owners. Due to how instantiation via a submessage operates in CosmWasm contracts, we must claim ownership of the components in order to execute messages that have authorization restrictions (owner restricted).

{% hint style="warning" %}
You can also use proxy messages to execute through the app ADO itself, but for these examples we will be claiming ownership.
{% endhint %}

#### Claim Components

```json
{
"claim_ownership":{}
}
```

```
  wasm execute <app_contract_address> '{"claim_ownership":{}}' 
```

[Claim Components](https://testnet-ping.wildsage.io/andromeda/tx/516099A2ECD274768D3F98E6FC82DDF2FE326007EBDA7FBD3F6B8D21F5AB28DF)

#### User 1 stakes tokens

To stake tokens we perform a `send` message from Token contract and attach the `Stake{}` message to it. For user 1 we will stake 3000 tokens.&#x20;

{% hint style="warning" %}
This message needs to be performed by the address user 1. This means you need to switch wallets to one of the addresses that you specified when distributing initial balances.
{% endhint %}

```json
{
 "send":{
    "contract":"<staking-contract-address>",
    "amount":"3000",
    "msg":"ewogICAgInN0YWtlX3Rva2VucyI6e30KICAgIAp9"
    }
 }
```

Here the attached message is:

```json
{
"stake_tokens":{}
}
```

Let us execute it:

```
wasm execute <cw20-contract-address> '{"send":{"contract":"<staking-contract-address>","amount":"3000","msg":"ewogICAgInN0YWtlX3Rva2VucyI6e30KICAgIAp9"}}' 
```

[User 1 stakes 3000 tokens](https://testnet-ping.wildsage.io/andromeda/tx/FCC44C46879F204987DBC65316DA900695791A34C6E62E7ADF2A9EEC9F6E709F)

We perform the same steps to stake for users 2 and 3:

{% hint style="warning" %}
Make sure to change wallet for each user and change the amount being staked.
{% endhint %}

[User 2 stakes 1000 tokens ](https://testnet-ping.wildsage.io/andromeda/tx/08AF4FDA3B41998AAF9C1FF25DED0C27EE450DE3CDE41FDC237C6C1099714875)

[User 3 stakes 6000 tokens](https://testnet-ping.wildsage.io/andromeda/tx/A0A664FD3E2F65825556527B13CB6244DD6F7C8218CCD7A45F34F27B5D12D9B7)

#### Checking Pending Rewards

Let us check the staker data for user 1:

```
wasm query  <staking-contract-address> '{"staker":{"address":"<user-address>"}}' 
```

#### Result:

```
{
  "address": "andr1cp9vxvplgjf508thtacvxeh6qxqsx67gseqcd5",
  "share": "3000",
  "balance": "3000",
  "pending_rewards": []
}
```

{% hint style="warning" %}
Here we check one of the stakers. As we can see, the pending rewards are empty and the balance is the same as the share.&#x20;
{% endhint %}

#### Transfer Some Tokens as Rewards

Now let us send 1000 tokens to the contract. By doing this, we are telling the contract to use them as rewards for stakers.

{% hint style="warning" %}
You can use any of the wallets that cw20 tokens remaining.&#x20;
{% endhint %}

```json
{
 "transfer":{
      "recipient":"<staking-contract-address>",
      "amount": "1000"
      }
 }
```

```
wasm execute <cw20-contract-address> '{"transfer":{"recipient":"<staking-contract-address>","amount": "1000"}}'
```

Now since the token we sent is the same one being staked, then the rewards are distributed automatically to the stakers, and there is no need to call `Claim` or `UpdateGlobalIndex`.\


Let us check the new balances of the users:

#### User 1:

```
wasm query  <staking-contract-address> '{"staker":{"address":"<user1-address>"}}' 
```

#### Result:

```
{
  "address": "andr1cp9vxvplgjf508thtacvxeh6qxqsx67gseqcd5",
  "share": "3000",
  "balance": "3300",
  "pending_rewards": []
}
```

#### User 2:

```
wasm query  <staking-contract-address> '{"staker":{"address":"<user2-address>"}}' 
```

#### Result:

```
{
  "address": "andr1f9q5dhg5q434jyjes002fvkcuk062tnt0wdzl8",
  "share": "1000",
  "balance": "1100",
  "pending_rewards": []
}
```

#### User 3:

```
wasm query  <staking-contract-address> '{"staker":{"address":"<user3-address>"}}' 
```

#### Result:

```
{
  "address": "andr1e9qgel058tvgryvsu8uhta0u4sswajre3cjhav",
  "share": "6000",
  "balance": "6600",
  "pending_rewards": []
}
```

As we can see each of the stakers got the amount proportional to the amount staked.&#x20;

#### Conclusion

This was a very simple App that showcases our cw20-staking contract. If you want to play around with it more, you can built it using the allocated rewards and add up to 10 different token rewards. It would also allow you to spread the rewards distribution to be in cycles instead of instant as we got here. This App although simple, is needed in most web3 projects that launch their own token. With Andromeda, you can have it all setup easily like we saw in this example or even using our no-code builder App which takes minutes.
