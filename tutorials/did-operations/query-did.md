# Querying a DID

Follow these instructions to query a DID from cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md) for Veramo CLI

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
        "controller": "did:cheqd:mainnet:PdMMR9Jb4vgKkGboB7NFLC",
        "id": "did:cheqd:mainnet:PdMMR9Jb4vgKkGboB7NFLC#key1",
        "publicKeyJwk": {
          "crv": "Ed25519",
          "kty": "OKP",
          "x": "jaa70K9yy4Tw-YEsA2T4F10jsQuFdpVJN9LLhjmOUGw"
        },
        "type": "JsonWebKey2020"
    "authentication": [
      "did:cheqd:mainnet:PdMMR9Jb4vgKkGboB7NFLC#key1"
        ],
      }
    ]
  },
    "didDocumentMetadata": {
    "created": "2022-05-13T14:01:20Z",
    "versionId": "B55E82F3022593C2A55D08484D658003B1685177197796FA00B56440671DB842"
  }
}
```

## Next steps

DID queries are passed and handled using the [cheqd DID Resolver](../../guides/did-resolver.md). You can also check out the API endpoints exposed by the DID Resolver to understand how this can be fetched/consumed outside Veramo CLI.
