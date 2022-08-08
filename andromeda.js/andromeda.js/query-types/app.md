---
description: The GraphQL queries that can be performed on the App ADO.
---

# App

### queryApp

Queries details about an App given its contract address.

{% hint style="info" %}
You can find the contract queries [here](../../../smart-contracts/andromeda-apps/app.md#querymsg).
{% endhint %}

```typescript
export async function queryApp(contractAddress: string) {
  return query<QueryApp, QueryAppResponse>(QUERY_APP, { contractAddress });
}
```

| Name              | Type   | Description                    |
| ----------------- | ------ | ------------------------------ |
| `contractAddress` | string | The contract address to query. |

#### Response

```typescript
export interface QueryAppResponse {
  app: {
    getAddresses: {
      address: string;
      name: string;
    }[];
    getComponents: {
      name: string;
      ado_type: string;
    }[];
    config: {
      name: string;
      owner: string;
    };
  };
}
```

{% tabs %}
{% tab title="getAddresses" %}
| Name      | Type   | Description                             |
| --------- | ------ | --------------------------------------- |
| `address` | string | The address of the contract in the app. |
| `name`    | string | The name of the contract in the app.    |
{% endtab %}

{% tab title="getComponents" %}
| Name       | Type   | Description                     |
| ---------- | ------ | ------------------------------- |
| `name`     | string | The name of the component.      |
| `ado_type` | string | The ado\_type of the component. |
{% endtab %}

{% tab title="config" %}
| Name    | Type   | Description               |
| ------- | ------ | ------------------------- |
| `name`  | string | Name of the App.          |
| `owner` | string | Owner address of the App. |
{% endtab %}
{% endtabs %}
