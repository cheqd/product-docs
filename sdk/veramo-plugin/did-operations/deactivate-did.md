# Deactivate a DID

Follow these instructions to update an existing `did:cheqd` entry on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup/) for Veramo CLI

## Instructions

### Deactivate a DID

Construct your `payload.json` file like this:

```json
{
  "kms": "local",
  "alias": "deactivate-my-did",
  "document": {
    "id": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
        "publicKeyMultibase": "z2yJuNbhoUpRn7ypAugSLzkCc8QEw146RJ8DD3jzCZQ6A"
      }
    ]
  },
  "versionId": "<uuid>", // optional
  "keys": [
    {
      "publicKeyHex": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "privateKeyHex": "84ecb7d289dd5c5e82071f8a16503ba5d7b5b3174a619186f430918a6ab00e3b074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "kid": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "type": "Ed25519"
    }
  ],
  "fee": {
    "amount": [{
      "denom": "ncheq",
      "amount": "10000000000"
      }],
    "gas": "400000",
    "payer": "cheqd1rnr5jrt4exl0samwj0yegv99jeskl0hsxmcz96"
  }
}
```

Where importantly:

* `denom`: ncheq (smallest denomination classification)
* `amount`: **10000000000** (This is **10 CHEQ by default** and will not work with a different value)

Then try running the command below to deactivate the `did`:

```bash
veramo execute -m cheqdDeactivateIdentifier --argsFile path/to/payload.json
```

If you would like to fetch the deactivated DIDDoc, execute the `resolve` command as outlined [here](query-did.md). You can check if `didDocumentMetadata` has the `deactivated` flag set to true.
