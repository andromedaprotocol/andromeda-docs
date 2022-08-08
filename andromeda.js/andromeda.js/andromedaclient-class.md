# AndromedaClient Class

## Import

```typescript
import AndromedaClient from "@andromedaprotocol/andromeda.js"
```

## Class

A helper class for interacting with the Andromeda ecosystem

```typescript
export default class AndromedaClient {
  cosmWasmClient: SigningCosmWasmClient | undefined;
  signer: string = "";
  ado = new ADOAPI(this, "");

  /**
   * A pre-message hook to check that the client is connected and functioning
   */
  private preMessage() {
    if (!this.isConnected) throw new Error("Client not connected");
    if (!this.signer) throw new Error("No signing wallet assigned");
  }

```

| Name             | Type                  | Description                                                                                                                                                 |
| ---------------- | --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cosmWasmClient` | SigningCosmWasmClient | CosmWasm Signing Client. More information can be found [here](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html). |
| `signer`         | String                | The signer used to sign messages.                                                                                                                           |
| `ado`            | ADOAPI                | ///                                                                                                                                                         |

## Methods

### connect

Connects to a new chain by endpoint.

```typescript
async connect(
    endpoint: string,
    signer: OfflineSigner,
    registryAddress: string,
    options?: SigningCosmWasmClientOptions
  ) {
    delete this.cosmWasmClient;

    this.cosmWasmClient = await SigningCosmWasmClient.connectWithSigner(
      endpoint,
      signer,
      options
    );
    const [account] = await signer.getAccounts();
    this.signer = account.address;

    this.registry = new Primitive(registryAddress, this);
    const factoryAddress = await this.registry.get("factory");
    this.factory = new Factory(factoryAddress, this);
  }
```

| Name              | Type                                                                                                                                  | Description                                                                                                                                                                            |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `endpoint`        | string                                                                                                                                | The endpoint of the chain to connect to.                                                                                                                                               |
| `signer`          | OfflineSigner                                                                                                                         | The signer used to sign messaged.                                                                                                                                                      |
| `registryAddress` | string                                                                                                                                | The contract address of the registry.                                                                                                                                                  |
| `options`         | [SigningCosmWasmClientOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/SigningCosmWasmClientOptions.html) | Optional field to set the client options. More information can be found [here](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/SigningCosmWasmClientOptions.html). |

### signAndBroadcast

Creates a transaction with the given messages, fee and memo. Then signs and broadcasts the transaction.

```typescript
  async signAndBroadcast(
    messages: EncodeObject[],
    fee: Fee,
    memo?: string
  ): Promise<DeliverTxResponse> {
    this.preMessage();
    return this.cosmWasmClient!.signAndBroadcast(
      this.signer,
      messages,
      fee,
      memo
    );
  }
```

| Name       | Type                                      | Description                       |
| ---------- | ----------------------------------------- | --------------------------------- |
| `messages` | EncodeObject\[]                           | The messagesto broadcast.         |
| `fee`      | [Fee](andromedaclient-class.md#undefined) | The fees attached.                |
| `memo`     | string                                    | optional memo to the transaction. |

{% hint style="info" %}
This function is a wrapper function for CosmWasm sign and broadcast. Check [signAndBroadcast](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#signAndBroadcast) for more details.
{% endhint %}

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
async upload(code: Uint8Array, fee: Fee, memo?: string) {
    this.preMessage();
    return await this.cosmWasmClient!.upload(this.signer, code, fee, memo);
  }
```

