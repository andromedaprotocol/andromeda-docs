---
description: Deploying a cw20 staking App using the Andromeda CLI.
---

# Cw20 Staking App

If unfamiliar with the steps of deploying an app, go back to the [first example](crowdfunding-app.md) where we explain in detail all the different parts of deploying an App.

{% hint style="warning" %}
If any of the messages in this example do not work, you might want to cross reference the messages with the ADO specific section which always contains the latest ADO versions to make sure they are correct. Other than that the logic will remain the same.

Make sure to read the [Introduction to Apps](introduction-to-apps.md) before going through building an app.
{% endhint %}

## Defining our App

For this example, we will assume that we have a project that wants to create their own cw-20 token that can be staked for rewards.

The steps we will perform:&#x20;

* Mint some coins to different addresses
* Stake some of the coins for rewards of our choice&#x20;
* Check that all the results are as expected

## Defining the Instantiation Messages

The components/ADOs we would need:

* [CW20](../andromeda-digital-objects/cw20-v1.0.0.md)
* [CW20 staking](../andromeda-digital-objects/cw20-staking-v1.0.0.md)

{% hint style="warning" %}
Although not necessary, if you are unfamiliar with these ADOs, it is suggested to read through each of the them before deploying an app.&#x20;
{% endhint %}

Let us first start by representing the instantiation message for each of our ADOs:

{% hint style="warning" %}
Keep in mind that the app takes these messages as base64 encoded.
{% endhint %}

### CW20 Token

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
              "address":"andr1v0an0ar6js3kwmgwr6pp5kuswm6wh0gwdnjska",
              "amount":"10000"
          },
                   {
              "address":"andr1dzrcm9swj22v2d05sqfktlwrz3u8hskgujyqhk",
              "amount":"10000"
          },
                   {
              "address":"andr1gzk2m0k40e30wn6wv9j9kcrx4ljlk4ks8whrgq",
              "amount":"10000"
          }
             
        ],
"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"
 }  
