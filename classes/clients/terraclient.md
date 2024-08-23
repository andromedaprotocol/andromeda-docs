# TerraClient

### arrayToJson

Helper function to convert Uint8Array to JSON.

```typescript
function arrayToJson(array: Uint8Array) {
  const jsonString = Buffer.from(array).toString("utf8");

  const parsedData = JSON.parse(jsonString);
  return parsedData;
}
```

## Class

A client to interact with the cosmos hub chain.

```typescript
export default class TerraClient
  extends BaseChainClient
  implements ChainClient
{
  private directSigner?: OfflineDirectSigner;
  public signingClient?: LCDClient;
  ...
  }
```

| Name            | Type                                                                                                             | Description                                                                                                                                |
| --------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `directSigner`  | [OfflineDirectSigner](https://cosmos.github.io/cosmjs/latest/proto-signing/interfaces/OfflineDirectSigner.html)  | The signer to sign the messages.                                                                                                           |
| `signingClient` | [LCDClient](https://terra-money.github.io/terra.js/classes/LCDClient.html)                                       | An object repesenting a connection to a terrad node running the Lite Client Daemon (LCD) server, a REST server providing access to a node. |

## Methods

### connect

Connects to the given chain. Assigns all clients used within the chain client, if a signer is provided a signing client is assigned.

```typescript
async connect(endpoint: string, signer?: OfflineDirectSigner) {
    delete this.signingClient;
    delete this.queryClient;

    this.queryClient = await CosmWasmClient.connect(endpoint);
    this.signingClient = new LCDClient({
      URL: "https://pisco-lcd.terra.dev/",
      chainID: "pisco-1",
    }); 
    if (signer) {
      this.directSigner = signer;

      const [account] = await signer.getAccounts();
      this.signer = account.address;
    }
  }
```

<table><thead><tr><th width="218.22184091187643">Name</th><th width="301.16406249999994">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>endpoint</code></td><td>string</td><td>The endpoint of the chain to connect to. </td></tr><tr><td><code>signer</code></td><td><a href="https://cosmos.github.io/cosmjs/latest/proto-signing/interfaces/OfflineDirectSigner.html">OfflineDirectSigner</a></td><td>Optional signer used to sign the messages.</td></tr></tbody></table>

### disconnect

Disconnects the assigned clients.&#x20;

```typescript
async disconnect() {
    delete this.directSigner;
    delete this.signingClient;
    delete this.queryClient;
    this.signer = "";
  }
```

### sign

Signs a given message with the connected signer

```typescript
async sign(
    messages: EncodeObject[],
    fee?: Fee,
    memo?: string
  ): Promise<TxRaw> {
    const tx = await this.createTx(messages, fee, memo);
    return {
      bodyBytes: tx.body.toBytes(),
      authInfoBytes: tx.auth_info.toBytes(),
      signatures: tx.signatures.map((str) => Uint8Array.from(Buffer.from(str))), //TODO: THIS MAY NOT WORK?
    };
  }
```

| Name       | Type                      | Description                                            |
| ---------- | ------------------------- | ------------------------------------------------------ |
| `messages` | EncodeObject\[]           | The messages to sign.                                  |
| `fee`      | [Fee](terraclient.md#fee) | Fee to attach. Defaults to auto if not specified.      |
| `memo`     | string                    | Optional memo to attach. Defaults to an empty string.  |

#### Fee

```typescript
export type Fee = number | StdFee | "auto";
```

### broadcast

Broadcasts a given transaction to the connected client.

```typescript
 async broadcast(
    tx: Tx
  ): Promise<DeliverTxResponse & { logs: readonly Log[] }> {
    const resp = await this.signingClient?.tx.broadcast(tx);
    if (!resp) throw new Error("No response when broadcasting Tx");
    const logs = parseRawLog(resp.raw_log);
    const codeIdAttr = findAttribute(logs, "store_code", "code_id");
    return {
      ...resp,
      code: codeIdAttr ? parseInt(codeIdAttr.value, 10) : -1,
      transactionHash: resp.txhash,
      events: _.flatten(logs.map((log) => log.events)),
      gasUsed: resp.gas_used,
      gasWanted: resp.gas_wanted,
      logs,
    };
  }
```

| Name | Type                                                         | Description                    |
| ---- | ------------------------------------------------------------ | ------------------------------ |
| `tx` | [Tx](https://terra-money.github.io/terra.js/classes/Tx.html) | The transaction to broadcast.  |

### CreateTx

Creates a tx from the given message.

```typescript
  async createTx(
    messages: EncodeObject[],
    _fee?: Fee,
    memo?: string
  ): Promise<Tx> {
    const details = await this.signingClient?.auth.accountInfo(this.signer);
    if (!details)
      throw new Error(
        `Could not fetch account info for address ${this.signer}`
      );
    const tx = await this.signingClient?.tx.create(
      [
        {
          address: this.signer,
          sequenceNumber: details.getSequenceNumber(),
          publicKey: details.getPublicKey(),
        },
      ],
      {
        msgs: encodeObjectToMsgArgs(messages),
        memo,
      }
    );
    if (!tx) throw new Error("Failed to create Tx data");
    return tx;
  }
```

| Name       | Type                      | Description                                            |
| ---------- | ------------------------- | ------------------------------------------------------ |
| `messages` | EncodeObject\[]           | The messages to create a tx from.                      |
| `fee`      | [Fee](terraclient.md#fee) | Fee to attach. Defaults to auto if not specified.      |
| `memo`     | string                    | Optional memo to attach. Defaults to an empty string.  |

### signAndBroadcast

Signs a given message before broadcasting it to the connected chain.&#x20;

```typescript
async signAndBroadcast(
    messages: EncodeObject[],
    fee?: Fee,
    memo?: string | undefined
  ): Promise<DeliverTxResponse & { logs: readonly Log[] }> {
    const tx = await this.createTx(messages, fee, memo);
    return this.broadcast(tx);
  }
```

| Name        | Type                                           | Description                                                                                  |
| ----------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------- |
| `messages`  | [EncodeObject](terraclient.md#encodeobject)\[] | The messages to sign and broadcast.                                                          |
| `fee`       | [Fee](terraclient.md#fee)                      | The fee to pay for signing and broadcasting the message. Defaults to auto if not specified.  |
| `memo`      | string or undefined                            | An optional memo to attach to the transaction.                                               |

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
    messages: readonly EncodeObject[],
    fee?: Fee | undefined,
    memo?: string | undefined
  ): Promise<number> {
    const tx = await this.createTx([...messages], fee, memo);
    const resp = await this.signingClient?.tx.estimateGas(tx);

    return resp ?? -1;
  }
```

| Name       | Type                                           | Description                                  |
| ---------- | ---------------------------------------------- | -------------------------------------------- |
| `messages` | [EncodeObject](terraclient.md#encodeobject)\[] | The messages to simulate.                    |
| `fee`      | [Fee](terraclient.md#fee) or underfined        | The fee attached to the message.             |
| `memo`     | string or undefined                            | Optional memo to attach to the transaction.  |

### simulate

Simulates the given message and returns a gas fee estimate.

```typescript
async simulate(
    message: EncodeObject,
    fee?: Fee | undefined,
    memo?: string | undefined
  ): Promise<number> {
    const tx = await this.createTx([message], fee, memo);
    const resp = await this.signingClient?.tx.estimateGas(tx);

    return resp ?? -1;
  }
```

| Name      | Type                                        | Description                                  |
| --------- | ------------------------------------------- | -------------------------------------------- |
| `message` | [EncodeObject](terraclient.md#encodeobject) | The message to simulate.                     |
| `fee`     | [Fee](terraclient.md#fee) or undefined      | Fee attached to the message.                 |
| `memo`    | string or undefined                         | Optional memo to attach to the transaction.  |

### execute

Executes a message on the specified contract.

```typescript
 async execute(
    contractAddress: string,
    msg: Msg,
    fee?: Fee,
    memo?: string,
    funds?: readonly Coin[]
  ): Promise<ExecuteResult> {
    const message = this.encodeExecuteMsg(contractAddress, msg, [
      ...(funds ?? []),
    ]);
    return this.signAndBroadcast([message], fee, memo);
  }
```

| Name              | Type                                                                         | Description                                                             |
| ----------------- | ---------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `contractAddress` | string                                                                       | The contract address to execute on.                                     |
| `msg`             | [Msg](terraclient.md#msg)                                                    | The execute message.                                                    |
| `fee`             | [Fee](terraclient.md#fee)                                                    | The fee amount to pay for to execut. Defaults to auto if not specified. |
| `memo`            | string                                                                       | Optional memo to attach to the transaction.                             |
| `funds`           | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[] | Optional funds to attach along with the message.                        |

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
    fee?: Fee | undefined,
    memo?: string | undefined
  ): Promise<number | undefined> {
    const message = this.encodeExecuteMsg(address, msg, funds);
    return this.simulate(message, fee, memo);
  }
```

| Name      | Type                                                                         | Description                                 |
| --------- | ---------------------------------------------------------------------------- | ------------------------------------------- |
| `address` | string                                                                       | The contract address you are executing on.  |
| `msg`     | [Msg](terraclient.md#msg)                                                    | The execute message to simulate.            |
| `funds`   | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[] | The funds attached along with the message.  |
| `fee`     |  [Fee](terraclient.md#fee) or undefined                                      | Fee amount for executing the message.       |
| `memo`    | string or undefined                                                          | Optional memo to add to the transaction.    |

### upload

Uploads given contract code (Uint8Array) to the chain.

```typescript
async upload(
    code: Uint8Array,
    fee?: Fee | undefined,
    memo?: string | undefined
  ): Promise<UploadResult> {
    const compressed = gzip(code, { level: 9 });
    const message = this.encodeUploadMessage(compressed);
    const resp = await this.signAndBroadcast([message], fee, memo);

    const originalChecksum = toHex(sha256(code));
    const compressedChecksum = toHex(sha256(compressed));
    return {
      ...resp,
      codeId: resp.code,
      originalSize: code.length,
      originalChecksum,
      compressedSize: compressed.length,
      compressedChecksum,
    };
  }
```

| Name   | Type                                   | Description                                              |
| ------ | -------------------------------------- | -------------------------------------------------------- |
| `code` | Uint8Array                             | The wasm binary code to upload to the connected client.  |
| `fee`  | [Fee](terraclient.md#fee) or undefined | Fee to pay for uploading.                                |
| `memo` | string or undefined                    | Optional memo to add to the transaction.                 |

### simulateUpload

Simulate an upload message and returns a gas fee estimate.

```typescript
async simulateUpload(
    code: Uint8Array,
    fee?: Fee,
    memo?: string | undefined
  ): Promise<number | undefined> {
    const compressed = gzip(code, { level: 9 });
    const message = this.encodeUploadMessage(compressed);
    return this.simulate(message, fee, memo);
  }

```

| Name   | Type                      | Description                                 |
| ------ | ------------------------- | ------------------------------------------- |
| `code` | Uint8Array                | The wasm binary code to simulate uploading. |
| `_fee` | [Fee](terraclient.md#fee) | Fee to pay for uploading.                   |
| `memo` | string or undefined       | Optional memo to add to the transaction.    |

### instantiate

Instantiates a contract with the given code id using the provided instantiate message.

```typescript
  async instantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee?: Fee,
    options?: InstantiateOptions
  ): Promise<InstantiateResult> {
    const message = this.encodeInstantiateMsg(codeId, msg, label);
    const resp = await this.signAndBroadcast(
      [message],
      fee,
      options ? options.memo : ""
    );
    const contractAddressAttr = findAttribute(
      resp.logs,
      "wasm",
      "_contract_address"
    );

    return {
      ...resp,
      contractAddress: contractAddressAttr.value,
    };
  }
```

| Name      | Type                                                                                                              | Description                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `codeId`  | number                                                                                                            | The code Id of the contract to instantiate.                                                             |
| `msg`     | [Msg](terraclient.md#msg)                                                                                         | The instantiation message.                                                                              |
| `label`   | string                                                                                                            | A label for the instantiation. Can be any string such as " instantiating a CW721" or "my\_label" ect... |
| `fee`     | [Fee](terraclient.md#fee)                                                                                         | Fee to pay for instantiation. Defaults to auto.                                                         |
| `options` | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | The options of an .instantiate() call. All properties are optional.                                     |

### simulateInstantiate

Simulates an instantiation message and returns a gas fee estimate.

```typescript
async simulateInstantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee?: Fee,
    options?: InstantiateOptions
  ): Promise<number | undefined> {
    const message = this.encodeInstantiateMsg(codeId, msg, label);
    return this.simulate(message, fee, options?.memo);
  }
```

| Name      | Type                                                                                                              | Description                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `codeId`  | number                                                                                                            | The code Id of the contract to instantiate.                                                             |
| `msg`     | [Msg](terraclient.md#msg)                                                                                         | The instantiation message.                                                                              |
| `label`   | string                                                                                                            | A label for the instantiation. Can be any string such as " instantiating a CW721" or "my\_label" ect... |
| `fee`     | [Fee](terraclient.md#fee)                                                                                         | Fee to pay for instantiation. Defaults to auto if not specified.                                        |
| `options` | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | The options of an .instantiate() call. All properties are optional.                                     |

### migrate

Migrates a contract to a given code id.

```typescript
async migrate(
    contractAddress: string,
    codeId: number,
    msg: Msg,
    fee?: Fee,
    memo?: string
  ): Promise<MigrateResult> {
    const message = this.encodeMigrateMessage(contractAddress, codeId, msg);
    const resp = await this.signAndBroadcast([message], fee, memo);
    return resp;
  }
```

| Name              | Type                      | Description                                                         |
| ----------------- | ------------------------- | ------------------------------------------------------------------- |
| `contractAddress` | string                    | The address of the contract that you want to migrate.               |
| `codeId`          | number                    | The code Id to migrate to.                                          |
| `msg`             | [Msg](terraclient.md#msg) | The migrate message.                                                |
| `fee`             | [Fee](terraclient.md#fee) | The fee to pay for the migration. Defaults to auto if not specfied. |
| `memo`            | string or undefined       | Optional memo to attach to the transaction.                         |

### simulateMigrate

Simulates a migrate message for a given contract address, code id and migrate message and returns a gas estimate.

```typescript
async simulateMigrate(
    contractAddress: string,
    codeId: number,
    msg: Msg,
    fee?: Fee,
    memo?: string | undefined
  ): Promise<number | undefined> {
    const message = this.encodeMigrateMessage(contractAddress, codeId, msg);
    return this.simulate(message, fee, memo);
  }
```

| Name              |  Type                     | Description                                                     |
| ----------------- | ------------------------- | --------------------------------------------------------------- |
| `contractAddress` | string                    | The contract address that you want to migrate.                  |
| `codeId`          | number                    | The code Id to migrate to.                                      |
| `msg`             | Msg                       | The migrate message.                                            |
| `fee`             | [Fee](terraclient.md#fee) | Fee to pay to send the funds. Defaults to auto if not specifed. |
| `memo`            | string                    | Optional memo to attach to the transaction.                     |

### sendTokens

Sends tokens from the signing address to the provided receiving address.

```typescript
async sendTokens(
    receivingAddress: string,
    amount: readonly Coin[],
    fee?: Fee,
    memo?: string
  ): Promise<DeliverTxResponse> {
    const message = this.encodeSendMessage(receivingAddress, [...amount]);

    return this.signAndBroadcast([message], fee, memo);
  }
```

| Name               | Type                                                                          | Description                                                       |
| ------------------ | ----------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `receivingAddress` | string                                                                        | The address to receive the tokens.                                |
| `amount`           | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html) \[] | The funds to send.                                                |
| `fee`              | [Fee](terraclient.md#fee)                                                     | Fee to pay to send the funds. Defaults to auto if not specified.  |
| `memo`             | string                                                                        | Optional memo to attach to the transaction.                       |
