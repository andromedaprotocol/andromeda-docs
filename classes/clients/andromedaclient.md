# AndromedaClient

## Import

```typescript
import AndromedaClient from "@andromedaprotocol/andromeda.js"
```

## Class

A helper class for interacting with the Andromeda ecosystem

```typescript
export default class AndromedaClient {
  public chainClient?: ChainClient;

  /**
   * Instantiate all provided APIs
   */
  public ado = new ADOAPI(this);
  public schema = new ADOSchemaAPI(this);
  public os = new OperatingSystemAPI(this);

  /**
   * A pre-message hook to check that the client is connected and functioning
   * @param signed Whether the message is signed
   */
  private preMessage() {
    if (!this.isConnected) throw new Error("Client not connected");
  }

```

| Name          | Type                                                       | Description                                                                                                                       |
| ------------- | ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `chainClient` | [ChainClient](chainclient.md)                              | Client used to interact with the chain, includes a query client when connected and a signing client when connected with a signer. |
| `ado`         | [ADOAPI](../api-classes/)                                  | API for shared ADO messages.                                                                                                      |
| `schema`      | [ADOSchemaAPI](../api-classes/adoschemaapi.md)             | API for schema specific messages.                                                                                                 |
| `os`          | [OperatingSystemAPI](../api-classes/operatingsystemapi.md) | API for OS contracts specific messages.                                                                                           |

## Methods

### connect

Connects to a new chain by endpoint.

```typescript
  async connect(
    endpoint: string,
    kernelAddress: string,
    addressPrefix: string,
    signer?: OfflineSigner | OfflineDirectSigner,
    // Only used for Cosmos Clients
    options?: SigningCosmWasmClientOptions
  ) {
    delete this.chainClient;

    this.chainClient = createClient(addressPrefix);
    await this.chainClient.connect(endpoint, signer, options);
    await this.assignKeyAddresses(kernelAddress);
  }
```

<table><thead><tr><th width="218.22184091187643">Name</th><th width="301.16406249999994">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>endpoint</code></td><td>string</td><td>The endpoint of the chain to connect to. </td></tr><tr><td>kernelAddress</td><td>string</td><td>The contract address of the kernel contract.</td></tr><tr><td><code>addressPrefix</code></td><td>string</td><td>The prefixo of the connecting address. </td></tr><tr><td><code>signer</code></td><td><a href="https://cosmos.github.io/cosmjs/latest/proto-signing/modules.html#OfflineSigner">OfflineSigner</a> or <a href="https://cosmos.github.io/cosmjs/latest/proto-signing/interfaces/OfflineDirectSigner.html">OfflineDirectSigner</a></td><td>Optional signer used to sign the messages.</td></tr><tr><td><code>options</code></td><td><a href="https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/SigningCosmWasmClientOptions.html">SigningCosmWasmClientOptions</a></td><td>Optional field to set the client options. </td></tr></tbody></table>

### assignKeyAddresses

Assigns the kernel address and fetches the VFS and ADODB addresses.

```typescript
  private async assignKeyAddresses(kernelAddress: string) {
    if (kernelAddress && kernelAddress.length > 0) {
      await this.os.assignKernelAddress(kernelAddress);
    }
  }
```

| Name            | Type   | Description                                    |
| --------------- | ------ | ---------------------------------------------- |
| `kernelAddress` | string | The address of the kernel  contract to assign. |

### disconnect

Disconnects the assigned clients.

```typescript
 disconnect() {
    this.chainClient!.disconnect();
    delete this.chainClient;
    this.os = new OperatingSystemAPI(this);
  }
```

### isConnected

Getter method to indicate whether the client is currently connected.

```typescript
get isConnected() {
    return !isUndefined(this.chainClient) && this.chainClient!.isConnected;
  }
```

### signAndBroadcast

