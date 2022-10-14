# 📚 Updating existing DIDs with cheqd Cosmos CLI

The purpose of this document is to describe how an existing DID (and associated DIDDoc) can be _updated_ using [the `cheqd-node` Cosmos CLI](https://docs.cheqd.io/node/docs/cheqd-cli).

> **NOTE: The procedures below are only recommended for development purposes!**
>
> Using `cheqd-node` Cosmos CLI for real-world production usage is not recommended, since the identity keys are passed in raw form to the CLI. This is fine in development/testing usage, but is not recommend for mainnet.
>
> Developers are encouraged to use [production-grade CLI tools such as Veramo SDK for cheqd](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/) or look at [our developer guide on how to integrate custom applications](../developer-guide.md).

## Setup

### Pre-Requisites

* Learn how to [create a DID with cheqd Cosmos CLI](cheqd-cosmos-cli.md)
* [Query an existing DID/DIDDoc](query-did-and-did-document.md) with cheqd Cosmos CLI

### Assumptions

* Given this procedure below is intended for development/training purposes, we assume that the user stores their public/private keys safely during the DID creation process and has access to it.
* Updating a DIDDoc requires the _full_ updated DIDDoc to be sent. Only the updated/changed parts cannot be sent. This accounts for scenarios where the DIDDoc may have been updated on ledger, but the local copy is not synced with the latest changes.

## Update an existing DID

### 1. Update the DIDDoc contents / body

Use a text editor like `nano` to edit the body of the DIDDoc (the example below assumes it's saved in a file called `diddoc.json`).

```bash
nano diddoc.json
```

For example, we can [take the DIDDoc created previously](cheqd-cosmos-cli.md) and change the Service Endpoint from `bar.example.com` to `foo.example.com`:

```json
{
  "id": "did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe",
  "verification_method": [
    {
      "id": "did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe#key1",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe",
      "public_key_multibase": "z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe3Nmod35uua9TE"
    }
  ],
  "authentication": [
    "did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe#key1"
  ],
  "service": [{
    "id":"did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe#linked-domain",
    "type": "LinkedDomains",
    "service_endpoint": "https://foo.example.com"
  }]
}
```

### 2. Send an update DID transaction to the ledger

Send the updated DIDDoc to the ledger. This _must_ be signed with the correct identity keys:

```bash
cheqd-noded tx cheqd update-did <DIDDoc_in_JSON> <did_verification_method_id> <identity_private_key_BASE_64> \
  --from <cosmos_account> --node <url> --chain-id <chain> --fees <fee>
```

### Flags

* `did_verification_method_id`: Fully-qualified verification method key name, in `did:cheqd:<namespace>:<unique-id>#<key-alias>` format.
* `identity_private_key_BASE_64>`: Base64 encoded private key to sign the current identity message with. Must be a valid identity key.
* `--from`: Cosmos account key which will pay fees for the transaction to be written to ledger.
* `--node`: IP address or URL of node to send request to
* `--chain-id`: E.g., `cheqd-testnet-4`
* `--fees`: Set to 5000000ncheq

### Example

```bash
cheqd-noded tx cheqd update-did "$(cat diddoc.json)" "FxaJOy4HFoC2Enu1SizKtU0L+hmBRBAEpC+B4TopfQoyetOF5T68Ks3db5Yy9ykFdgEboPUes3m6wvXNLpbv+Q==" \
  --from my_account --node http://nodes.testnet.cheqd.network:26657 --chain-id cheqd-testnet-4 --fees 5000000ncheq
```
