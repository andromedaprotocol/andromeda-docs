# ChainClient

When interacting with any Cosmos chain there may be differences in how they sign messages or how the messages themselves are constructed. This class is used to provide a generic interface for interacting with any Cosmos chain and is used by our AndromedaClient class. Most of the methods are simply wrappers however some require specific implementations.

```typescript
export default interface ChainClient {
  signingClient?:
  | SigningCosmWasmClient
  | SigningStargateClient
  | LCDClient
  | SigningArchwayClient;
    queryClient?: CosmWasmClient;
  signer: string;
  isConnected: boolean;
  gasPrice?: GasPrice;
```

| Name            | Type                                                                                                                                                                                                                                                                                                                                                                                                                                          | Description                                                                                                                                                                                                                                                                                                                                                |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `signingClient` | [SigningCosmwasmClient](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html), [SigningStargateClient](https://cosmos.github.io/cosmjs/latest/stargate/classes/SigningStargateClient.html), [LCDClient](https://terra-money.github.io/terra.js/classes/LCDClient.html), [SigningArchwayClient](https://github.com/archway-network/arch3.js/blob/main/packages/arch3-core/src/signingarchwayclient.ts). | The client used to sign any transactions broadcast to the chain. SigningCosmWamClient is used by the [CosmClient](cosmclient.md), SigningStargateClient is used by the   by the [Injective client](injectiveclient.md), LCDClient is used by the [TerraClient](terraclient.md), and SigningArchwayClient us used by the [ArchwayClient](archwayclient.md). |
| `queryClient`   | [CosmWasmClient](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/CosmWasmClient.html)                                                                                                                                                                                                                                                                                                                                        | The client used to query the chain.                                                                                                                                                                                                                                                                                                                        |
| `signer`        | string                                                                                                                                                                                                                                                                                                                                                                                                                                        | The current signer address.                                                                                                                                                                                                                                                                                                                                |
| `isConnected`   | boolean                                                                                                                                                                                                                                                                                                                                                                                                                                       | Whether the current chain is connected.                                                                                                                                                                                                                                                                                                                    |
| gasPrice        | [GasPrice](https://cosmos.github.io/cosmjs/latest/stargate/classes/GasPrice.html)                                                                                                                                                                                                                                                                                                                                                             | Optional configurations for the gas prices to use.                                                                                                                                                                                                                                                                                                         |

## Methods

### connect

Connects to the given chain. Assigns all clients used within the chain client, if a signer is provided a signing client is assigned.

```typescript
connect(
    endpoint: string,
    signer?: OfflineSigner | OfflineDirectSigner,
    options?: SigningCosmWasmClientOptions
  ): Promise<void>;
```

### disconnect

Disconnects from the current chain completely.

```typescript
disconnect(): Promise<void>;
```

### sign

Signs a given message with the connected signer.

```typescript
sign(
    messages: EncodeObject[],
    fee?: Fee,
    memo?: string
  ): ReturnType<SigningCosmWasmClient["sign"]> | TerraTx;
```

### broadcast

Broadcasts a given transaction to the connected chain.

```typescript
broadcast(
    tx: TxRaw | InjTxRaw | TerraTx
  ): ReturnType<SigningCosmWasmClient["broadcastTx"]>;
```

### signAndBroadcast

Signs a given message before broadcasting it to the connected chain.

```typescript
signAndBroadcast(
    messages: EncodeObject[],
    fee?: Fee,
    memo?: string
  ): Promise<DeliverTxResponse>;
```

### simulateMulti

Simulates all given messages and returns a gas fee estimate.

```typescript
simulateMulti(
    messages: readonly EncodeObject[],
    fee?: Fee,
    memo?: string
  ): ReturnType<SigningCosmWasmClient["simulate"]>;
```

### simulate

Simulates a given message and returns a gas fee estimate.

```typescript
simulate(
    message: EncodeObject,
    fee?: Fee,
    memo?: string
  ): ReturnType<SigningCosmWasmClient["simulate"]>;
```

### execute

Executes a message on the specified contract.

```typescript
execute(
    contractAddress: string,
    msg: Msg,
    fee?: Fee,
    memo?: string,
    funds?: readonly Coin[]
  ): Promise<ExecuteResult>;
```

### simulateExecute

Simulates an execute message and returns a gas fee estimate.

```typescript
simulateExecute(
    address: string,
    msg: Msg,
    funds: Coin[],
    fee?: Fee,
    memo?: string
  ): Promise<number | undefined>;
```

### upload

Uploads given contract code (Uint8Array) to the chain.

```typescript
upload(code: Uint8Array, fee?: Fee, memo?: string): Promise<UploadResult>;
```

### simulateUpload

Simulates an upload message and returns a gas fee estimate.

```typescript
simulateUpload(
    code: Uint8Array,
    fee?: Fee,
    memo?: string
  ): Promise<number | undefined>;
```

### instantiate

Instantiates a contract with the given code id using the provided instantiate message.

```typescript
instantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee?: Fee,
    options?: InstantiateOptions
  ): Promise<InstantiateResult>;
```

### simulateInstantiate

Simulates an instantiation message and returns a gas fee estimate.

```typescript
simulateInstantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee?: Fee,
    options?: InstantiateOptions
  ): Promise<number | undefined>;
```

### migrate

Migrates a contract to a given code id.

```typescript
migrate(
    contractAddress: string,
    codeId: number,
    msg: Msg,
    fee?: Fee,
    memo?: string
  ): Promise<MigrateResult>;
```

### simulateMigrate

Simulates a migrate message for a given contract address, code id and migrate message and returns a gas estimate.

```typescript
simulateMigrate(
    contractAddress: string,
    codeId: number,
    msg: Msg,
    fee?: Fee,
    memo?: string
  ): Promise<number | undefined>;
```

### encodeExecuteMsg

Converts an execute message to an EncodeObject for signing or simulating.

```typescript
  encodeExecuteMsg(
    address: string,
    msg: Msg,
    funds: Coin[]
  ): MsgExecuteContractEncodeObject;
```

### encodeInstantiateMsg

Converts an instantiate message to an EncodeObject for signing or simulating.

```typescript
encodeInstantiateMsg(
    codeId: number,
    msg: Msg,
    label: string
  ): MsgInstantiateContractEncodeObject;
```

### encodeUploadMessage

Converts an upload message to an EncodeObject for signing or simulating.

```typescript
encodeUploadMessage(wasmByteCode: Uint8Array): MsgStoreCodeEncodeObject;
```

### encodeMigrateMessage

Converts a migrate message to an EncodeObject for signing or simulating.

```typescript
encodeMigrateMessage(
    address: string,
    codeId: number,
    msg: Msg
  ): MsgMigrateContractEncodeObject;
```

### encodeSendMessage

Converts a sendTokens message to an EncodeObject for signing or simulating.

```typescript
encodeSendMessage(
    receivingAddress: string,
    amount: Coin[]
  ): MsgSendEncodeObject;
```

### sendTokens

Sends tokens from the signing address to the provided receiving address.

```typescript
sendTokens(
    receivingAddress: string,
    amount: readonly Coin[],
    fee?: Fee,
    memo?: string
  ): Promise<DeliverTxResponse>;
```