Wrapper function for CosmWasm [sign and broadcast](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#signAndBroadcast). Creates a transaction with the given messages, fee and memo and then signs and broadcasts the transaction.

```typescript
  async signAndBroadcast(
    messages: EncodeObject[],
    fee: Fee,
    memo?: string
  ): Promise<DeliverTxResponse> {
    this.preMessage();
    return this.chainClient!.signAndBroadcast(messages, fee, memo);
  }
```

| Name       | Type                                               | Description                       |
| ---------- | -------------------------------------------------- | --------------------------------- |
| `messages` | [EncodeObject](andromedaclient.md#encodeobject)\[] | The messages to broadcast.        |
| `fee`      | [Fee](andromedaclient.md#fee)                      | The fee to pay for the message.   |
| `memo`     | string                                             | optional memo to the transaction. |

#### Fee

```typescript
export type Fee = number | StdFee | "auto"
```

Check [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html).

#### EncodeObject

```typescript
export interface EncodeObject {
    readonly typeUrl: string;
    readonly value: any;
}
```

### upload

Wrapper function for CosmWasm [upload](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#upload). Uploads code and returns a receipt, including the code ID.

```typescript
 async upload(code: Uint8Array, fee?: Fee, memo?: string) {
    this.preMessage();
    return this.chainClient!.upload(code, fee, memo);
  }
```

| Name   | Type                          | Description                                        |
| ------ | ----------------------------- | -------------------------------------------------- |
| `code` | Uint8Array                    | The code to upload.                                |
| `fee`  | [Fee](andromedaclient.md#fee) | The fee to pay. Defaults to auto if not specified. |
| `memo` | string                        | Optional memo to attatch.                          |

### execute

Wrapper function for CosmWasm [execute](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#execute). Execute the defined message.

```typescript
  async execute(
    contractAddress: string,
    msg: Msg,
    fee?: Fee,
    memo?: string,
    funds?: readonly Coin[]
  ) {
    this.preMessage();
    return await this.chainClient!.execute(
      contractAddress,
      msg,
      fee,
      memo,
      funds
    );
  }
```

| Name            | Type                                                                                  | Description                               |
| --------------- | ------------------------------------------------------------------------------------- | ----------------------------------------- |
| contractAddress | string                                                                                | The contract address to execute on.       |
| `msg`           | [Msg](andromedaclient.md#msg)                                                         | The message to execute.                   |
| `fee`           | [Fee](andromedaclient.md#fee)                                                         | The fee to pay.                           |
| `memo`          | string                                                                                | Optional memo to attach along a message.  |
| `funds`         | readonly[ Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[] | Optional funds to send along the message. |

### instantiate

Wrapper function for CosmWasm [instantiate](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#instantiate). Instantiates the defined message.

```typescript
  async instantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee?: Fee,
    options?: InstantiateOptions
  ) {
    this.preMessage();
    return await this.chainClient!.instantiate(codeId, msg, label, fee, {
      admin: this.chainClient!.signer,
      ...options,
    });
  }
```

| Name      | Type                                                                                                              | Description                                                                 |
| --------- | ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `codeId`  | number                                                                                                            | The code ID of the contract to instantiate.                                 |
| `msg`     | [Msg](andromedaclient.md#msg)                                                                                     | The instantiation message.                                                  |
| `label`   | string                                                                                                            | The label attached to instantiation.                                        |
| `fee`     | string                                                                                                            | The fee to pay. Defaults to auto if not specified.                          |
| `options` | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | Options for the instantiate call. Includes optional admin, funds, and memo. |

### queryContract

Makes a smart query on the contract, returns the parsed JSON document. Promise is rejected when contract does not exist, for invalid query format, and for invalid response format.

{% hint style="warning" %}
Wrapper function for CosmWasm [query](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#queryContractSmart).
{% endhint %}

```typescript
 async queryContract<T = any>(address: string, query: Msg) {
    this.preMessage();
    return (await this.chainClient!.queryClient!!.queryContractSmart(
      address,
      query
    )) as T;
  }
```

| Name      | Type                          | Description                           |
| --------- | ----------------------------- | ------------------------------------- |
| `address` | string                        | The address of the contract to query. |
| `query`   | [Msg](andromedaclient.md#msg) | The query message.                    |

#### Msg

```typescript
export type Msg = Record<string, unknown>;
```

### migrate

Wrapper function to ComsWasm [migrate](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#migrate).

```typescript
 async migrate(
    contractAddress: string,
    codeId: number,
    msg: Msg,
    fee?: Fee,
    memo?: string
  ) {
    this.preMessage();
    return await this.chainClient!.migrate(
      contractAddress,
      codeId,
      msg,
      fee,
      memo
    );
  }
```

| Name              | Type                          | Description                                        |
| ----------------- | ----------------------------- | -------------------------------------------------- |
| `contractAddress` | string                        | The contract address to migrate.                   |
| `codeId`          | number                        | The code Id to migrate to.                         |
| `msg`             | [Msg](andromedaclient.md#msg) | The migrate message.                               |
| `fee`             | [Fee](andromedaclient.md#fee) | The fee to pay. Defaults to auto if not specified. |
| `memo`            | string                        | Optional memo to attach.                           |

### simulateMigrate

Estimates the gas cost of sending a migrate transaction.

```typescript
async simulateMigrate(
    address: string,
    codeId: number,
    msg: Msg,
    fee?: StdFee
  ) {
    this.preMessage();
    return this.simulateMsgs(
      [this.chainClient!.encodeMigrateMessage(address, codeId, msg)],
      fee
    );
  }
```

| Name      | Type                                                                             | Description                                                      |
| --------- | -------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| `address` | string                                                                           | The contract address to migrate.                                 |
| `codeId`  | number                                                                           | The code Id to migrate to.                                       |
| `msg`     | [Msg](andromedaclient.md#msg)                                                    | The migrate message.                                             |
| `fee`     | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee to pay for the migration. Defaults to auto if not specified. |

### estimateMigrateFee

Estimates the fee cost of sending a migrate transaction.

```typescript
  async estimateMigrateFee(address: string, codeId: number, msg: Msg) {
    this.preMessage();
    return this.estimateFee([
      this.chainClient!.encodeMigrateMessage(address, codeId, msg),
    ]);
  }
```

| Name      | Type                          | Description                      |
| --------- | ----------------------------- | -------------------------------- |
| `address` | string                        | The contract address to migrate. |
| `codeId`  | number                        | The code Id to migrate to.       |
| `msg`     | [Msg](andromedaclient.md#msg) | The migrate message.             |

### simulateExecute

Estimates the gas cost of sending an execute transaction.

```typescript
  async simulateExecute(
    address: string,
    msg: Msg,
    funds: Coin[],
    fee?: StdFee,
    memo: string = ""
  ) {
    this.preMessage();
    return this.simulateMsgs(
      [this.chainClient!.encodeExecuteMsg(address, msg, funds)],
      fee,
      memo
    );
  }
```

| Name      | Type                                                                             | Description                                                      |
| --------- | -------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| `address` | string                                                                           | The contract address to simulate the execute for.                |
| `msg`     | [Msg](andromedaclient.md#msg)                                                    | The message of the execute.                                      |
| `funds`   | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[]     | The amount of funds attached to the message.                     |
| `fee`     | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee to pay for the execution. Defaults to auto if not specified. |
| `memo`    | string                                                                           | Optional memo to attach.                                         |

### simulateMsgs

Simulates provided messages returning an estimated gas cost.

```typescript
async simulateMsgs(
    msgs: readonly EncodeObject[],
    fee?: StdFee,
    memo?: string
  ) {
    const gas = await this.chainClient?.simulateMulti(msgs, fee, memo);
    return gas;
  }
```

| Name   | Type                                                                             | Description               |
| ------ | -------------------------------------------------------------------------------- | ------------------------- |
| `msgs` | [EncodeObject](andromedaclient.md#encodeobject)\[]                               | The messages to simulate. |
| `fee`  | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee to pay.               |
| `memo` | string                                                                           | Optional memo to attach.  |

### simulateInstantiate

Estimates the gas cost of sending an instantiate transaction and returns it.

```typescript
async simulateInstantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee?: StdFee,
    memo?: string
  ) {
    this.preMessage();
    console.log(msg);
    return this.simulateMsgs(
      [this.chainClient!.encodeInstantiateMsg(codeId, msg, label)],
      fee,
      memo
    );
  }
```

| Name     | Type                                                                             | Description                                  |
| -------- | -------------------------------------------------------------------------------- | -------------------------------------------- |
| `codeId` | number                                                                           | The code Id of the contract to instantiate.  |
| `msg`    | [Msg](andromedaclient.md#msg)                                                    | The instantiation message.                   |
| `label`  | string                                                                           | The label attached to the instantiation.     |
| `fee`    | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | Fee to pay.                                  |
| `memo`   | string                                                                           | Optional memo to attach.                     |

### simulateUpload

Estimates the gas cost of sending an upload transaction.

```typescript
async simulateUpload(wasmByteCode: Uint8Array) {
    return this.chainClient?.simulateUpload(wasmByteCode);
  }
```

| Name           | Type       | Description                          |
| -------------- | ---------- | ------------------------------------ |
| `wasmByteCode` | Uint8Array | The wasm code to simulate uploading. |

### estimateUploadFee

Estimates the fee cost of sending an upload transaction.

```typescript
 async estimateUploadFee(wasmByteCode: Uint8Array) {
    this.preMessage();
    return this.estimateFee([
      this.chainClient!.encodeUploadMessage(wasmByteCode),
    ]);
  }
```

| Name           | Type       | Description                          |
| -------------- | ---------- | ------------------------------------ |
| `wasmByteCode` | Uint8Array | The wasm code to simulate uploading. |

### calculateFee

Wrapped around cosmjs calculateFee using client's set gasPrice. Errors if no gas price provided.

```typescript
 calculcateFee(gas: number) {
    const gasPrice = this.chainClient?.gasPrice;
    if (!gasPrice)
      throw new Error(
        "No gas prices provided for client. Cannot simulate Tx fee."
      );
    const multiplier = 1.3; // Unsure why this is necessary but is added during simulateTx in cosmjs
    return calculateFee(Math.round(gas * multiplier), gasPrice);
  }
```

| Name  | Type   | Description        |
| ----- | ------ | ------------------ |
| `gas` | number | The amount of gas. |

### estimateFee

Simulates provided messages and calculates an estimated fee.

```typescript
 async estimateFee(
    msgs: readonly EncodeObject[],
    fee?: StdFee,
    memo?: string
  ) {
    const gas = await this.simulateMsgs(msgs, fee, memo);
    if (!gas) {
      throw new Error("Could not simulate transaction");
    }
    return this.calculcateFee(gas);
  }
```

| Name   | Type                                                                             | Description                           |
| ------ | -------------------------------------------------------------------------------- | ------------------------------------- |
| `msgs` | EncodeObject\[]                                                                  | The messages to estimate the fee for. |
| `fee`  | [StdFee](https://cosmos.github.io/cosmjs/latest/stargate/interfaces/StdFee.html) | The fee to pay.                       |
| `memo` | string                                                                           | Optional memo to attach.              |

### estimateExecuteFee

Estimates the fee cost of sending an execute transaction.

```typescript
async estimateExecuteFee(
    address: string,
    msg: Msg,
    funds: Coin[],
    fee?: StdFee,
    memo: string = ""
  ) {
    this.preMessage();
    return this.estimateFee(
      [this.chainClient!.encodeExecuteMsg(address, msg, funds)],
      fee,
      memo
    );
  }
```

The fields are the same as in [execute](andromedaclient.md#execute) but will return an estimate of the fees instead of executing the message.&#x20;

### estimateInstantiationFee

Estimates the fee cost of sending an instantiate transaction and returns it.

```typescript
async estimateInstantiationFee(
    codeId: number,
    msg: Msg,
    label: string,
    fee?: StdFee,
    memo?: string
  ) {
    this.preMessage();
    return this.estimateFee(
      [this.chainClient!.encodeInstantiateMsg(codeId, msg, label)],
      fee,
      memo
    );
  }
```

| Name     | Type                          | Description                                 |
| -------- | ----------------------------- | ------------------------------------------- |
| `codeId` | number                        | The code ID of the contract to instantiate. |
| `msg`    | [Msg](andromedaclient.md#msg) | The instantiation message                   |
| `label`  | string                        | The label attached to instantiation         |
| `memo`   | string                        | Optional memo to attach.                    |

### getBalance

Gets the balance for a given address and denom. Defaults to the signing wallet address if none provided.

```typescript
 async getBalance(denom: string, address?: string) {
    this.preMessage();
    const _address =
      address && address.length > 0 ? address : this.chainClient!.signer;
    if (!_address || _address.length === 0) throw new Error("Invalid address");

    return this.chainClient!.queryClient!.getBalance(_address, denom);
  }
```

| Name      | Type   | Description                                                                                           |
| --------- | ------ | ----------------------------------------------------------------------------------------------------- |
| `denom`   | string | The denom to check the balance for.                                                                   |
| `address` | string | The address to check the balance for. If not specified, the address of the connected signer is used.  |

### getSentTxsByAddress

Gets all send transactions for a given address.

```typescript
  async getSentTxsByAddress(addr: string) {
    this.preMessage();
    return this.chainClient!.queryClient!?.searchTx([{ key: "message.sender", value: addr }]);
  }
```

| Name   | Type   | Description                                   |
| ------ | ------ | --------------------------------------------- |
| `addr` | string | The address to get the send transactions for. |

### getTx

Wrapper around the cosm.js client's [getTx](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/CosmWasmClient.html#getTx) function.

```typescript
 async getTx(hash: string) {
    this.preMessage();
    return this.chainClient!.queryClient!.getTx(hash);
  }
```

| Name   | Type   | Description  |
| ------ | ------ | ------------ |
| `hash` | string | The tx hash. |

### getTxsByContract

Gets all transactions sent to a contract.

```typescript
async getTxsByContract(addr: string) {
    this.preMessage();
    return this.chainClient!.queryClient!?.searchTx({
      tags: [{ key: "execute._contract_address", value: addr }],
    });
  }
```

| Name   | Type   | Description                                     |
| ------ | ------ | ----------------------------------------------- |
| `addr` | string | The contract address to check transactions for. |

### getBankTxsByAddress

Gets all bank messages sent to or from an address.

```typescript
  async getBankTxsByAddress(addr: string) {
    this.preMessage();
    const sentQuery = `message.module='bank' AND transfer.sender='${addr}'`;
    const receivedQuery = `message.module='bank' AND transfer.recipient='${addr}'`;
    const [sent, received] = await Promise.all(
      [sentQuery, receivedQuery].map((rawQuery) => this.chainClient!.queryClient!?.searchTx(rawQuery)),
    );
    const sentHashes = sent.map((t) => t.hash);
    return [...sent, ...received.filter((t) => !sentHashes.includes(t.hash))];
  };
```

| Name   | Type   | Description                                 |
| ------ | ------ | ------------------------------------------- |
| `addr` | string | The address to check the bank messages for. |

### getAllTxsByAddress

Queries all possible transactions for a given address.

```typescript
  async getAllTxsByAddress(addr: string) {
    const sentTxs = await this.getSentTxsByAddress(addr);
    const contractTxs = await this.getTxsByContract(addr);
    const bankTxs = await this.getBankTxsByAddress(addr);
    return [
      ...(sentTxs ?? []),
      ...(contractTxs ?? []),
      ...(bankTxs ?? []),
    ].sort((txA, txB) => (txA.height < txB.height ? 1 : -1));
  }
}
```

| Name   | Type   | Description                                  |
| ------ | ------ | -------------------------------------------- |
| `addr` | string | The address to get all the transactions for. |

### sendTokens

wrapper around the cosm.js client's [sendTokens](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#sendTokens) function. Send tokens to another address.

```typescript
 async sendTokens(
    receivingAddress: string,
    amount: readonly Coin[],
    fee?: Fee,
    memo?: string
  ) {
    this.preMessage();
    return this.chainClient?.sendTokens(receivingAddress, amount, fee, memo);
  }
```

| Name               | Type                                                                          | Description                                                           |
| ------------------ | ----------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| `receivingAddress` | string                                                                        | The address to receive the tokens.                                    |
| `amount`           | [Coin\[\]](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html) | The amount of tokens to send.                                         |
| `fee`              | [Fee](andromedaclient.md#fee)                                                 | Fee to pay for sending the tokens. Defaults to auto if not specified. |
| `memo`             | string                                                                        | An optional memo to attach.                                           |

## Helper Function

### JsonToArray

Helper function to convert JSON to Uint8Array

```typescript
const JsonToArray = function (json: Record<string, any>) {
  var str = JSON.stringify(json, null, 0);
  var ret = new Uint8Array(str.length);
  for (var i = 0; i < str.length; i++) {
    ret[i] = str.charCodeAt(i);
  }
  return ret;
};
```

| Name   | Type                 | Description                          |
| ------ | -------------------- | ------------------------------------ |
| `json` | Record\<string, any> | JSON object to convert to Uint8Array |

Returns the Uint8Array.
