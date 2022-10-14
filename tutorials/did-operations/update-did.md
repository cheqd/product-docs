# Update an existing DID

Follow these instructions to update an existing `did:cheqd` entry on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md) for Veramo CLI

## Instructions

### 1. Fetch the latest version of the DIDDoc from the ledger

Use `veramo did resolve <did>` (as explained in the [_Querying a DID_ article](query-did.md)) to resolve a DID and its latest state from the ledger.

This is recommended as a best-practice, since the DIDDoc could have been changed during a previous update operation by other controllers, or from a different machine.

If you're _absolutely_ sure that the DIDDoc version stored locally is up-to-date, you can view it using `veramo did list <alias>`

### 2. Prepare DIDDoc contents

Before updating the DID, you will need to prepare the updated DIDDoc and parameters for the transaction in an `args.json` file.

This file can be saved whereever you choose, but the location must be specified in the create DID command used in Step 2. (By default, it will be saved under the project root directory.)

#### Parameters

* `kms` (default `local`): Key Management System (KMS) to be used for storage.
* `did`: The `did` that resolves to your `DIDDoc`.
* `alias`: A human-friendly alias for the DID. Only used locally when referencing operations in Veramo CLI.
* `document`: Full body of the DID Document _including_ updated sections.
* `keys`: Keys used to sign the DIDDoc. These must match the ones specified in the DIDDoc, otherwise an error will be thrown.

In addition to the changes made to the DIDDoc itself, you must also add the `versionId` in the DIDDoc `document` field. The [`versionId` is the same as the Tendermint transaction hash of the latest version of the DIDDoc](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method), e.g., `EF072CC45F2839652A60BF392BAB4D1913A9D2728CCAFD32AF7384DBD13F7FE5`.

### 3. Update existing DID

Use the command below to construct and broadcast update transaction.

So, let's try to update `service` section of our `DIDDoc`. Then, your `args.json` file will look like this.

First resolve your `did` and copy the `versionId` and update it in `args.json` file.

```json
{
  "kms": "local",
  "alias": "update-my-did",
  "did": "did:cheqd:testnet:zVJe7ZNvCtbK83pv",
  "document": {
    "context": [],
    "id": "headdid:cheqd:testnet:zVJe7ZNvCtbK83pv",
    "controller": ["did:cheqd:testnet:zVJe7ZNvCtbK83pv"],
    "authentication": ["did:cheqd:testnet:zVJe7ZNvCtbK83pv#key-1"],
    "assertionMethod": [],
    "capabilityInvocation": [],
    "capabilityDelegation": [],
    "keyAgreement": [],
    "alsoKnownAs": [],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:zVJe7ZNvCtbK83pv#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:zVJe7ZNvCtbK83pv",
        "publicKeyMultibase": "zVJe7ZNvCtbK83pvJYD9eRNc5CSrNBKkyjep6gYdaWub",
        "publicKeyJwk": []
      }
    ],
    "service": [
      {
        "id": "did:cheqd:testnet:zVJe7ZNvCtbK83pv#linked-domain",
        "type": "LinkedDomains",
        "serviceEndpoint": "https://cheqd.io/"
      }
    ],
    "versionId": "5C56B973A9D568E67E2AE1F77CFE990F6A4135134282F48AA3E92DA1A20D72C8"
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

Notice, that we are updating a `service` section of in our `DIDDoc`.

Then try running the command below to update the `did`:

```bash
veramo execute -m cheqdUpdateIdentifier --argsFile path/to/args.json
```

If you would like to fetch the updated DIDDoc body following the successful DID update result, execute the `resolve` command as outlined in Step 1. You can check if `service` section of `DIDDoc` has been updated.

## Next steps

DID update operations can be complicated, especially when changing or updating keys, using multiple keys etc. Understand the [design of the cheqd DID method](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method) and [_Verification Relationships_ in the W3C DID Core specification](https://w3c.github.io/did-core/#verification-relationships) to understand the logic that is applied in these scenarios.
