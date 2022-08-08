---
description: The GraphQL queries that can be performed on the Splitter ADO.
---

# Splitter

### queryConfig

Query the config of the splitter contract.

{% hint style="info" %}
Contract query can be found [here](../../../smart-contracts/finance/andromeda-splitter.md#getsplitterconfig).
{% endhint %}

```typescript
export async function queryConfig(
  contractAddress: string
): Promise<SplitterConfig> {
  const resp = await query<QuerySplitterConfig, QuerySplitterConfigResponse>(
    QUERY_SPLITTER_CONFIG,
    { contractAddress }
  );

  return resp.config;
}

```

| Name              | Type   | Description                                                            |
| ----------------- | ------ | ---------------------------------------------------------------------- |
| `contractAddress` | string | The contract address of the  splitter we want to query the config for. |

#### Response

```typescript
export interface QuerySplitterConfigResponse {
  config: SplitterConfig;
}
```

| Name              | Type   | Description                                                            |
| ----------------- | ------ | ---------------------------------------------------------------------- |
| `contractAddress` | string | The contract address of the  splitter we want to query the config for. |

#### SplitterConfig

```typescript
export interface SplitterConfig {
  locked: boolean;
  recipients: RecipientPercentage[];
}


export interface RecipientPercentage {
  percent: string;
  recipient: Recipient;
}
```

| Name         | Type                                         | Description                                       |
| ------------ | -------------------------------------------- | ------------------------------------------------- |
| `locked`     | boolean                                      | Whether the splitter is locked or not.            |
| `recipients` | [RecipientPercentage](splitter.md#undefined) | The recipients of the funds sent to the splitter. |

#### RecipientPercentage

```typescript
export interface RecipientPercentage {
  percent: string;
  recipient: Recipient;
}
```

| Name        | Type                      | Description                            |
| ----------- | ------------------------- | -------------------------------------- |
| `percent`   | string                    | The percent for the recipient.         |
| `recipient` | [Recipient](./#recipient) | The recipient of the above percentage. |
