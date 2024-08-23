# InjectiveClient

Helper function to convert Uint8Array to JSON.

```typescript
function arrayToJson(array: Uint8Array) {
  const jsonString = Buffer.from(array).toString("utf8");

  const parsedData = JSON.parse(jsonString);
  return parsedData;
}
```

## Class

A client to interact with the Injective chain.

```typescript
export default class InjectiveClient
  extends BaseChainClient
  implements ChainClient {
  declare public signingClient?: SigningStargateClient;
  declare public queryClient?: CosmWasmClient;
  public gasPrice?: GasPrice;

  private directSigner?: OfflineDirectSigner;

  protected preMessage(signed = true) {
    super.preMessage(signed);
    if (signed && !this.directSigner) {
      throw new Error("No signer assigned");
    }
  }
```

## Methods

### connect

Connects to the given chain. Assigns all clients used within the chain client, if a signer is provided a signing client is assigned.

{% code overflow="wrap" %}
```typescript
 async connect(
    endpoint: string,
    signer?: OfflineDirectSigner,
    options?: SigningCosmWasmClientOptions
  ): Promise<void> {
    delete this.signingClient;
    delete this.queryClient;


    this.queryClient = await CosmWasmClient.connect(endpoint);
    this.gasPrice = options?.gasPrice || GasPrice.fromString(DEFAULT_GAS_PRICE + DEFAULT_FEE_DENOM);
    if (signer) {
      const tmClient = await Tendermint37Client.connect(endpoint);
      this.signingClient = await InjectiveStargate.InjectiveSigningStargateClient.createWithSigner(tmClient, signer, {
        registry: options?.registry || new Registry([...defaultRegistryTypes, ...wasmTypes]),
        aminoTypes: options?.aminoTypes || new AminoTypes({ ...createDefaultAminoConverters() }),
        gasPrice: this.gasPrice
      });
      this.directSigner = signer;

      const [account] = await signer.getAccounts();
      this.signer = account.address;
    }
  }
```
{% endcode %}

<table><thead><tr><th width="218.22184091187643">Name</th><th width="301.16406249999994">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>endpoint</code></td><td>string</td><td>The endpoint of the chain to connect to. </td></tr><tr><td><code>signer</code></td><td><a href="https://cosmos.github.io/cosmjs/latest/proto-signing/interfaces/OfflineDirectSigner.html">CosmosOfflineDirectSigner</a> or OfflineDirectSigner</td><td>Optional signer used to sign the messages.</td></tr><tr><td>options</td><td><a href="https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/SigningCosmWasmClientOptions.html#gasPrice">SigningCosmWasmClientOptions</a></td><td>Optional field to set the client options. </td></tr></tbody></table>

### disconnect

Disconnects the from the connected client.

```typescript
 async disconnect(): Promise<void> {
    delete this.signingClient;
    this.queryClient?.disconnect();
    delete this.queryClient;
    this.signer = "";
    delete this.directSigner;
    delete this.gasPrice;
  }
```

### sign

Signs a given message with the connected signer.

{% code overflow="wrap" %}
```typescript
  async sign(messages: EncodeObject[], fee?: StdFee, memo?: string): Promise<TxRaw> {
    this.preMessage(true);
    return await this.signingClient!.sign(this.signer, messages, fee || DEFAULT_STD_FEE, memo || "");
  }
```
{% endcode %}

