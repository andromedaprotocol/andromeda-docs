# GraphQL Queries

You can use Andromeda.js to perform queries on a GraphQL server that we have set up.

### GraphQL URI

This is the current URI for our GraphQL server:

```typescript
let uri: string = "https://andr-graphql.herokuapp.com/graphql";
```

### query

Function to query the GraphQL server.

```typescript
export async function query<Input, Output>(
  document: RequestDocument,
  variables: Input
) {
  return await request<Output>(uri, document, variables);
}
```

| Name        | Type            | Description                  |
| ----------- | --------------- | ---------------------------- |
| `document`  | RequestDocument | The query document.          |
| `variables` | Input           | The variables for the query. |

### setGQLUri

Sets the GQL Server URI.

```typescript
export function setGQLUri(newUri: string) {
  uri = newUri;
}
```

| Name  | Type   | Description             |
| ----- | ------ | ----------------------- |
| `uri` | string | The new GQL Server URI. |
