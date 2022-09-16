# Cw-20 Staking App

If unfimailar with the steps of deploying an app, go back to the [first example](crowdfunding-app.md) where we explain in detail all the different parts of deploying an App.

{% hint style="warning" %}
If any of the messages in this example do not work, you might want to cross reference the messages with the ADO specific section which always contains the latest ADO versions to make sure they are correct. Other than that the logic will remain the same.
{% endhint %}

### Defining our App

For this example, we will assume that we have a project that wants to create their own cw-20 token that can be staked for rewards.

The steps we will perform:&#x20;

* Mint some coins to different addresses
* Stake some of the coins for rewards of our choice&#x20;
* Check that all the results work as intended

### Instantiating the App

The components/ADOs we would need:

* [Cw-20 token](../andromeda-digital-objects/cw20.md)
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
              "address":"juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll",
              "amount":"10000"
          },
                   {
              "address":"juno197y44w58djquqkpu8v56pgwvm058f6jswm0jec",
              "amount":"10000"
          },
                   {
              "address":"juno1nekdhtj60s692g28e4cq5yvpg9aqd4yeafu07t",
              "amount":"10000"
          }
             
        ]
 }  
```

As Base64:&#x20;

```
eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6Imp1bm8xaGZuZ3doNGY1M3NtdGh2cWRlMzJ5MDdyamNlemRzbmx6dHh0bGwiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xOTd5NDR3NThkanF1cWtwdTh2NTZwZ3d2bTA1OGY2anN3bTBqZWMiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xbmVrZGh0ajYwczY5MmcyOGU0Y3E1eXZwZzlhcWQ0eWVhZnUwN3QiLCJhbW91bnQiOiIxMDAwMCJ9IF19ICA=
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

```json
{
  "name": "Staking App",
  "app": [
    {
      "name": "cw20",
      "ado_type": "cw20",
      "instantiate_msg": "eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6Imp1bm8xaGZuZ3doNGY1M3NtdGh2cWRlMzJ5MDdyamNlemRzbmx6dHh0bGwiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xOTd5NDR3NThkanF1cWtwdTh2NTZwZ3d2bTA1OGY2anN3bTBqZWMiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xbmVrZGh0ajYwczY5MmcyOGU0Y3E1eXZwZzlhcWQ0eWVhZnUwN3QiLCJhbW91bnQiOiIxMDAwMCJ9IF19ICA"
    },
    {
      "name": "staking",
      "ado_type": "cw20_staking",
      "instantiate_msg": "eyJzdGFraW5nX3Rva2VuIjp7ImlkZW50aWZpZXIiOiJjdzIwIn19"
    }
  ],
  "operators": [],
  "primitive_contract": "juno144r95jrnk5erqhpuepe5mwz33e5mns7yuyhmxw7fmfx8w0duqwws9qd36x"
  }
```

First let us upload the app contract to JUNO test-net:

{% hint style="warning" %}
We dont have to repeat this step for every app we build. We can use the same code-id of other apps, but for the sake of the example, I will be reuploading it as if this is my first app.
{% endhint %}

```
junod tx wasm store andromeda_app_contract.wasm  --from juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --chain-id=uni-3 --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y
```

