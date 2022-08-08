---
description: The GraphQL queries that can be performed on the Crowdfund ADO.
---

# Crowdfund

### queryCrowdfundAvailableTokens

Queries the available tokens for sale.

{% hint style="info" %}
You can find the contract query [here](../../../smart-contracts/non-fungible-tokens/crowdfund.md#availabletokens).
{% endhint %}

```typescript
export async function queryCrowdfundAvailableTokens(
  contractAddress: string
): Promise<string[]> {
  const resp = await query<
    QueryCrowdfundAvailableTokens,
    QueryCrowdfundAvailableTokensResponse
  >(QUERY_CROWDFUND_AVAILABLE_TOKENS, { contractAddress });

  return resp.availableTokens;
}
```

| Name              | Type   | Description                                                                    |
| ----------------- | ------ | ------------------------------------------------------------------------------ |
| `contractAddress` | string | The contract address of the crowdfund ADO we want to get available tokens for. |

#### Response

```typescript
export interface QueryCrowdfundAvailableTokensResponse {
  availableTokens: string[];
}
```

| Name              | Type      | Description                                                                 |
| ----------------- | --------- | --------------------------------------------------------------------------- |
| `availableTokens` | string\[] | A vector of string that contains the token Ids of the tokens/NFTs for sale. |

### queryCrowdfundConfig

Queries the config of the crowdfund contract.

{% hint style="info" %}
The contract query can be found [here](../../../smart-contracts/non-fungible-tokens/crowdfund.md#config).
{% endhint %}

```typescript
export async function queryCrowdfundConfig(
  contractAddress: string
): Promise<CrowdfundConfig> {
  const resp = await query<QueryCrowdfundConfig, QueryCrowdfundConfigResponse>(
    QUERY_CROWDFUND_CONFIG,
    { contractAddress }
  );

  return resp.config;
}
```

| Name              | Type   | Description                                                              |
| ----------------- | ------ | ------------------------------------------------------------------------ |
| `contractAddress` | string | The contract address of the crowdfund ADO we want to get the config for. |

#### Response

```typescript
export interface QueryCrowdfundConfigResponse {
  config: CrowdfundConfig;
}
```

#### CrowdfundConfig

```typescript
export interface CrowdfundConfig {
  can_mint_after_sale: boolean;
  token_address: AndrAddress;
}
```

| Name                  | Type                          | Description                                                                       |
| --------------------- | ----------------------------- | --------------------------------------------------------------------------------- |
| `can_mint_after_sale` | boolean                       | Whether the crowdfund is able to mint new NFTs after the sale has been concluded. |
| `token_address`       | [AndrAddress](./#andraddress) | The contract address of the token/NFT contract.                                   |

### queryCrowdfundTokenAvailable

Query whether to token with the specified token Id is available for sale.

{% hint style="info" %}
The contract query can be found [here](../../../smart-contracts/non-fungible-tokens/crowdfund.md#istokenavailable).
{% endhint %}

```typescript
export async function queryCrowdfundTokenAvailable(
  contractAddress: string,
  tokenId: string
): Promise<boolean> {
  const resp = await query<
    QueryCrowdfundTokenAvailable,
    QueryCrowdfundTokenAvailableResponse
  >(QUERY_CROWDFUND_TOKEN_AVAILABLE, { contractAddress, tokenId });

  return resp.isTokenAvailable;
}
```

| Name              | Type   | Description                                                |
| ----------------- | ------ | ---------------------------------------------------------- |
| `contractAddress` | string | The contact address of the crowdfund ADO we want to query. |
| `tokenId`         | string | The Id of the token/NFT we want to check availability for. |

#### Response

```typescript
export interface QueryCrowdfundTokenAvailableResponse {
  isTokenAvailable: boolean;
}
```

| Name               | Type    | Description                            |
| ------------------ | ------- | -------------------------------------- |
| `isTokenAvailable` | boolean | Whether the token is available or not. |

### queryCrowdfundState

Queries the state of the crowdfund.

{% hint style="info" %}
The contract query can be found [here](../../../smart-contracts/non-fungible-tokens/crowdfund.md#state).
{% endhint %}

```typescript
export async function queryCrowdfundState(
  contractAddress: string
): Promise<CrowdfundState> {
  const resp = await query<QueryCrowdfundState, QueryCrowdfundStateResponse>(
    QUERY_CROWDFUND_STATE,
    { contractAddress }
  );

  return resp.state;
}
```

| Name              | Type   | Description                                                            |
| ----------------- | ------ | ---------------------------------------------------------------------- |
| `contractAddress` | string | The contract address of the crowfund ADO we want to get the state of.  |

#### Response

```typescript
export interface QueryCrowdfundStateResponse {
  state: CrowdfundState;
}
```

#### CrowdfundState

```typescript
export interface CrowdfundState {
  amount_sold: number;
  amount_to_send: number;
  amount_transferred: number;
  expiration: Expiry;
  max_amount_per_wallet: number;
  min_tokens_sold: number;
  price: Coin;
  recipient: Recipient;
}
```

| Name                    | Type                      | Description                                                                                                           |
| ----------------------- | ------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `amount_sold`           | number                    | The amount of tokens/NFTs sold.                                                                                       |
| `amount_to_send`        | number                    | The amount of funds to send to recipient if sale successful. This already takes into account the royalties and taxes. |
| `amount_transferred`    | number                    | Number of tokens transferred to purchasers if sale was successful.                                                    |
| `expiration`            | [Expiry](./#expiry)       | The expiration denoting when the sale ends.                                                                           |
| `max_amount_per_wallet` | number                    | The max number of tokens allowed per wallet.                                                                          |
| `min_tokens_sold`       | number                    | The minimum number of tokens sold for the sale to go through.                                                         |
| `price`                 | [Coin](crowdfund.md#coin) | The price of each token.                                                                                              |
| `recipient`             | [Recipient](./#recipient) | The recipient of the raised funds if the sale is successful.                                                          |

#### Coin

```typescript
export interface Coin {
/* The denomination of the Coin */
    readonly denom: string;
/* The amount of coins */
    readonly amount: string;
}
```
