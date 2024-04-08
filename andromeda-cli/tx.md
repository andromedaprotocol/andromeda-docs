# Tx

### Available Commands <a href="#available-commands" id="available-commands"></a>

| Command                           | Description                                            |
| --------------------------------- | ------------------------------------------------------ |
| [**byaddress**](tx.md#by-address) | Gets a history of transactions for a given address     |
| [**history**](tx.md#history)      | Gets a history of transactions for your current wallet |
| [**info**](tx.md#info)            | Gets transaction info from provided hash               |

## By Address

Gets a history of transactions for a given address.

#### Usage

```
tx byaddress <address>
```

#### Example

```
? main-star@elgafar-1> tx byaddress stars14pmn28jyqgphd5wv0z28ppxe5ryeraqqyhkuas

Hash                                                             Height  Type                   Link                                                                                                                   
75184641D8275A5B34183CF31181C563FB6E4D007FC7BBC805D2DA43892BBBD7 9874374 MsgInstantiateContract https://testnet-explorer.publicawesome.dev/stargaze/tx/75184641D8275A5B34183CF31181C563FB6E4D007FC7BBC805D2DA43892BBBD7
DF1645E23EBFBAF0937CC10462D68E377732FC82100120430533B45C9A3C4C71 3645693 MsgExecuteContract     https://testnet-explorer.publicawesome.dev/stargaze/tx/DF1645E23EBFBAF0937CC10462D68E377732FC82100120430533B45C9A3C4C71
B1C0271E07BAF4E9707C7753EE69A1FB07DCA050B13647B508F6552C2313F1DF 3645438 MsgExecuteContract     https://testnet-explorer.publicawesome.dev/stargaze/tx/B1C0271E07BAF4E9707C7753EE69A1FB07DCA050B13647B508F6552C2313F1DF
5738FB93EEB38424D30C49D8F2563E550D91108A8058BBBB12F00C810D024C69 3645427 MsgInstantiateContract https://testnet-explorer.publicawesome.dev/stargaze/tx/5738FB93EEB38424D30C49D8F2563E550D91108A8058BBBB12F00C810D024C69
DA9586846479E4B174EFC9DFA6CBA35E59A0504AAE19A2B7F353A0739B49259D 3642496 MsgExecuteContract     https://testnet-explorer.publicawesome.dev/stargaze/tx/DA9586846479E4B174EFC9DFA6CBA35E59A0504AAE19A2B7F353A0739B49259D
? main-star@elgafar-1>
```

## History

Gets a history of transactions for your current wallet.

#### Usage

```
tx history 
```

#### Example

```
? main-star@elgafar-1> tx history

Hash                                                             Height  Type                   Link                                                                                                                   
75184641D8275A5B34183CF31181C563FB6E4D007FC7BBC805D2DA43892BBBD7 9874374 MsgInstantiateContract https://testnet-explorer.publicawesome.dev/stargaze/tx/75184641D8275A5B34183CF31181C563FB6E4D007FC7BBC805D2DA43892BBBD7
DF1645E23EBFBAF0937CC10462D68E377732FC82100120430533B45C9A3C4C71 3645693 MsgExecuteContract     https://testnet-explorer.publicawesome.dev/stargaze/tx/DF1645E23EBFBAF0937CC10462D68E377732FC82100120430533B45C9A3C4C71
B1C0271E07BAF4E9707C7753EE69A1FB07DCA050B13647B508F6552C2313F1DF 3645438 MsgExecuteContract     https://testnet-explorer.publicawesome.dev/stargaze/tx/B1C0271E07BAF4E9707C7753EE69A1FB07DCA050B13647B508F6552C2313F1DF
5738FB93EEB38424D30C49D8F2563E550D91108A8058BBBB12F00C810D024C69 3645427 MsgInstantiateContract https://testnet-explorer.publicawesome.dev/stargaze/tx/5738FB93EEB38424D30C49D8F2563E550D91108A8058BBBB12F00C810D024C69
DA9586846479E4B174EFC9DFA6CBA35E59A0504AAE19A2B7F353A0739B49259D 3642496 MsgExecuteContract     https://testnet-explorer.publicawesome.dev/stargaze/tx/DA9586846479E4B174EFC9DFA6CBA35E59A0504AAE19A2B7F353A0739B49259D
? main-star@elgafar-1>
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
