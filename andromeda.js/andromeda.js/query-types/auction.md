---
description: The GraphQL queries that can be performed on the Auction ADO.
---

# Auction

### queryAuctionIds

Queries the Auction Ids for the specified token.

{% hint style="info" %}
You can find the contract query[ here](../../../smart-contracts/non-fungible-tokens/auction.md#auctionids).
{% endhint %}

```typescript
export async function queryAuctionIds(
  tokenId: string,
  tokenAddress: string
): Promise<number[]> {
  const resp = await query<
    QueryAuctionAuctionIds,
    QueryAuctionAuctionIdsResponse
  >(QUERY_AUCTION_AUCTION_IDS, { tokenId, tokenAddress });

  return resp.auctionIds;
}
```

| Name           | Type   | Description                                         |
| -------------- | ------ | --------------------------------------------------- |
| `tokenId`      | string | The Id of the token we want to get Auction Ids for. |
| `tokenAddress` | string | The contract address of the  token contract.        |

#### Response

```typescript
export interface QueryAuctionAuctionIdsResponse {
  auctionIds: number[];
}
```

| Name         | Type      | Description      |
| ------------ | --------- | ---------------- |
| `auctionIds` | number\[] | The auction Ids. |

### queryAuctionInfo

Queries the information for the specified auction.

{% hint style="info" %}
You can find the contract query [here](../../../smart-contracts/non-fungible-tokens/auction.md#auctioninfosforaddress).
{% endhint %}

```typescript
export async function queryAuctionInfo(
  options: AndrSearchOptions,
  tokenAddress: string
): Promise<QueryAuctionAuctionInfoResponse> {
  const resp = await query<
    QueryAuctionAuctionInfo,
    QueryAuctionAuctionInfoResponse
  >(QUERY_AUCTION_AUCTION_INFO, { tokenAddress, options });

  return resp;
}
```

| Name           | Type                                      | Description                                 |
| -------------- | ----------------------------------------- | ------------------------------------------- |
| `options`      | [AndrSearchOptions](./#andrsearchoptions) | Options for pagination.                     |
| `tokenAddress` | string                                    | The contract address of the token contract. |

#### Response

```typescript
export interface QueryAuctionAuctionInfoResponse {
  auctionInfo: {
    auctionIds: number[];
    contractAddress: string;
    tokenId: string;
  };
}
```

| Name              | Type      |                                                                           |
| ----------------- | --------- | ------------------------------------------------------------------------- |
| `auctionIds`      | number\[] | The auction Ids of the auctions that use the specified `contractAddress`. |
| `contractAddress` | string    | The contract address of the  auction ADO.                                 |
| `tokenId`         | string    | The Id of the token that was auctioned.                                   |

### queryAuctionState

Queries the state of the specified auction.

{% hint style="info" %}
You can find the contract query [here](../../../smart-contracts/non-fungible-tokens/auction.md#auctionstate).
{% endhint %}

```typescript
export async function queryAuctionState(
  auctionId: number
): Promise<AuctionState> {
  const resp = await query<
    QueryAuctionAuctionState,
    QueryAuctionAuctionStateResponse
  >(QUERY_AUCTION_AUCTION_STATE, { auctionId });

  return resp.auctionState;
}
```

| Name        | Type   | Description                                |
| ----------- | ------ | ------------------------------------------ |
| `auctionId` | number | The Id of the auction to get the state of. |

#### Response

```typescript
export interface AuctionState {
  adoId: string;
  adoType: string;
  auctionId: number;
  coinDenom: string;
  endTime: Expiry;
  highBidderAddr: AndrAddress;
  highBidderAmount: number;
  isCancelled: boolean;
  modules: {
    address: AndrAddress;
    isMutable: boolean;
    moduleType: string;
  }[];
  startTime: Expiry;
  tokenAddress: string;
  tokenId: string;
  whitelist: AndrAddress[];
}
```

| Name               | Type                          | Description                                                                                                       |
| ------------------ | ----------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `adoId`            | string                        | The Id of the ado.                                                                                                |
| `adoType`          | string                        | The type of the ado. ("auction").                                                                                 |
| `auctionId`        | number                        | The Id of the auction.                                                                                            |
| `coinDenom`        | string                        | The denom of the funds used in the auction.                                                                       |
| `endTime`          | [Expiry](./#expiry)           | When the auction will end.                                                                                        |
| `highBidderAmount` | number                        | The highest bid.                                                                                                  |
| `isCancelled`      | boolean                       | Whether the auction was cancelled.                                                                                |
| `modules`          |                               | Query information on each of the modules on the auction contract.                                                 |
| `address`          | [AndrAddress](./#andraddress) | The address of the module contract.                                                                               |
| `isMutable`        | boolean                       | Whether the module is mutable (Can be changed/altered).                                                           |
| `moduleType`       | string                        | The type of the module. (One of the four types found in the [modules section](../../../smart-contracts/modules/)) |
| `startTime`        | [Expiry](./#expiry)           | The start time of the auction.                                                                                    |
| `tokenAddress`     | string                        | The contract address of the token being auctioned.                                                                |
| `tokenId`          | string                        | The id of the token/NFT being auctioned.                                                                          |
| `whitelist`        | [AndrAddress](./#andraddress) | Vector of AndrAdress representing the whitelisted addresses.                                                      |

### queryBids

Queries the bids placed on the specified `auctionId`.

{% hint style="info" %}
You can find the contract query [here](../../../smart-contracts/non-fungible-tokens/auction.md#bids).
{% endhint %}

```typescript
export async function queryBids(
  auctionId: number,
  options?: AndrSearchOptions
): Promise<Bid[]> {
  const resp = await query<QueryAuctionBids, QueryAuctionBidsResponse>(
    QUERY_AUCTION_BIDS,
    { auctionId, options }
  );

  return resp.bids;
}
```

| Name        | Type                                      | Description                                          |
| ----------- | ----------------------------------------- | ---------------------------------------------------- |
| `auctionId` | number                                    | The Id of the auction to query bids for.             |
| `options?`  | [AndrSearchOptions](./#andrsearchoptions) | Optional set of options to used to setup pagination. |

#### Response

```typescript
export interface QueryAuctionBidsResponse {
  bids: Bid[];
}
```

| Name   | Type   | Description                                                   |
| ------ | ------ | ------------------------------------------------------------- |
| `bids` | Bid\[] | A vector of Bid containing the bids of the specified auction. |

#### Bid

```typescript
export interface Bid {
  amount: number;
  bidder: string;
  timestamp: string;
}
```

| Name        | Type   | Description                     |
| ----------- | ------ | ------------------------------- |
| `amount`    | number | The amount of coins of the bid. |
| `bidder`    | string | The bidder address.             |
| `timestamp` | string | The time of the bid.            |

### queryAuctionLatestState

Queries the most recent auction for the given token (either ongoing, complete, or not started yet).

{% hint style="info" %}
You can find the contract query [here](../../../smart-contracts/non-fungible-tokens/auction.md#latestauctionstate).
{% endhint %}

```typescript
export async function queryAuctionLatestState(
  tokenAddress: string,
  tokenId: string
): Promise<AuctionState> {
  const resp = await query<
    QueryAuctionLatestAuctionState,
    QueryAuctionLatestAuctionStateResponse
  >(QUERY_AUCTION_LATEST_AUCTION_STATE, { tokenAddress, tokenId });

  return resp.latestAuctionState;
}
```

| Name           | Type   | Description                                        |
| -------------- | ------ | -------------------------------------------------- |
| `tokenAddress` | string | The contract address of the token being auctioned. |
| `tokenId`      | string | The Id of the token/NFT being auctioned.           |

#### Response

```typescript
export interface QueryAuctionLatestAuctionStateResponse {
  latestAuctionState: AuctionState;
}
```

Returns an [AuctionState](auction.md#queryauctionstate).