[Upload the Contract](https://testnet.mintscan.io/juno-testnet/txs/568B5263285613A0569F97B3F997F5926D70F64D714F0AE4DB172A9CB8B38B77)

Now let us instantiate the App using its message:

```
junod tx wasm instantiate 977 '{"name": "Staking App","app": [{"name": "cw20","ado_type": "cw20","instantiate_msg": "eyJuYW1lIjogIlN0YWtpbmcgVG9rZW4iLCJzeW1ib2wiOiJTVEsiLCJkZWNpbWFscyI6NiwiaW5pdGlhbF9iYWxhbmNlcyI6W3siYWRkcmVzcyI6Imp1bm8xaGZuZ3doNGY1M3NtdGh2cWRlMzJ5MDdyamNlemRzbmx6dHh0bGwiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xOTd5NDR3NThkanF1cWtwdTh2NTZwZ3d2bTA1OGY2anN3bTBqZWMiLCJhbW91bnQiOiIxMDAwMCJ9LHsiYWRkcmVzcyI6Imp1bm8xbmVrZGh0ajYwczY5MmcyOGU0Y3E1eXZwZzlhcWQ0eWVhZnUwN3QiLCJhbW91bnQiOiIxMDAwMCJ9IF19ICA"},{"name": "staking","ado_type": "cw20_staking","instantiate_msg": "eyJzdGFraW5nX3Rva2VuIjp7ImlkZW50aWZpZXIiOiJjdzIwIn19"}],"operators": [],"primitive_contract": "juno144r95jrnk5erqhpuepe5mwz33e5mns7yuyhmxw7fmfx8w0duqwws9qd36x"}' --label "Auction App" --from  juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y --no-admin
```

[Instantiation](https://testnet.mintscan.io/juno-testnet/txs/82368FCE23C3E789F9E7220D78C0C9720BB7BABB500F4495F50A7DED56CE677F)

### Interacting with the App

Andromeda contracts assign the original instantiator as contract owners. Due to how instantiation via a submessage operates in CosmWasm contracts, we must claim ownership of the components in order to execute messages that have authorization restrictions (owner restricted).

#### Claim Components

```json
{
"claim_ownership":{}
}
```

```
junod tx wasm execute juno1fzkpu2fcppzf8wadurm6sw08pq3ww657r9ggk9z6w6pm9jxa4ckqs45u24 '{"claim_ownership":{}}' --from juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y
```

[Claim Components](https://testnet.mintscan.io/juno-testnet/txs/E04C0CDA3F9677F59DA24AE45C325DDDF390FD8D9F739EE5E29628795726B25F)

#### User 1 stakes tokens

To stake tokens we perform a `send` message from Token contract and attach the `Stake{}` message to it. For user 1 we will stake 3000 tokens.&#x20;

```json
{
 "send":{
    "contract":"juno1wf526ma0mjskhlgrun9qkytelnfsl5pny3l2gwvpgjqwhcrx2q8qpqg53f",
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
junod tx wasm execute juno1cngmjgaf9jqh8ukc6j75ygke6vdajpkx3dkphhus45agghd2tuqs06kut7 '{"send":{"contract":"juno1wf526ma0mjskhlgrun9qkytelnfsl5pny3l2gwvpgjqwhcrx2q8qpqg53f","amount":"3000","msg":"ewogICAgInN0YWtlX3Rva2VucyI6e30KICAgIAp9"}}' --from juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y
```

[User 1 stakes 3000 tokens](https://testnet.mintscan.io/juno-testnet/txs/4F78DEF5D7F332BAAE6457F18AEF3B3A94730D5CF1276203767C7082C48B0318)

We perform the same steps to stake for users 2 and 3:

[User 2 stakes 1000 tokens ](https://testnet.mintscan.io/juno-testnet/txs/5BCDB3A8DC2873C59AE84B989E0781333E00109D2EB8ECFFB229160AC81CDF26)

[User 3 stakes 6000 tokens](https://testnet.mintscan.io/juno-testnet/txs/756EA49C08FD6E1AEC1012649009A34954C9E8F67E828FF0ABF94D7108F6EC0E)

#### Checking Pending Rewards

Let us check the staker data:

```
junod query wasm contract-state smart juno1wf526ma0mjskhlgrun9qkytelnfsl5pny3l2gwvpgjqwhcrx2q8qpqg53f '{"staker":{"address":"juno197y44w58djquqkpu8v56pgwvm058f6jswm0jec"}}' 
```

#### Result:

data:

address: juno197y44w58djquqkpu8v56pgwvm058f6jswm0jec

&#x20;balance: "1000"&#x20;

pending\_rewards: \[]&#x20;

share: "1000"

{% hint style="warning" %}
Here we check one of the stakers. As we can see, the pending rewards are empty and the balance is the same as the share.&#x20;
{% endhint %}

#### Transfer Some Tokens as Rewards

Now let us send 1000 tokens to the contract. By doing this, we are telling the contract to use them as rewards for stakers.

```json
{
 "transfer":{
      "recipient":"juno1wf526ma0mjskhlgrun9qkytelnfsl5pny3l2gwvpgjqwhcrx2q8qpqg53f",
      "amount": "1000"
      }
 }
```

```
junod tx wasm execute juno1cngmjgaf9jqh8ukc6j75ygke6vdajpkx3dkphhus45agghd2tuqs06kut7 '{"transfer":{"recipient":"juno1wf526ma0mjskhlgrun9qkytelnfsl5pny3l2gwvpgjqwhcrx2q8qpqg53f","amount": "1000"}}' --from juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -b block -y
```

[Transfer Tokens](https://testnet.mintscan.io/juno-testnet/txs/3265827CD049752C9BE85E10DF99D07386472A6EE35314E55747AB27A697FC57)

Now since the token we sent is the same one being staked, then the rewards are distributed automatically to the stakers, and there is no need to call `Claim` or `UpdateGlobalIndex`.\


Let us check the new balances of the users:

#### User 1:

```
junod query wasm contract-state smart juno1wf526ma0mjskhlgrun9qkytelnfsl5pny3l2gwvpgjqwhcrx2q8qpqg53f '{"staker":{"address":"juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll"}}'
```

#### Result:

data:

address: juno1hfngwh4f53smthvqde32y07rjcezdsnlztxtll&#x20;

balance: "3300"

&#x20;pending\_rewards: \[]&#x20;

share: "3000"

#### User 2:

```
junod query wasm contract-state smart juno1wf526ma0mjskhlgrun9qkytelnfsl5pny3l2gwvpgjqwhcrx2q8qpqg53f '{"staker":{"address":"juno197y44w58djquqkpu8v56pgwvm058f6jswm0jec"}}' 
```

#### Result:

data: address: juno197y44w58djquqkpu8v56pgwvm058f6jswm0jec

balance: "1100"&#x20;

pending\_rewards: \[]&#x20;

share: "1000"

#### User 3:

```
junod query wasm contract-state smart juno1wf526ma0mjskhlgrun9qkytelnfsl5pny3l2gwvpgjqwhcrx2q8qpqg53f '{"staker":{"address":"juno1nekdhtj60s692g28e4cq5yvpg9aqd4yeafu07t"}}' 
```

#### Result:

data: address: juno1nekdhtj60s692g28e4cq5yvpg9aqd4yeafu07t

&#x20;balance: "6600"

&#x20;pending\_rewards: \[]

&#x20;share: "6000

As we can see each of the stakers got the amount proportional to the amount staked.&#x20;

#### Conclusion

This was a very simple App that showcases our cw20-staking contract. If you want to play around with it more, you can built it using the allocated rewards and add up to 10 different token rewards. It would also allow you to spread the rewards distribution to be in cycles instead of instant as we got here. This App although simple, is needed in most web3 projects that launch their own token. With Andromeda, you can have it all setup easily like we saw in this example or even using our no-code builder App which takes minutes.
