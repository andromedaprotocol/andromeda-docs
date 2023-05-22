---
description: Deploying a cw20 staking App using the Andromeda CLI.
---

# Cw20 Staking App

If unfimailar with the steps of deploying an app, go back to the [first example](crowdfunding-app.md) where we explain in detail all the different parts of deploying an App.

{% hint style="warning" %}
If any of the messages in this example do not work, you might want to cross reference the messages with the ADO specific section which always contains the latest ADO versions to make sure they are correct. Other than that the logic will remain the same.

The examples use uni-5 testnet. As of now, Juno has upgraded to uni-6. The steps of building the App remain the same.
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

I will start out by giving 10000 tokens to three addresses. We have not set a minter, meaning no additional tokens can ever be minted.

```json
{
 "name": "Staking Token",
 "symbol":"STK",
 "decimals":6,
 "initial_balances":[
                {
              "address":"juno1zkpthqsz3ud97fm6p4kxcra8ae99jgzauugyem",
              "amount":"10000"
          },
                   {
              "address":"juno1e53vtk7fmqzfttdvpf4a3pyx0e79wkmjzh6qsk",
              "amount":"10000"
          },
                   {
              "address":"juno19s3l3wh5a3w5dpyv6v2342aej39mwmjea8vpsn",
              "amount":"10000"
          }
             
        ]
 }  
```

As Base64:&#x20;

```
eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6Imp1bm8xemtwdGhxc3ozdWQ5N2ZtNnA0a3hjcmE4YWU5OWpnemF1dWd5ZW0iLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xZTUzdnRrN2ZtcXpmdHRkdnBmNGEzcHl4MGU3OXdrbWp6aDZxc2siLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xOXMzbDN3aDVhM3c1ZHB5djZ2MjM0MmFlajM5bXdtamVhOHZwc24iLCJhbW91bnQiOiIxMDAwMCJ9IF19ICA=
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
{% endhint %}

```json
{
  "name": "Staking App",
  "app": [
    {
      "name": "cw20",
      "ado_type": "cw20",
      "instantiate_msg": "eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6Imp1bm8xemtwdGhxc3ozdWQ5N2ZtNnA0a3hjcmE4YWU5OWpnemF1dWd5ZW0iLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xZTUzdnRrN2ZtcXpmdHRkdnBmNGEzcHl4MGU3OXdrbWp6aDZxc2siLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xOXMzbDN3aDVhM3c1ZHB5djZ2MjM0MmFlajM5bXdtamVhOHZwc24iLCJhbW91bnQiOiIxMDAwMCJ9IF19ICA="
    },
    {
      "name": "staking",
      "ado_type": "cw20-staking",
      "instantiate_msg": "eyJzdGFraW5nX3Rva2VuIjp7ImlkZW50aWZpZXIiOiJjdzIwIn19"
    }
  ],
  "primitive_contract": "juno133fdsnvcah870exzcyxknydswyh778jfhwxzlhhgjuagh4482zpqp856dz"
  }
```

### Instantiating the App

If you do not have the CLI downloaded, go to the [Introduction to Apps](introduction-to-apps.md) section to get the latest version.

First, let us open the CLI by running `andr` in our terminal. We then need to choose the chain we want to deploy on. Run `"chain use"` in the CLI and select the testnet that you want to deploy on. For this example I will be using the juno testnet uni-5.

{% hint style="warning" %}
If this is the first time using the CLI make sure to run `"wallet add <wallet-name>"`in order to create a wallet. Then go to that chain's faucet (usually located in their discord) and request tokens.
{% endhint %}

We have already uploaded the App ADO to the uni-5 testnet. The code I will use is 98. This code Id will most likely be outdated in the future. A simple way to check the latest code id for the App ADO is to query it from the ADODB using the the chain you want to use.&#x20;

&#x20;In the CLI, while connected to the chain of choice, run:

```
ado db getcodeid app
```

The code Id to use will be returned.

&#x20;Now we can instantiate our App. We will be using our wasm command to instantiate our app:

```
wasm instantiate 98 '{"name": "Staking App","app": [{"name": "cw20","ado_type": "cw20","instantiate_msg": "eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6Imp1bm8xemtwdGhxc3ozdWQ5N2ZtNnA0a3hjcmE4YWU5OWpnemF1dWd5ZW0iLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xZTUzdnRrN2ZtcXpmdHRkdnBmNGEzcHl4MGU3OXdrbWp6aDZxc2siLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xOXMzbDN3aDVhM3c1ZHB5djZ2MjM0MmFlajM5bXdtamVhOHZwc24iLCJhbW91bnQiOiIxMDAwMCJ9IF19ICA="},{"name": "staking","ado_type": "cw20-staking","instantiate_msg": "eyJzdGFraW5nX3Rva2VuIjp7ImlkZW50aWZpZXIiOiJjdzIwIn19"}],"primitive_contract": "juno133fdsnvcah870exzcyxknydswyh778jfhwxzlhhgjuagh4482zpqp856dz"}'
```

[Instantiation](https://testnet.mintscan.io/juno-testnet/txs/1EE4CC4DEB10C568A8D8B6028AAB7727665EBEAAD35C8A561E07C9544CC355B7)

{% hint style="warning" %}
You will be using the contract addresses that were instantiated for you instead of the ones in this tutorial. The addresses are found in the explorer in the "Instantiates" section.
{% endhint %}

### Interacting with the App

Andromeda contracts assign the original instantiator as contract owners. Due to how instantiation via a submessage operates in CosmWasm contracts, we must claim ownership of the components in order to execute messages that have authorization restrictions (owner restricted).

#### Claim Components

```json
{
"claim_ownership":{}
}
```

```
 wasm execute juno1mgwet8dksqsjvx2u7evy9wyxwtj5cp8gftw7k4zzh4cqsdmaa38ql7sueh '{"claim_ownership":{}}'
