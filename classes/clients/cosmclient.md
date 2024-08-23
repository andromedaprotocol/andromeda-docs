# CosmClient

## Class

A client to interact with the cosmos hub chain.

```typescript
export default class CosmClient extends BaseChainClient implements ChainClient {
  public signingClient?: SigningCosmWasmClient;
  public queryClient?: CosmWasmClient;
  public gasPrice?: GasPrice;
```

| Name          | Type                                                                                                                 | Descriptions                                                      |
| ------------- | -------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| signingClient | [SigningCosmWasmClient](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html) | The client used to sign any transactions broadcast to the chain.  |
| queryClient   | [CosmWasmClient](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/CosmWasmClient.html)               | The client used to query the chain.                               |
| gasPrice      | [GasPrice](https://cosmos.github.io/cosmjs/latest/stargate/classes/GasPrice.html)                                    | Optional configurations for the gas prices to use.                |

## Methods

### connect

Connects to the given chain. Assigns all clients used within the chain client, if a signer is provided a signing client is assigned.

```typescript
async connect(
    endpoint: string,
    signer?: OfflineSigner,
    options?: SigningCosmWasmClientOptions
  ): Promise<void> {
    delete this.signingClient;
    delete this.queryClient;
    this.gasPrice = options?.gasPrice;

    this.queryClient = await CosmWasmClient.connect(endpoint);
    if (signer) {
      this.signingClient = await SigningCosmWasmClient.connectWithSigner(
        endpoint,
        signer,
        { broadcastTimeoutMs: 30000, ...options }
      );

      const [account] = await signer.getAccounts();
      this.signer = account.address;
    }
  }
```

<table><thead><tr><th width="218.22184091187643">Name</th><th width="301.16406249999994">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>endpoint</code></td><td>string</td><td>The endpoint of the chain to connect to. </td></tr><tr><td><code>signer</code></td><td><a href="https://cosmos.github.io/cosmjs/latest/proto-signing/modules.html#OfflineSigner">OfflineSigner</a></td><td>Optional signer used to sign the messages.</td></tr><tr><td><code>options</code></td><td><a href="https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/SigningCosmWasmClientOptions.html">SigningCosmWasmClientOptions</a></td><td>Optional field to set the client options. </td></tr></tbody></table>

### disconnect

Disconnects the assigned clients.

```typescript
  async disconnect(): Promise<void> {
    if (this.signingClient) this.signingClient.disconnect();
    if (this.queryClient) this.queryClient.disconnect();

    delete this.signingClient;
    delete this.queryClient;
    this.signer = "";
    delete this.gasPrice;
  }

```

### sign

Signs a given message with the connected signer.

```typescript
async sign(messages: EncodeObject[], fee: StdFee, memo = ""): Promise<TxRaw> {
    this.preMessage(true);
    return await this.signingClient!.sign(this.signer, messages, fee, memo);
  }
```

| Name       | Type                                                                             | Description                                            |
| ---------- | -------------------------------------------------------------------------------- | ------------------------------------------------------ |
| `messages` | EncodeObject\[]                                                                  | The messages to sign.                                  |
| `fee`      | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | The fee to pay for signing.                            |
| `memo`     | string                                                                           | Optional memo to attach. Defaults to an empty string.  |

### broadcast

Broadcasts a given transaction to the connected client.

```typescript
async broadcast(tx: TxRaw): ReturnType<ChainClient["broadcast"]> {
    this.preMessage(true);
    const txBytes = TxRaw.encode(tx).finish();
    return await this.signingClient!.broadcastTx(txBytes);
  }
```

| Name | Type                         | Description                   |
| ---- | ---------------------------- | ----------------------------- |
| `tx` | [TxRaw](cosmclient.md#txraw) | the transaction to broadcast. |

#### TxRaw

TxRaw is a variant of Tx that pins the signer's exact binary representationof body and auth\_info. This is used for signing, broadcasting and verification. The binary `serialize(tx: TxRaw)` is stored in Tendermint and the hash `sha256(serialize(tx: TxRaw))` becomes the "txhash", commonly used as the transaction ID.

```typescript
export interface TxRaw {
    /**
     * body_bytes is a protobuf serialization of a TxBody that matches the
     * representation in SignDoc.
     */
    bodyBytes: Uint8Array;
    /**
     * auth_info_bytes is a protobuf serialization of an AuthInfo that matches the
     * representation in SignDoc.
     */
    authInfoBytes: Uint8Array;
    /**
     * signatures is a list of signatures that matches the length and order of
     * AuthInfo's signer_infos to allow connecting signature meta information like
     * public key and signing mode by position.
     */
    signatures: Uint8Array[];
}
```

### signAndBroadcast

Signs a given message before broadcasting it to the connected chain.&#x20;

```typescript
async signAndBroadcast(
    messages: EncodeObject[],
    fee: Fee = "auto",
    memo?: string | undefined
  ): Promise<DeliverTxResponse> {
    this.preMessage(true);

    return await this.signingClient!.signAndBroadcast(
      this.signer,
      messages,
      fee,
      memo
    );
  }
```

| Name        | Type                                          | Description                                                                                 |
| ----------- | --------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `messages`  | [EncodeObject](cosmclient.md#encodeobject)\[] | The messages to sign and broadcast.                                                         |
| `fee`       | [Fee](cosmclient.md#fee)                      | The fee to pay for signing and broadcasting the message. Defaults to auto if not specified. |
| `memo`      | string or undefined                           | An optional memo to attach to the transaction.                                              |

#### EncodeObject

```typescript
export interface EncodeObject {
    readonly typeUrl: string;
    readonly value: any;
}
```

#### Fee

```typescript
export type Fee = number | StdFee | "auto";
```

### simulateMulti

Simulates all given messages and returns a gas fee estimate.

```typescript
async simulateMulti(
    messages: EncodeObject[],
    _fee: Fee = "auto",
    memo?: string | undefined
  ): Promise<number> {
    this.preMessage();
    return this.signingClient!.simulate(this.signer, messages, memo);
    }
```

| Name       | Type                                          | Description                                         |
| ---------- | --------------------------------------------- | --------------------------------------------------- |
| `messages` | [EncodeObject](cosmclient.md#encodeobject)\[] | The messages to simulate.                           |
| `_fee`     | [Fee](cosmclient.md#fee)                      | The fee to pay. Defaults to auto if not specified.  |
| `memo`     | string or undefined                           | Optional memo to attach to the transaction.         |

### simulate

Simulates the given message and returns a gas fee estimate.

```typescript
async simulate(
    message: EncodeObject,
    _fee?: Fee | undefined,
    memo?: string | undefined
  ): Promise<number> {
    this.preMessage();
    return this.signingClient!.simulate(this.signer, [message], memo);
  }
```

| Name      | Type                                       | Description                                  |
| --------- | ------------------------------------------ | -------------------------------------------- |
| `message` | [EncodeObject](cosmclient.md#encodeobject) | The message to simulate.                     |
| `_fee`    | [Fee](cosmclient.md#fee)                   | Optional fee attached to the message.        |
| `memo`    | string or undefined                        | Optional memo to attach to the transaction.  |

### execute

Executes a message on the specified contract.

```typescript
async execute(
    contractAddress: string,
    msg: Msg,
    fee?: Fee | undefined,
    memo?: string | undefined,
    funds?: readonly Coin[] | undefined
  ): Promise<ExecuteResult> {
    this.preMessage(true);

    return await this.signingClient!.execute(
      this.signer,
      contractAddress,
      msg,
      fee ?? "auto",
      memo,
      funds
    );
  }
```

| Name              | Type                                                                                     | Description                                      |
| ----------------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------ |
| `contractAddress` | string                                                                                   | The contract address to execute on.              |
| `msg`             | [Msg](cosmclient.md#msg)                                                                 | The execute message.                             |
| `memo`            | string or undefined                                                                      | Optional memo to attach to the transaction.      |
| `funds`           | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[] or udefined | Optional funds to attach along with the message. |

#### Msg

```typescript
export type Msg = Record<string, unknown>;
```

### simulateExecute

Simulates an execute message and returns a gas fee estimate.

```typescript
async simulateExecute(
    address: string,
    msg: Msg,
    funds: Coin[],
    _fee?: Fee,
    memo?: string | undefined
  ) {
    const message = this.encodeExecuteMsg(address, msg, funds);
    return this.simulate(message, undefined, memo);
  }
```

| Name      | Type                                                                         | Description                                 |
| --------- | ---------------------------------------------------------------------------- | ------------------------------------------- |
| `address` | string                                                                       | The contract address you are executing on.  |
| `msg`     | [Msg](cosmclient.md#msg)                                                     | The execute message to simulate.            |
| `funds`   | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[] | The funds attached along with the message.  |
| `_fee`    |  [Fee](cosmclient.md#fee)                                                    | Optional fee for executing the message.     |
| `memo`    | string or undefined                                                          | Optional memo to add to the transaction.    |

### upload

Uploads given contract code (Uint8Array) to the chain.

```typescript
async upload(code: Uint8Array, fee: Fee = "auto", memo?: string | undefined) {
    this.preMessage();
    return this.signingClient!.upload(this.signer, code, fee, memo);
  }
```

| Name   | Type                | Description                                              |
| ------ | ------------------- | -------------------------------------------------------- |
| `code` | Uint8Array          | The wasm binary code to upload to the connected client.  |
| `fee`  | Fee                 | Fee to pay for uploading.                                |
| `memo` | string or undefined | Optional memo to add to the transaction.                 |

### simulateUpload

Simulate an upload message and returns a gas fee estimate.

```typescript
async simulateUpload(
    code: Uint8Array,
    _fee?: Fee | undefined,
    memo?: string | undefined
  ): Promise<number | undefined> {
    const message = this.encodeUploadMessage(code);
    return this.simulate(message, undefined, memo);
  }

```

| Name   | Type                     | Description                                 |
| ------ | ------------------------ | ------------------------------------------- |
| `code` | Uint8Array               | The wasm binary code to simulate uploading. |
| `_fee` | [Fee](cosmclient.md#fee) | Fee to pay for uploading.                   |
| `memo` | string or undefined      | Optional memo to add to the transaction.    |

### instantiate

Instantiates a contract with the given code id using the provided instantiate message.

```typescript
async instantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee: Fee = "auto",
    options?: InstantiateOptions
  ): Promise<InstantiateResult> {
    this.preMessage(true);
    return this.signingClient!.instantiate(
      this.signer,
      codeId,
      msg,
      label,
      fee,
      options
    );
  }
```

| Name      | Type                                                                                                              | Description                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `codeId`  | number                                                                                                            | The code Id of the contract to instantiate.                                                             |
| `msg`     | [Msg](cosmclient.md#msg)                                                                                          | The instantiation message.                                                                              |
| `label`   | string                                                                                                            | A label for the instantiation. Can be any string such as " instantiating a CW721" or "my\_label" ect... |
| `fee`     | [Fee](cosmclient.md#fee)                                                                                          | Fee to pay for instantiation. Defaults to auto.                                                         |
| `options` | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | <p></p><p>The options of an .instantiate() call. All properties are optional.</p>                       |

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

| Name      | Type                                                                                                              | Description                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `codeId`  | number                                                                                                            | The code Id of the contract to simulate instantiating.                                                  |
| `msg`     | [Msg](cosmclient.md#msg)                                                                                          | The instantiation message.                                                                              |
| `label`   | string                                                                                                            | A label for the instantiation. Can be any string such as " instantiating a CW721" or "my\_label" ect... |
| `fee`     | [Fee](cosmclient.md#fee)                                                                                          | Fee to pay for instantiation. Defaults to auto.                                                         |
| `options` | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | The options of an .instantiate() call. All properties are optional.                                     |

### migrate

Migrates a contract to a given code id.

```typescript
async migrate(
    contractAddress: string,
    codeId: number,
    msg: Msg,
    fee: Fee = "auto",
    memo?: string | undefined
  ): Promise<MigrateResult> {
    this.preMessage(true);
    return this.signingClient!.migrate(
      this.signer,
      contractAddress,
      codeId,
      msg,
      fee,
      memo
    );
  }

```

| Name              | Type                     | Description                                            |
| ----------------- | ------------------------ | ------------------------------------------------------ |
| `contractAddress` | string                   | The address of the contract that you want to migrate.  |
| `codeId`          | number                   | The code Id to migrate to.                             |
| `msg`             | [Msg](cosmclient.md#msg) | The migrate message.                                   |
| `fee`             | [Fee](cosmclient.md#fee) | The fee to pay for the migration.                      |
| `memo`            | string or undefined      | Optional memo to attach to the transaction.            |

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

| Name              |  Type                    | Description                                     |
| ----------------- | ------------------------ | ----------------------------------------------- |
| `contractAddress` | string                   | The contract address that you want to migrate.  |
| `codeId`          | number                   | The code Id to migrate to.                      |
| `msg`             | Msg                      | The migrate message.                            |
| `fee`             | [Fee](cosmclient.md#fee) | Optional fee to pay to send the funds.          |
| `memo`            | string                   | Optional memo to attach to the transaction.     |

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

| Name               | Type                                                                          | Description                                 |
| ------------------ | ----------------------------------------------------------------------------- | ------------------------------------------- |
| `receivingAddress` | string                                                                        | The address to receive the tokens.          |
| `amount`           | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html) \[] | The funds to send.                          |
| `fee`              | [Fee](cosmclient.md#fee)                                                      | Optional fee to pay to send the funds.      |
| `memo`             | string                                                                        | Optional memo to attach to the transaction. |

