# BaseChainClient

### JsonToArray

Helper function to convert JSON to Uint8Array.

```typescript
const JsonToArray = function (json: Record<string, any>) {
  var str = JSON.stringify(json, null, 0);
  var ret = new Uint8Array(str.length);
  for (var i = 0; i < str.length; i++) {
    ret[i] = str.charCodeAt(i);
  }
  return ret;
```

## Class

A base class that extends the other clients.

```typescript
export default class BaseChainClient implements Partial<ChainClient> {
  public signingClient?: ChainClient["signingClient"];
  public queryClient?: ChainClient["queryClient"];
  public signer = "";
```

| Name            | Type                                                    | Description                                                      |
| --------------- | ------------------------------------------------------- | ---------------------------------------------------------------- |
| `signingClient` | Same as in the [ChainClient](chainclient.md) interface. | The client used to sign any transactions braodcast to the chain. |
| `queryClient`   | Same as in the [ChainClient](chainclient.md) interface. | The client used to query the chain.                              |
| `signer`        | string                                                  | The current signer address.                                      |

## Methods

### encodeExecuteMsg

Converts an execute message to an EncodeObject for signing or simulating.

```typescript
encodeExecuteMsg(
    address: string,
    msg: Msg,
    funds: Coin[]
  ): MsgExecuteContractEncodeObject {
    return {
      typeUrl: "/cosmwasm.wasm.v1.MsgExecuteContract",
      value: {
        sender: this.signer,
        contract: address,
        msg: JsonToArray(msg),
        funds,
      },
    };
  }

```

| Name      | Type                                                                         | Description                                |
| --------- | ---------------------------------------------------------------------------- | ------------------------------------------ |
| `address` | string                                                                       | The contract address you are executing on. |
| `msg`     | [Msg](basechainclient.md#msg)                                                | The msg to encode.                         |
| `funds`   | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[] | The funds to attach to the message.        |

#### Msg

```typescript
export type Msg = Record<string, unknown>;
```

### encodeInstantiateMsg

Converts an instantiate message to an EncodeObject for signing or simulating.

```typescript
 encodeInstantiateMsg(
    codeId: number,
    msg: Msg,
    label: string,
    options?: InstantiateOptions
  ): MsgInstantiateContractEncodeObject {
    return {
      typeUrl: "/cosmwasm.wasm.v1.MsgInstantiateContract",
      value: {
        sender: this.signer,
        codeId: BigInt(codeId),
        msg: JsonToArray(msg),
        label,
        'admin': options?.admin,
        'funds': options?.funds as any
      },
    };
  }
```

| Name     | Type                                                                                                              | Description                                                          |
| -------- | ----------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| `codeId` | number                                                                                                            | The code Id of the contract you are looking to instantiate.          |
| `msg`    | [Msg](basechainclient.md#msg)                                                                                     | The instantiation message.                                           |
| `label`  | string                                                                                                            | String of text to provide additional information on the transaction. |
| options  | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | Optional set of configurations inlcuding a memo, admin, and funds.   |

### encodeUploadMessage

Converts an upload message to an EncodeObject for signing or simulating.

```typescript
encodeUploadMessage(wasmByteCode: Uint8Array): MsgStoreCodeEncodeObject {
    return {
      typeUrl: "/cosmwasm.wasm.v1.MsgStoreCode",
      value: {
        sender: this.signer,
        wasmByteCode,
      },
    };
  }
```

| Name           | Type       | Description                              |
| -------------- | ---------- | ---------------------------------------- |
| `wasmByteCode` | Uint8Array | The wasm binary code to upload to chain. |

### encodeMigrateMessage

Converts a migrate message to an EncodeObject for signing or simulating.

```typescript
encodeMigrateMessage(
    address: string,
    codeId: number,
    msg: Msg
  ): MsgMigrateContractEncodeObject {
    return {
      typeUrl: "/cosmwasm.wasm.v1.MsgMigrateContract",
      value: {
        sender: this.signer,
        codeId: Long.fromNumber(codeId),
        contract: address,
        msg: JsonToArray(msg),
      },
    };
  }
```

| Name      | Type                          | Description                                |
| --------- | ----------------------------- | ------------------------------------------ |
| `address` | string                        | The contract address to migrate.           |
| `codeId`  | number                        | The code Id of the contract to migrate to. |
| `msg`     | [Msg](basechainclient.md#msg) | The migrate message.                       |

### encodeSendMessage

Converts a Send message to an EncodeObject for signing or simulating.

```typescript
encodeSendMessage(
    receivingAddress: string,
    amount: Coin[]
  ): MsgSendEncodeObject {
    return {
      typeUrl: "/cosmos.bank.v1beta1.MsgSend",
      value: {
        fromAddress: this.signer,
        toAddress: receivingAddress,
        amount,
      },
    };
  }
```

<table><thead><tr><th width="214">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>receivingAddress</code></td><td>string</td><td>The address to receive the funds sent.</td></tr><tr><td><code>amount</code></td><td><a href="https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html">Coin</a>[]</td><td>The funds to send.</td></tr></tbody></table>
