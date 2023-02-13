# Deactivate a DID

Follow these instructions to update an existing `did:cheqd` entry on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md) for Veramo CLI

## Instructions

### Deactivate a DID

Construct your `args.json` file like this

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
        "publicKeyMultibase": "3e6bd814-6851-4c8a-b114-c64f035ef590JYD9eRNc5CSrNBKkyjep6gYdaWub"
      }
    ]
  },
  "keys": [
    {
      "publicKeyHex": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "privateKeyHex": "84ecb7d289dd5c5e82071f8a16503ba5d7b5b3174a619186f430918a6ab00e3b074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "kid": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "type": "Ed25519"
    }
  ]
}
```

Then try running the command below to deactivate the `did`:

```bash
veramo execute -m cheqdDeactivateIdentifier --argsFile path/to/args.json
```

If you would like to fetch the deactivated DIDDoc, execute the `resolve` command as outlined [here](./query-did.md). You can check if `didDocumentMetadata` has the `deactivated` flag set to true.