| Name       | Type                                                                             | Description                                                        |
| ---------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `messages` | [EncodeObject](injectiveclient.md#encodeobject)\[]                               | The messages to sign.                                              |
| `fee`      | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee to pay for the transaction. Defaults to auto if not specified. |
| `memo`     | string                                                                           | Optional memo to attach. Defaults to an empty string.              |

### broadcast

Broadcasts a given transaction to the connected client.

```typescript
  async broadcast(
    tx: TxRaw,
  ): Promise<DeliverTxResponse> {
    this.preMessage(true);
    const txBytes = TxRaw.encode(tx).finish();
    return await this.signingClient!.broadcastTx(txBytes)
  }
```

| Name | Type                         | Description                    |
| ---- | ---------------------------- | ------------------------------ |
| `tx` | [TxRaw](cosmclient.md#txraw) | The transaction to broadcast.  |

### signAndBroadcast

Signs a given message before broadcasting it to the connected chain.&#x20;

```typescript
async signAndBroadcast(
    messages: EncodeObject[],
    fee?: StdFee,
    memo?: string
  ): Promise<DeliverTxResponse & { logs: readonly Log[] }> {
    const signed = await this.signInj(messages, fee, memo);
    const resp = await this.broadcast(signed);
    return {
      ...resp,
      logs: parseRawLog(resp.rawLog),
    };
  }
```

| Name        | Type                                                                             | Description                                                                                 |
| ----------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `messages`  | [EncodeObject](injectiveclient.md#encodeobject)\[]                               | The messages to sign and broadcast.                                                         |
| `fee`       | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | The fee to pay for signing and broadcasting the message. Defaults to auto if not specified. |
| `memo`      | string                                                                           | An optional memo to attach to the transaction.                                              |

#### EncodeObject

```typescript
export interface EncodeObject {
    readonly typeUrl: string;
    readonly value: any;
}
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

| Name       | Type                                                                             | Description                                                    |
| ---------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| `messages` | [EncodeObject](injectiveclient.md#encodeobject)\[]                               | The messages to simulate.                                      |
| `fee`      | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee amount to pay. Defaults to auto if not specified.          |
| `memo`     | string                                                                           | Optional memo to attach to the transaction. Defaults to empty. |

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

| Name      | Type                                                                             | Description                                                        |
| --------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `message` | [EncodeObject](injectiveclient.md#encodeobject)                                  | The message to simulate.                                           |
| `fee`     | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee payed to run the simulate. Defaults to auto if not specified.  |
| `memo`    | string or undefined                                                              | Optional memo to attach to the transaction.                        |

### execute

Executes a message on the specified contract.

```typescript
async execute(
    contractAddress: string,
    msg: Msg,
    fee?: StdFee | undefined,
    memo?: string | undefined,
    funds?: readonly Coin[] | undefined
  ): Promise<DeliverTxResponse & { logs: any }> {
    this.preMessage(true);
    const encoded = this.encodeExecuteMsg(contractAddress, msg, [...funds || []]);
    const res = await this.signAndBroadcast([encoded], fee, memo);
    return {
      ...res,
      logs: []
    }
  }
```

| Name              | Type                                                                             | Description                                                             |
| ----------------- | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `contractAddress` | string                                                                           | The contract address to execute on.                                     |
| `msg`             | [Msg](injectiveclient.md#msg)                                                    | The execute message.                                                    |
| `fee`             | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee amount to pay for the execution. Defaults to auto if not specified. |
| `memo`            | string                                                                           | Optional memo to attach to the transaction.                             |
| `funds`           | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[]     | Optional funds to attach along with the message.                        |

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

| Name      | Type                                                                         | Description                                           |
| --------- | ---------------------------------------------------------------------------- | ----------------------------------------------------- |
| `address` | string                                                                       | The contract address you are executing on.            |
| `msg`     | [Msg](injectiveclient.md#msg)                                                | The execute message to simulate.                      |
| `funds`   | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[] | The funds attached along with the message.            |
| `fee`     | [Fee](cosmclient.md#fee)                                                     | Fee amount to pay. Defaults to auto if not specified. |
| `memo`    | string or undefined                                                          | Optional memo to add to the transaction.              |

### upload

Uploads given contract code (Uint8Array) to the chain.

{% code overflow="wrap" %}
```typescript
  async upload(code: Uint8Array, fee: StdFee | "auto" = "auto", memo?: string | undefined): Promise<UploadResult> {
    this.preMessage();
    const compressed = gzip(code, { level: 9 });
    const encoded = this.encodeUploadMessage(compressed);
    const res = await this.signAndBroadcast([encoded], fee, memo);
    const compressedChecksum = toHex(sha256(compressed));
    const codeIdAttr = findAttribute([{ msg_index: 0, 'log': '', events: res.events }], "cosmwasm.wasm.v1.EventCodeStored", "code_id");
    let codeId = -1;
    try {
      codeId = parseInt(JSON.parse(codeIdAttr.value));
    } catch (err) { }
    return {
      ...res,
      originalSize: code.length,
      compressedSize: compressed.length,
      checksum: compressedChecksum,
      codeId: codeId,
      logs: []
    }
  }
```
{% endcode %}

| Name   | Type                                                                             | Description                                                                            |
| ------ | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| `code` | Uint8Array                                                                       | The wasm binary code to upload to the connected client.                                |
| `fee`  | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee to pay for uploading. Defaults to auto if not specified.                           |
| `memo` | string                                                                           | Optional memo to add to the transaction. Defaults to an empty string if not specified. |

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

| Name   | Type                     | Description                                                   |
| ------ | ------------------------ | ------------------------------------------------------------- |
| `code` | Uint8Array               | The wasm binary code to simulate uploading.                   |
| `fee`  | [Fee](cosmclient.md#fee) | Fee to pay for uploading. Defaults to auto if not specified.  |
| `memo` | string or undefined      | Optional memo to add to the transaction.                      |

### instantiate

Instantiates a contract with the given code id using the provided instantiate message.

```typescript
async instantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee: StdFee | "auto" = "auto",
    options?: InstantiateOptions
  ): Promise<InstantiateResult> {
    this.preMessage(true);
    const encoded = this.encodeInstantiateMsg(codeId, msg, label, options);
    const res = await this.signAndBroadcast([encoded], fee, "Instantiate");
    const contractAddressAttr = findAttribute(
      res.logs,
      "wasm",
      "_contract_address"
    );
    return {
      ...res,
      contractAddress: contractAddressAttr.value,
    }
  }
```

| Name      | Type                                                                                                              | Description                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `codeId`  | number                                                                                                            | The code Id of the contract to instantiate.                                                             |
| `msg`     | [Msg](injectiveclient.md#msg)                                                                                     | The instantiation message.                                                                              |
| `label`   | string                                                                                                            | A label for the instantiation. Can be any string such as " instantiating a CW721" or "my\_label" ect... |
| `fee`     | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html)                                  | Fee to pay for instantiation. Defaults to auto if not specified.                                        |
| `options` | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | The options of an .instantiate() call. All properties are optional.                                     |

### simulateInstantiate

Simulates an instantiation message and returns a gas fee estimate.

```typescript
async simulateInstantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee?: StdFee,
    options?: InstantiateOptions
  ): Promise<number | undefined> {
    const message = this.encodeInstantiateMsg(codeId, msg, label);
    return this.simulate(message, fee, options?.memo);
  }
```

| Name      | Type                                                                                                              | Description                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `codeId`  | number                                                                                                            | The code Id of the contract to instantiate.                                                             |
| `msg`     | [Msg](injectiveclient.md#msg)                                                                                     | The instantiation message.                                                                              |
| `label`   | string                                                                                                            | A label for the instantiation. Can be any string such as " instantiating a CW721" or "my\_label" ect... |
| `fee`     | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html)                                  | Fee to pay for instantiation. Defaults to auto if not specified.                                        |
| `options` | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | The options of an .instantiate() call. All properties are optional.                                     |

### migrate

Migrates a contract to a given code id.

```typescript
async migrate(
    contractAddress: string,
    codeId: number,
    msg: Msg,
    fee: StdFee | "auto" = "auto",
    memo?: string | undefined
  ): Promise<MigrateResult> {
    this.preMessage(true);
    const encoded = this.encodeMigrateMessage(contractAddress, codeId, msg);
    return this.signAndBroadcast([encoded], fee, memo);
  }
```

| Name              | Type                                                                             | Description                                                      |
| ----------------- | -------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| `contractAddress` | string                                                                           | The address of the contract that you want to migrate.            |
| `codeId`          | number                                                                           | The code Id to migrate to.                                       |
| `msg`             | [Msg](injectiveclient.md#msg)                                                    | The migrate message.                                             |
| `fee`             | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee to pay for the migration. Defaults to auto if not specified. |
| `memo`            | string                                                                           | Optional memo to attach to the transaction.                      |

### simulateMigrate

Simulates a migrate message for a given contract address, code id and migrate message and returns a gas estimate.

```typescript
async simulateMigrate(
    contractAddress: string,
    codeId: number,
    msg: Msg,
    fee?: Fee | undefined,
    memo?: string | undefined
  ): Promise<number | undefined> {
    const message = this.encodeMigrateMessage(contractAddress, codeId, msg);
    return this.simulate(message, fee, memo);
  }
```

| Name              |  Type                    | Description                                                       |
| ----------------- | ------------------------ | ----------------------------------------------------------------- |
| `contractAddress` | string                   | The contract address that you want to migrate.                    |
| `codeId`          | number                   | The code Id to migrate to.                                        |
| `msg`             | Msg                      | The migrate message.                                              |
| `fee`             | [Fee](cosmclient.md#fee) | Fee to pay to send the funds. Defaults to auto if not specified.  |
| `memo`            | string or undefined      | Optional memo to attach to the transaction.                       |

### sendTokens

Sends tokens from the signing address to the provided receiving address.

{% hint style="warning" %}
You can only send one amount at a time.&#x20;
{% endhint %}

```typescript
async sendTokens(
    receivingAddress: string,
    amount: readonly Coin[],
    fee: Fee = "auto",
    memo?: string | undefined
  ): Promise<any> {
    return this.signingClient?.sendTokens(
      this.signer,
      receivingAddress,
      amount,
      fee,
      memo
    );
  }
}
```

| Name               | Type                                                                          | Description                                                       |
| ------------------ | ----------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `receivingAddress` | string                                                                        | The address to receive the tokens.                                |
| `amount`           | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html) \[] | The funds to send.                                                |
| `fee`              | [Fee](cosmclient.md#fee)                                                      | Fee to pay to send the funds. Defaults to auto if not specified.  |
| `memo`             | string                                                                        | Optional memo to attach to the transaction.                       |

