---
description: The GraphQL queries that can be performed on the CW721 Offers ADO.
---

# CW721 Offers

### queryAllOffers

Query all the offers placed by the specified purchaser.

{% hint style="info" %}
Contract query can be found [here](../../../smart-contracts/modules/nft-offers.md#alloffers).
{% endhint %}

```typescript
export async function queryAllOffers(
  contractAddress: string,
  purchaser: string,
  options: AndrSearchOptions
): Promise<NFTOffer[]> {
  const resp = await query<
    QueryCW721OffersAllOffers,
    QueryCW721OffersAllOffersResponse
  >(QUERY_CW721_OFFERS_ALL_OFFERS, { purchaser, contractAddress, options });

  return resp.allOffers;
}
```

| Name              | Type                                      | Description                                                            |
| ----------------- | ----------------------------------------- | ---------------------------------------------------------------------- |
| `contractAddress` | string                                    | The contract address of the cw721 offers contract to query.            |
| `purchaser`       | string                                    | The address that we want to get the offers for.                        |
| `options`         | [AndrSearchOptions](./#andrsearchoptions) | Optional additional options for the query. Mostly used for pagination. |

#### Response

```typescript
export interface QueryCW721OffersAllOffersResponse {
  allOffers: NFTOffer[];
}
```

| Name        | Type        | Description                                                                  |
| ----------- | ----------- | ---------------------------------------------------------------------------- |
| `allOffers` | NFTOffer\[] | A vector containing information on each of the offers done by the purchaser. |

#### NFTOffer

```typescript
export interface NFTOffer {
  denom: string;
  expiration: Expiry;
  offerAmount: number;
  purchaser: string;
  remainingAmount: number;
  taxAmount: number;
}
```

| Name              | Type   | Description                                                                           |
| ----------------- | ------ | ------------------------------------------------------------------------------------- |
| `denom`           | string | The denomination of the coin being offered.                                           |
| `expiration`      | Expiry | Expiration for the offer.                                                             |
| `offerAmount`     | number | The amount of coins offered.                                                          |
| `purchaser`       | string | The address that has placed the offer.                                                |
| `remainingAmount` | string | The amount left after any royalties or taxes have been applied to the `offer_amount`. |
| `taxAmount`       | number | The amount of coins taken as tax.                                                     |

### queryOffer

Returns the latest offer of the token with the given `token_id`.

{% hint style="info" %}
Contract query can be found [here](../../../smart-contracts/modules/nft-offers.md#offer).
{% endhint %}

```typescript
export async function queryOffer(
  contractAddress: string,
  tokenId: string
): Promise<NFTOffer> {
  const resp = await query<
    QueryCW721OffersOffer,
    QueryCW721OffersOfferResponse
  >(QUERY_CW721_OFFERS_OFFER, { contractAddress, tokenId });

  return resp.offer;
}
```

| Name              | Type   | Description                                                 |
| ----------------- | ------ | ----------------------------------------------------------- |
| `contractAddress` | string | The contract address of the cw721 offers contract to query. |
| `tokenId`         | string | The Id of the token/NFT.                                    |

#### Response

```typescript
export interface QueryCW721OffersOfferResponse {
  offer: NFTOffer;
}
```

| Name    | Type                                 | Description                      |
| ------- | ------------------------------------ | -------------------------------- |
| `offer` | [NFTOffer](cw721-offers.md#nftoffer) | Information on the offer placed. |

\
