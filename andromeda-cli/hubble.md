---
description: >-
  The Hubble service is a GraphQL server custom built by Andromeda to query from
  our Apps.
---

# Hubble

### Available Commands

| Command  | Description                  |
| -------- | ---------------------------- |
| **app**  | Queries details about an app |

## App

Queries details about an app.

#### Usage

```
hubble app <contract address>
```

#### Example

```
hubble app juno1jcuuvppz5dndm37q6rsyxyln7x02dvcljwxn3nx4lkfw9u669h0qj9gdms
```

#### Response

The response includes the owner address, app name, and components of the app.

```
 Searching the Cosmos...
Owner: juno197y44w58djquqkpu8v56pgwvm058f6jswm0jec
App Name: Auction App

Components
auction auction juno1hmen67uf2ap7z4vjm3wygkzv0nspn3vpd3jcsrl0y6quxrl7w3tqu3avt3
tokens  cw721   juno1v06demasstn9td266rf3du2dt9frcwxexsnan77x0nnamy0laeuqsqjz5w
```
