# Tx

### Available Commands <a href="#available-commands" id="available-commands"></a>

| Command                                   | Description                                            |
| ----------------------------------------- | ------------------------------------------------------ |
| ****[**byaddress**](tx.md#by-address)**** | Gets a history of transactions for a given address     |
| ****[**history**](tx.md#history)****      | Gets a history of transactions for your current wallet |
| ****[**info**](tx.md#info)****            | Gets transaction info from provided hash               |

## By Address

Gets a history of transactions for a given address.

#### Usage

```
tx byaddress <address>
```

#### Example

```
? $main@uni-5> tx byaddress juno10ejg7p508qxdfsnmt6868lamt35ws3cp7hupzj7xs0wx0fvgnmrs3szsj3

Hash                                                             Height Link                                                                                                         
D528CC32305F91C98A8B21140EA9FA3FD22495D158F42ED18DC71CC29C574D6A 106539 https://testnet.mintscan.io/juno-testnet/txs/D528CC32305F91C98A8B21140EA9FA3FD22495D158F42ED18DC71CC29C574D6A
62C3DA698DC55A084656C6EF8191597BF894727370E08B072F99F229F2FDA772 106531 https://testnet.mintscan.io/juno-testnet/txs/62C3DA698DC55A084656C6EF8191597BF894727370E08B072F99F229F2FDA772
A9A826ABB849F2EC22D25017D059D39CDCD1523C868345087877C4C445890D3B 106519 https://testnet.mintscan.io/juno-testnet/txs/A9A826ABB849F2EC22D25017D059D39CDCD1523C868345087877C4C445890D3B
02234589D7BA2AE69966564B70F52F6867E0CFD1A2691B0332852D107F3F6CE0 105916 https://testnet.mintscan.io/juno-testnet/txs/02234589D7BA2AE69966564B70F52F6867E0CFD1A2691B0332852D107F3F6CE0
DD957DEA751DAC9E04190F9FA99697A09D4B898FC616D845BC6ABC97268246A3 105908 https://testnet.mintscan.io/juno-testnet/txs/DD957DEA751DAC9E04190F9FA99697A09D4B898FC616D845BC6ABC97268246A3
? $main@uni-5> () 
```

## History

Gets a history of transactions for your current wallet.

#### Usage

```
tx history 
```

#### Example

```
? $main@uni-5> tx history

Hash                                                             Height Link                                                                                                         
0BC340FA026B7EE657A58D4F1312B59BF15C2167D368C223493D34F78127A033 106459 https://testnet.mintscan.io/juno-testnet/txs/0BC340FA026B7EE657A58D4F1312B59BF15C2167D368C223493D34F78127A033
8ADB723D829E995206F409638AD93E5352D1B6D0E49D192C5D7E6E5147430E3B 78474  https://testnet.mintscan.io/juno-testnet/txs/8ADB723D829E995206F409638AD93E5352D1B6D0E49D192C5D7E6E5147430E3B
BBC56ED6B9D1093A59F146C9A555795256C468FF3966864ECD10BD939AF75292 66204  https://testnet.mintscan.io/juno-testnet/txs/BBC56ED6B9D1093A59F146C9A555795256C468FF3966864ECD10BD939AF75292
0F820454356D9FE8E80D736CB47F93C9BA40A901FD937B6395B93B9A11D72436 47917  https://testnet.mintscan.io/juno-testnet/txs/0F820454356D9FE8E80D736CB47F93C9BA40A901FD937B6395B93B9A11D72436
? $main@uni-5> () 
```

## Info

Gets transaction info from provided hash.

#### Usage

```
tx info <hash>
```

#### Example

```
tx info DD957DEA751DAC9E04190F9FA99697A09D4B898FC616D845BC6ABC97268246A3
```

You will get all the information related to the transaction.