```

As Base64:&#x20;

```
eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6ImFuZHIxdjBhbjBhcjZqczNrd21nd3I2cHA1a3Vzd202d2gwZ3dkbmpza2EiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZHpyY205c3dqMjJ2MmQwNXNxZmt0bHdyejN1OGhza2d1anlxaGsiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZ3prMm0wazQwZTMwd242d3Y5ajlrY3J4NGxqbGs0a3M4d2hyZ3EiLCJhbW91bnQiOiIxMDAwMCJ9XSwia2VybmVsX2FkZHJlc3MiOiJhbmRyMTRoajJ0YXZxOGZwZXNkd3h4Y3U0NHJ0eTNoaDkwdmh1anJ2Y21zdGw0enIzdHhtZnZ3OXNocHRrcWwifSAg
```

### CW20 Staking

{% hint style="warning" %}
We have not set any additional rewards. This means that the rewards are the same as the staking token as most staking works. If you want, you can add up to 10 different reward tokens to the staking.
{% endhint %}

```json
{
"staking_token":"./cw20",
"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"
}
```

As Base64:

```
eyJzdGFraW5nX3Rva2VuIjoiLi9jdzIwIiwia2VybmVsX2FkZHJlc3MiOiJhbmRyMTRoajJ0YXZxOGZwZXNkd3h4Y3U0NHJ0eTNoaDkwdmh1anJ2Y21zdGw0enIzdHhtZnZ3OXNocHRrcWwifQ==
```

### App

{% hint style="warning" %}
The `instantiate_message` of the cw20 will be different for you since you are using different addresses to receive initial balances.
{% endhint %}

```json
{
  "name": "Staking App",
  "app_components": [
    {
      "name": "cw20",
      "ado_type": "cw20",
      "component_type":{
       "new":"eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6ImFuZHIxdjBhbjBhcjZqczNrd21nd3I2cHA1a3Vzd202d2gwZ3dkbmpza2EiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZHpyY205c3dqMjJ2MmQwNXNxZmt0bHdyejN1OGhza2d1anlxaGsiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZ3prMm0wazQwZTMwd242d3Y5ajlrY3J4NGxqbGs0a3M4d2hyZ3EiLCJhbW91bnQiOiIxMDAwMCJ9XSwia2VybmVsX2FkZHJlc3MiOiJhbmRyMTRoajJ0YXZxOGZwZXNkd3h4Y3U0NHJ0eTNoaDkwdmh1anJ2Y21zdGw0enIzdHhtZnZ3OXNocHRrcWwifSAg"
       }
    },
    {
      "name": "staking",
      "ado_type": "cw20-staking",
      "component_type":{
       "new":"eyJzdGFraW5nX3Rva2VuIjoiLi9jdzIwIiwia2VybmVsX2FkZHJlc3MiOiJhbmRyMTRoajJ0YXZxOGZwZXNkd3h4Y3U0NHJ0eTNoaDkwdmh1anJ2Y21zdGw0enIzdHhtZnZ3OXNocHRrcWwifQ=="
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

<pre><code><a data-footnote-ref href="#user-content-fn-1">wasm</a> instantiate "&#x3C;app-code-id>" '{"name": "Staking App","app_components": [{"name": "cw20","ado_type": "cw20","component_type":{"new":"eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6ImFuZHIxdjBhbjBhcjZqczNrd21nd3I2cHA1a3Vzd202d2gwZ3dkbmpza2EiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZHpyY205c3dqMjJ2MmQwNXNxZmt0bHdyejN1OGhza2d1anlxaGsiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6ImFuZHIxZ3prMm0wazQwZTMwd242d3Y5ajlrY3J4NGxqbGs0a3M4d2hyZ3EiLCJhbW91bnQiOiIxMDAwMCJ9XSwia2VybmVsX2FkZHJlc3MiOiJhbmRyMTRoajJ0YXZxOGZwZXNkd3h4Y3U0NHJ0eTNoaDkwdmh1anJ2Y21zdGw0enIzdHhtZnZ3OXNocHRrcWwifSAg"}},{"name": "staking","ado_type": "cw20-staking","component_type":{"new":"eyJzdGFraW5nX3Rva2VuIjoiLi9jdzIwIiwia2VybmVsX2FkZHJlc3MiOiJhbmRyMTRoajJ0YXZxOGZwZXNkd3h4Y3U0NHJ0eTNoaDkwdmh1anJ2Y21zdGw0enIzdHhtZnZ3OXNocHRrcWwifQ=="}}],"kernel_address":"andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql"}'
</code></pre>

[Instantiation](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/6C060BBCB7AA6E77D0E2B8E49C9A0E0FCFB29A51E7B7688C2EF07F831F14508E)

{% hint style="warning" %}
You will be using the contract addresses that were instantiated for you instead of the ones in this tutorial.
{% endhint %}

## Interacting with the App

### User 1 stakes tokens

To stake tokens we perform a `send` message from CW20w contract and attach the `Stake{}` message to it. For user 1 we will stake 3000 tokens.&#x20;

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

[User 1 stakes 3000 tokens](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/0BBA32D2416B382DA40DA88380F8425B4684696B3BD26876B91E8CBB7F94E154)

We perform the same steps to stake for users 2 and 3:

{% hint style="warning" %}
Make sure to change wallet for each user and change the amount being staked.
{% endhint %}

[User 2 stakes 1000 tokens](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/72F422269C8EDD7F070154ABCE00B9199671E9A33D15EBAA82DAD0E0FE0E68EC)&#x20;

[User 3 stakes 6000 tokens](https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/497BC9EAB79E19A1A7329A586C1680D93D18BC00409A2396D0CDC404E9EE3A40)

### Checking Pending Rewards

Let us check the staker data for user 1:

```
wasm query  <staking-contract-address> '{"staker":{"address":"<user-address>"}}' 
```

#### Result:

```
{
  "address": "andr1v0an0ar6js3kwmgwr6pp5kuswm6wh0gwdnjska",
  "share": "3000",
  "balance": "3000",
  "pending_rewards": []
}
```

{% hint style="warning" %}
Here we check one of the stakers. As we can see, the pending rewards are empty and the balance is the same as the share.&#x20;
{% endhint %}

### Transfer Some Tokens as Rewards

Now let us send 1000 tokens to the contract. By doing this, we are telling the contract to use them as rewards for stakers.

{% hint style="warning" %}
You can use any of the wallets that have cw20 tokens remaining.  This is usually done by the project owner address.w
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

Now since the token we sent is the same one being staked, then the rewards are distributed automatically to the stakers, and there is no need to call `Claim` or `UpdateGlobalIndex`.

Let us check the new balances of the users:

#### User 1:

```
wasm query  <staking-contract-address> '{"staker":{"address":"<user1-address>"}}' 
```

#### Result:

```
{
  "address": "andr1v0an0ar6js3kwmgwr6pp5kuswm6wh0gwdnjska",
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
  "address": "andr1dzrcm9swj22v2d05sqfktlwrz3u8hskgujyqhk",
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
  "address": "andr1gzk2m0k40e30wn6wv9j9kcrx4ljlk4ks8whrgq",
  "share": "6000",
  "balance": "6600",
  "pending_rewards": []
}
```

As we can see each of the stakers got the amount proportional to the amount staked. Each user can now claim these tokens by calling the [ClaimRewards](../andromeda-digital-objects/cw20-staking-v1.0.0.md#claimrewards) message.

### Conclusion

This was a very simple App that showcases our cw20-staking contract. If you want to play around with it more, you can built it using the allocated rewards and add up to 10 different token rewards. It would also allow you to spread the rewards distribution to be in cycles instead of instant as we got here. This App although simple, is needed in most web3 projects that launch their own token. With Andromeda, you can have it all setup easily like we saw in this example or even using our no-code builder App which takes minutes.

[^1]: 
