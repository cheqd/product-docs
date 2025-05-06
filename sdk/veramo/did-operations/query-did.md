---
description: Query a Decentralized Identifier (DID) on cheqd using Veramo.
---

# Querying a DID

Follow these instructions to query a DID from cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup/) for Veramo CLI

## Instructions

### 1. Invoke Veramo CLI's DID resolver function

Use `veramo did resolve <did>` to resolve a DID. For example:

```bash
veramo did resolve did:cheqd:mainnet:PdMMR9Jb4vgKkGboB7NFLC
```

_TIp_: If you have followed along the process of creating a _did_. You can put your _did_ instead.

```bash
veramo did resolve did:cheqd:testnet:<your_did_>
```

### 2. Inspect DID/DIDDoc output

The output should look like the following:

```json
{
  "didResolutionMetadata": {
    "contentType": "application/did+json",
    "retrieved": "2022-07-28T15:05:06Z",
    "did": {
      "didString": "did:cheqd:mainnet:PdMMR9Jb4vgKkGboB7NFLC",
      "methodSpecificId": "PdMMR9Jb4vgKkGboB7NFLC",
      "method": "cheqd"
    }
  },
  "didDocument": {
    "id": "did:cheqd:mainnet:PdMMR9Jb4vgKkGboB7NFLC",
    "verificationMethod": [
      {
        "id": "did:cheqd:mainnet:PdMMR9Jb4vgKkGboB7NFLC#key1",
        "type": "JsonWebKey2020"
        "controller": "did:cheqd:mainnet:PdMMR9Jb4vgKkGboB7NFLC",
        "publicKeyJwk": {
          "crv": "Ed25519",
          "kty": "OKP",
          "x": "jaa70K9yy4Tw-YEsA2T4F10jsQuFdpVJN9LLhjmOUGw"
        },
    "authentication": [
      "did:cheqd:mainnet:PdMMR9Jb4vgKkGboB7NFLC#key1"
        ],
      }
    ]
  },
  "didDocumentMetadata": {
    "created": "2022-05-13T14:01:20Z",
    "versionId": "83fe6fa4-c844-438f-861a-cbd91202864f"
  }
}
```

## Next steps

DID queries are passed and handled using [the cheqd DID Resolver](../../../advanced/did-resolver/). You can also check out the API endpoints exposed by the DID Resolver to understand how this can be fetched/consumed outside Veramo CLI.
