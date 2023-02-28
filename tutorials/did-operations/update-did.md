# Update an existing DID

Follow these instructions to update an existing `did:cheqd` entry on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md) for Veramo CLI

## Instructions

### 1. Prepare DIDDoc contents

Before updating the DID, you will need to prepare the updated DIDDoc and parameters for the transaction in an `payload.json` file.

This file can be saved whereever you choose, but the location must be specified in the [create DID command used in Step 2](create-a-did.md). (By default, it will be saved under the project root directory.)

#### Parameters

* `kms` (default `local`): Key Management System (KMS) to be used for storage.
* `did`: The `did` that resolves to your `DIDDoc`.
* `alias`: A human-friendly alias for the DID. Only used locally when referencing operations in Veramo CLI.
* `document`: Full body of the DID Document _including_ updated sections.
* `keys`: Keys used to sign the DIDDoc. These must match the ones specified in the DIDDoc, otherwise an error will be thrown.
* `versionId`: (optional) Custom versionId for the DID Document. If this is not set manually, then a UUID will be automatically generated for the DID Document version.
* `fee` (optional): [Custom fee](../custom-fee.md)

> Note that transaction fees are paid by the cheqd account set in the `agent.yml` configuration file, [setup here](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md). Each of cheqd's on-ledger identity transactions has a **fixed fee,** [the pricing for cheqd DIDs and DID-Linked Resources can be found here](https://docs.cheqd.io/node/architecture/adr-list/adr-005-genesis-parameters#cheqd-module-did-module). If your account has insufficient balance the transaction will fail.

### 2. Update existing DID

Use the command below to construct and broadcast update transaction.

So, let's try to update `service` section of our `DIDDoc`. Then, your `payload.json` file will look like this.

```json
{
  "kms": "local",
  "alias": "update-my-did",
  "did": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
  "document": {
    "context": [],
    "id": "headdid:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
    "controller": ["did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590"],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
        "publicKeyMultibase": "3e6bd814-6851-4c8a-b114-c64f035ef590JYD9eRNc5CSrNBKkyjep6gYdaWub"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590#key-1"
    ],
    "service": [
      {
        "id": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590#linked-domain",
        "type": "LinkedDomains",
        "serviceEndpoint": ["https://cheqd.io/"]
      }
    ]
    "assertionMethod": [],
    "capabilityInvocation": [],
    "capabilityDelegation": [],
    "keyAgreement": [],
    "alsoKnownAs": [],
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

Note, that we are updating a `service` section of in our `DIDDoc`.

After you have updated the `payload.json` file, run the command below to update the `did`:

```bash
veramo execute -m cheqdUpdateIdentifier --argsFile path/to/payload.json
```

If you would like to fetch the updated DIDDoc body following the successful DID update result, execute the `resolve` command as outlined [here](query-did.md). You can check if `service` section of `DIDDoc` has been updated.

## Next steps

DID update operations can be complicated, especially when changing or updating keys, using multiple keys etc. Understand the [design of the cheqd DID method](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method) and [_Verification Relationships_ in the W3C DID Core specification](https://w3c.github.io/did-core/#verification-relationships) to understand the logic that is applied in these scenarios.
