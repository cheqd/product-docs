# Querying and resolving a DID URL

## Tutorial for querying and resolving a DID URL

### Overview

This tutorial offers step-by-step guidance on how to query and resolve a DID URL.

This can be achieved through both the Veramo CLI, or directly through an application, setup to read and write to the cheqd ledger, using the Veramo SDK for cheqd.

### Pre-requisites

For full information on the architecture, setup and cofig, check [`did-provider-cheqd`]().

### Steps

Use `veramo did resolve` to resolve did

e.g.:

```bash
veramo did resolve did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX
```

The output should look like the following:

```jsonc
{
  "didDocumentMetadata": {
    "created": "2022-05-13T14:01:20Z",
    "versionId": "B55E82F3022593C2A55D08484D658003B1685177197796FA00B56440671DB842"
  },
  "didResolutionMetadata": {
    "contentType": "application/did+json",
    "retrieved": "2022-07-28T15:05:06Z",
    "did": {
      "didString": "did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX",
      "methodSpecificId": "zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX",
      "method": "cheqd"
    }
  },
  "didDocument": {
    "authentication": [
      "did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX#key1"
    ],
    "id": "did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX",
    "verificationMethod": [
      {
        "controller": "did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX",
        "id": "did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX#key1",
        "publicKeyJwk": {
          "crv": "Ed25519",
          "kty": "OKP",
          "x": "jaa70K9yy4Tw-YEsA2T4F10jsQuFdpVJN9LLhjmOUGw"
        },
        "type": "JsonWebKey2020"
      }
    ]
  }
}