| Name   | Type                                | Description                |
| ------ | ----------------------------------- | -------------------------- |
| `code` | Uint8Array                          | The code to upload.        |
| `fee`  | [Fee](andromedaclient-class.md#fee) | The fee to pay.            |
| `memo` | string                              | Optional memo to attatch.  |

### execute

Wrapper function for CosmWasm [execute](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#execute).

```typescript
async execute(
    contractAddress: string,
    msg: Msg,
    fee: Fee,
    memo?: string,
    funds?: readonly Coin[]
  ) {
    this.preMessage();
    return await this.cosmWasmClient!.execute(
      this.signer,
      contractAddress,
      msg,
      fee,
      memo,
      funds
    );
  }
```

| Name    | Type                                                                                  | Description                               |
| ------- | ------------------------------------------------------------------------------------- | ----------------------------------------- |
| `msg`   | Msg                                                                                   | The message to execute.                   |
| `fee`   | Fee                                                                                   | The fee to pay.                           |
| `memo`  | string                                                                                | Optional memo to attach along a message.  |
| `funds` | readonly[ Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[] | Optional funds to send along the message. |

### instantiate

Wrapper function for CosmWasm [instantiate](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#instantiate).

```typescript
  async instantiate(
    codeId: number,
    msg: Msg,
    label: string,
    fee: Fee,
    options?: InstantiateOptions
  ) {
    this.preMessage();
    return await this.cosmWasmClient!.instantiate(
      this.signer,
      codeId,
      msg,
      label,
      fee,
      options
    );
  }
```

| Name      | Type                                                                                                              | Description                                                                 |
| --------- | ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `codeId`  | number                                                                                                            | The code ID of the contract to instantiate.                                 |
| `msg`     | Msg                                                                                                               | The instantiation message.                                                  |
| `fee`     | string                                                                                                            | The fee to pay.                                                             |
| `options` | [InstantiateOptions](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/interfaces/InstantiateOptions.html) | Options for the instantiate call. Includes optional admin, funds, and memo. |

### queryContract

Makes a smart query on the contract, returns the parsed JSON document. Promise is rejected when contract does not exist. Promise is rejected for invalid query format. Promise is rejected for invalid response format.

{% hint style="warning" %}
Wrapper function for CosmWasm [query](https://cosmos.github.io/cosmjs/latest/cosmwasm-stargate/classes/SigningCosmWasmClient.html#queryContractSmart).
{% endhint %}

```typescript
async queryContract<T = any>(address: string, query: Msg) {
    this.preMessage();
    return (await this.cosmWasmClient!.queryContractSmart(address, query)) as T;
  }
```

| Name      | Type                                      | Description                           |
| --------- | ----------------------------------------- | ------------------------------------- |
| `address` | string                                    | The address of the contract to query. |
| `query`   | [Msg](andromedaclient-class.md#undefined) | The query message.                    |

#### Msg

```typescript
export type Msg = Record<string, unknown>;
```

### simulateTx

Estimates the gas cost of sending an execute transaction.

```typescript
  async simulateTx(
    address: string,
    msg: Msg,
    funds: Coin[],
    memo: string = ""
  ) {
    this.preMessage();
    return await this.cosmWasmClient?.simulate(
      this.signer,
      [this.encodeExecuteMsg(address, msg, funds)],
      memo
    );
  }
```

| Name      | Type                                                                         | Description           |
| --------- | ---------------------------------------------------------------------------- | --------------------- |
| `address` | string                                                                       | The contract address. |
| `Msg`     | [Msg](andromedaclient-class.md#msg)                                          | The message.          |
| `funds`   | [Coin\[](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)] | The funds sent along  |
| `memo`    | string                                                                       | The memo attached.    |

Returns a gas fee estimation.

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
}
```

| Name      | Type                                                                         | Description                        |
| --------- | ---------------------------------------------------------------------------- | ---------------------------------- |
| `address` | string                                                                       | The contract address.              |
| `msg`     | [Msg](andromedaclient-class.md#msg)                                          | The execute message.               |
| `funds`   | [Coin](https://cosmos.github.io/cosmjs/latest/amino/interfaces/Coin.html)\[] | The funds attached to the message. |

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

|        |                      |                                      |
| ------ | -------------------- | ------------------------------------ |
| `json` | Record\<string, any> | JSON object to convert to Uint8Array |

Returns the Uint8Array.
