## Instantiating an existing contract
**Please note**: this guide assumes you are using `CLI`, have a key added to your keychain, have some tokens to cover your fees and you know the contract ID. You you do not know the contract ID, please refer to the section on `Uploading a contract`

Here is a complete command:

`andromedad tx wasm instantiate 8 '{}' --amount 100000uandr --label "some counter contract" --admin andr1ljaxfttvhr7zhzr2eu5s46e3qlpe6tkhyjn023 --from testing --chain-id galileo-2 --fees 250uandr`

Now lets break it down to its core components and what it does:
1) `andromedad tx wasm instantiate 8` - here we are telling the binary to instantiate (create a copy) of a contract with an id of 8
2) `'{}'` - Data to be sent to the contract upon its creation. If this contract allowed user (or had an option) to set initial state (data) this is the json that you would need to pass to the contract.
3) `--amount 100000uandr` - Amount of tokens (if any) to be sent to the contract. _This is optional_.
4) `--label "some counter contract"` - Self explanatory. A name for the contract if you will.
5) `--admin andr1ljaxfttvhr7zhzr2eu5s46e3qlpe6tkhyjn023` - Who is the contract admin. This field is optional.
6) `--from testing` - Key name from the local keychain to use to sign the transaction
7) `-chain-id galileo-2 --fees 250uandr` - The chain ID and the fee amount we have to pay to transact with the blockchain.

After executing the command, you will be prompted to sign the transaction:

```
{"body":{"messages":[{"@type":"/cosmwasm.wasm.v1.MsgInstantiateContract","sender":"andr1ljaxfttvhr7zhzr2eu5s46e3qlpe6tkhyjn023","admin":"andr1ljaxfttvhr7zhzr2eu5s46e3qlpe6tkhyjn023","code_id":"8","label":"some counter","msg":{},"funds":[{"denom":"uandr","amount":"100000"}]}],"memo":"","timeout_height":"0","extension_options":[],"non_critical_extension_options":[]},"auth_info":{"signer_infos":[],"fee":{"amount":[{"denom":"uandr","amount":"250"}],"gas_limit":"200000","payer":"","granter":""}},"signatures":[]}

confirm transaction before signing and broadcasting [y/N]:
```
_You can sign the transaction and bypass the prompt with the simple `--yes` flag_.

After confirming your choice, you will be given transaction details

```
code: 0
codespace: ""
data: ""
events: []
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: '[]'
timestamp: ""
tx: null
txhash: 911DDFEF9F6CE4E4765A43D72107AD3330530FBD6D49B9B9F2946C9046A1DF1B
```

Use block explorer to find the contract address or view the results.