```

[Claim Components](https://testnet.mintscan.io/juno-testnet/txs/622CC57AE96A031C8169E34DCDE2C1B8ABE5548E90A87E832ACCDB709148BBDA)

#### User 1 stakes tokens

To stake tokens we perform a `send` message from Token contract and attach the `Stake{}` message to it. For user 1 we will stake 3000 tokens.&#x20;

```json
{
 "send":{
    "contract":"juno15vu55e5hvegppl2nr4dkqrhtmtl4qdspt6vlpld8ult55s95mlgsql7ydu",
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
wasm execute juno17ssms3dz3n88ltauh2735df6qslxttpxst0njsd8nx3x9vde5yfqyztl5p '{"send":{"contract":"juno15vu55e5hvegppl2nr4dkqrhtmtl4qdspt6vlpld8ult55s95mlgsql7ydu","amount":"3000","msg":"ewogICAgInN0YWtlX3Rva2VucyI6e30KICAgIAp9"}}' 
```

[User 1 stakes 3000 tokens](https://testnet.mintscan.io/juno-testnet/txs/F61883C135D4C30F9637DE681B01FCD80E1DA4F02B28E42563E84C89C3ECFE66)

We perform the same steps to stake for users 2 and 3:

{% hint style="warning" %}
Make sure to change wallet for each user.
{% endhint %}

[User 2 stakes 1000 tokens ](https://testnet.mintscan.io/juno-testnet/txs/0F5A429161E3497CC3F352BFA4B97F540BE2A564ADD22B255BE1FCD880C18CAA)

[User 3 stakes 6000 tokens](https://testnet.mintscan.io/juno-testnet/txs/83E89F0F8010EF80A9BEA4468B6C2B2D8E1F19F18D7D0D84C469E65EF0D2C847)

#### Checking Pending Rewards

Let us check the staker data for user 1:

```
junod query wasm contract-state smart juno1wf526ma0mjskhlgrun9qkytelnfsl5pny3l2gwvpgjqwhcrx2q8qpqg53f '{"staker":{"address":"juno197y44w58djquqkpu8v56pgwvm058f6jswm0jec"}}' 
```

#### Result:

```
– Querying contract...
{
  "address": "juno1zkpthqsz3ud97fm6p4kxcra8ae99jgzauugyem",
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

```json
{
 "transfer":{
      "recipient":"juno15vu55e5hvegppl2nr4dkqrhtmtl4qdspt6vlpld8ult55s95mlgsql7ydu",
      "amount": "1000"
      }
 }
```

```
 wasm execute juno17ssms3dz3n88ltauh2735df6qslxttpxst0njsd8nx3x9vde5yfqyztl5p '{"transfer":{"recipient":"juno15vu55e5hvegppl2nr4dkqrhtmtl4qdspt6vlpld8ult55s95mlgsql7ydu","amount": "1000"}}' 
```

[Transfer Tokens](https://testnet.mintscan.io/juno-testnet/txs/B8E6F22D383D14B1365D125AF73CBD38E07BEBDCCB76F0A71E8420DCA99AB92D)

Now since the token we sent is the same one being staked, then the rewards are distributed automatically to the stakers, and there is no need to call `Claim` or `UpdateGlobalIndex`.\


Let us check the new balances of the users:

#### User 1:

```
wasm query juno15vu55e5hvegppl2nr4dkqrhtmtl4qdspt6vlpld8ult55s95mlgsql7ydu '{"staker":{"address":"juno1zkpthqsz3ud97fm6p4kxcra8ae99jgzauugyem"}}'
```

#### Result:

```
- Querying contract...
{
  "address": "juno1zkpthqsz3ud97fm6p4kxcra8ae99jgzauugyem",
  "share": "3000",
  "balance": "3300",
  "pending_rewards": []
}
```

#### User 2:

```
wasm query juno15vu55e5hvegppl2nr4dkqrhtmtl4qdspt6vlpld8ult55s95mlgsql7ydu '{"staker":{"address":"juno1e53vtk7fmqzfttdvpf4a3pyx0e79wkmjzh6qsk"}}' 
```

#### Result:

```
– Querying contract...
{
  "address": "juno1e53vtk7fmqzfttdvpf4a3pyx0e79wkmjzh6qsk",
  "share": "1000",
  "balance": "1100",
  "pending_rewards": []
}
```

#### User 3:

```
wasm query  juno15vu55e5hvegppl2nr4dkqrhtmtl4qdspt6vlpld8ult55s95mlgsql7ydu '{"staker":{"address":"juno19s3l3wh5a3w5dpyv6v2342aej39mwmjea8vpsn"}}' 
```

#### Result:

```
— Querying contract...
{
  "address": "juno19s3l3wh5a3w5dpyv6v2342aej39mwmjea8vpsn",
  "share": "6000",
  "balance": "6600",
  "pending_rewards": []
}
```

As we can see each of the stakers got the amount proportional to the amount staked.&#x20;

#### Conclusion

This was a very simple App that showcases our cw20-staking contract. If you want to play around with it more, you can built it using the allocated rewards and add up to 10 different token rewards. It would also allow you to spread the rewards distribution to be in cycles instead of instant as we got here. This App although simple, is needed in most web3 projects that launch their own token. With Andromeda, you can have it all setup easily like we saw in this example or even using our no-code builder App which takes minutes.
