# Deactivate existing DIDs with cheqd Cosmos CLI

The purpose of this document is to describe how an existing DID (and associated DIDDoc) can be _deactivated_ using [the `cheqd-node` Cosmos CLI](https://docs.cheqd.io/node/docs/cheqd-cli).

> **NOTE: The procedures below are only recommended for development purposes!**
>
> Using `cheqd-node` Cosmos CLI for real-world production usage is not recommended, since the identity keys are passed in raw form to the CLI. This is fine in development/testing usage, but is not recommend for mainnet.
>
> Developers are encouraged to use [production-grade CLI tools such as Veramo SDK for cheqd](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/) or look at [our developer guide on how to integrate custom applications](../developer-guide.md).

## Setup

### Pre-Requisites

* Learn how to [create a DID with cheqd Cosmos CLI](create-did.md)
* [Query an existing DID/DIDDoc](query-did-and-did-document.md) with cheqd Cosmos CLI

## Deactivate an existing DID

### 1. Compile the payload

As in flow with [creating DID-Document](create-did.md) we need to compile `payload.json` file with private key inside and pass it to the CLI.
The result JSON in our example will look liks:

```json
{
  "Payload": {
    "id": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77"
  },
  "SignInputs": [
    {
      "VerificationMethodID": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1",
      "PrivKey": "FxaJOy4HFoC2Enu1SizKtU0L+hmBRBAEpC+B4TopfQoyetOF5T68Ks3db5Yy9ykFdgEboPUes3m6wvXNLpbv+Q=="
    }
  ]
}
```

### 3. Send an deactivate DID transaction to the ledger

Send the updated DIDDoc to the ledger. This _must_ be signed with the correct identity keys:

```bash
cheqd-noded tx cheqd deactivate-did <Payload_in_JSON>  \
  --from <cosmos_account> --node <url> --chain-id <chain> --fees <fee>
```

### Flags

* `Payload_in_JSON` - previously compiled JSON.
* `--from`: Cosmos account key which will pay fees for the transaction to be written to ledger.
* `--node`: IP address or URL of node to send request to
* `--chain-id`: E.g., `cheqd-testnet-6`
* `--fees`: Set to 10000000000ncheq

> Note that each of cheqd's on-ledger identity transactions has a **fixed fee,** [the pricing for cheqd DIDs and DID-Linked Resources can be found here](https://docs.cheqd.io/node/architecture/adr-list/adr-005-genesis-parameters#cheqd-module-did-module). If your account has insufficient balance the transaction will fail.

### Example

```bash
cheqd-noded tx cheqd deactivate-did "payload.json" \
  --from my_account --node http://rpc.cheqd.network:443 --chain-id cheqd-testnet-6 --fees 10000000000ncheq
```
