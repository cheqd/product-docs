# 📚 Updating existing DIDs with cheqd Cosmos CLI

The purpose of this document is to describe how an existing DID (and associated DIDDoc) can be _updated_ using [the `cheqd-node` Cosmos CLI](https://docs.cheqd.io/node/docs/cheqd-cli).

> **NOTE: The procedures below are only recommended for development purposes!**
>
> Using `cheqd-node` Cosmos CLI for real-world production usage is not recommended, since the identity keys are passed in raw form to the CLI. This is fine in development/testing usage, but is not recommend for mainnet.
>
> Developers are encouraged to use [production-grade CLI tools such as Veramo SDK for cheqd](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/) or look at [our developer guide on how to integrate custom applications](../developer-guide.md).

## Setup

### Pre-Requisites

* Learn how to [create a DID with cheqd Cosmos CLI](create-did.md)
* [Query an existing DID/DIDDoc](query-did-and-did-document.md) with cheqd Cosmos CLI

### Assumptions

* Given this procedure below is intended for development/training purposes, we assume that the user stores their public/private keys safely during the DID creation process and has access to it.
* Updating a DIDDoc requires the _full_ updated DIDDoc to be sent. Only the updated/changed parts cannot be sent. This accounts for scenarios where the DIDDoc may have been updated on ledger, but the local copy is not synced with the latest changes.

## Update an existing DID

### 1. Update the DIDDoc contents / body

Use a text editor like `nano` to edit the body of the DIDDoc (the example below assumes it's saved in a file called `payload.json`).

```bash
nano payload.json
```

For example, we can [take the DIDDoc created previously](create-did.md) and change the Service Endpoint from `bar.example.com` to `foo.example.com`:

```json
{
  "id": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
  "verificationMethod": [
    {
      "id": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
      "publicKeyMultibase": "z6MkjafyeUPvVJb9LDKtx7qwdZqFaVB8mnhqKVYf5yA9DxSa"
    }
  ],
  "authentication": [
    "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1"
  ],
  "service": [{
    "id":"did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#linked-domain",
    "type": "LinkedDomains",
    "serviceEndpoint": [
      "https://foo.example.com"
    ]
  }]
}
```

### 2. Compile the payload

As in flow with [creating DID-Document](create-did.md) we need to compile `payload.json` file with private key inside and pass it to the CLI.
The result JSON in our example will look liks:

```json
{
  "payload": {
    "id": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
        "publicKeyMultibase": "z6MkjafyeUPvVJb9LDKtx7qwdZqFaVB8mnhqKVYf5yA9DxSa"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1"
    ],
    "service": [{
      "id":"did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#linked-domain",
      "type": "LinkedDomains",
      "serviceEndpoint": [
        "https://foo.example.com"
      ]
    }]
  },
  "signInputs": [
    {
      "verificationMethodId": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1",
      "privKey": "wNXCJ9Ny0uzCYhnTE3gfQuwgQM4QZCw08+j01QDfoGxMMI9u9GIv/90eH3E3KjHjlSi9hKRQy94PvKVAH1+Rhw=="
    }
  ]
}
```

### 3. Send an update DID transaction to the ledger

Send the updated DIDDoc to the ledger. This _must_ be signed with the correct identity keys:

```bash
cheqd-noded tx cheqd update-did <Payload_in_JSON>  \
  --from <cosmos_account> --node <url> --chain-id <chain> --fees <fee>
```

### Flags

* `Payload_in_JSON` - previously compiled JSON.
* `--from`: Cosmos account key which will pay fees for the transaction to be written to ledger.
* `--node`: IP address or URL of node to send request to
* `--chain-id`: E.g., `cheqd-testnet-6`
* `--fees`: Set to 5000000ncheq

### Example

```bash
cheqd-noded tx cheqd update-did "payload.json" \
  --from my_account --node http://rpc.cheqd.network:443 --chain-id cheqd-testnet-6 --fees 5000000ncheq
```
