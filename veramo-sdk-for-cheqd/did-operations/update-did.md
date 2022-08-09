# Update an existing cheqd DID using Veramo CLI

Follow these instructions to update an existing `did:cheqd` entry on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup-cli.md) for Veramo CLI

## Instructions

### 1. Fetch the latest version of the DIDDoc from the ledger

Use `veramo did resolve <did>` (as explained in the [*Querying a DID* article](query-did.md)) to resolve a DID and its latest state from the ledger.

This is recommended as a best-practice, since the DIDDoc could have been changed during a previous update operation by other controllers, or from a different machine.

If you're *absolutely* sure that the DIDDoc version stored locally is up-to-date, you can view it using `veramo did list <alias>`

### 2. Prepare DIDDoc contents

Before updating the DID, you will need to prepare the updated DIDDoc and parameters for the transaction in an `args.json` file.

This file can be saved whereever you choose, but the location must be specified in the create DID command used in Step 2. (By default, it will be saved under the project root directory.)

#### Parameters

* `kms` (default `local`): Key Management System (KMS) to be used for storage.
* `alias`: A human-friendly alias for the DID. Only used locally when referencing operations in Veramo CLI.
* `document`: Full body of the DID Document *including* updated sections.
* `keys`: Keys used to sign the DIDDoc. These must match the ones specified in the DIDDoc, otherwise an error will be thrown.

In addition to the changes made to the DIDDoc itself, you must also add the `versionId` in the DIDDoc `document` field. The [`versionId` is the same as the Tendermint transaction hash of the latest version of the DIDDoc](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method), e.g., `EF072CC45F2839652A60BF392BAB4D1913A9D2728CCAFD32AF7384DBD13F7FE5`.

### 3. Update existing DID

Use the command below to construct and broadcast update transaction.

```bash
veramo execute -m cheqdUpdateIdentifier --argsFile path/to/args.json
```

If you would like to fetch the updated DIDDoc body following the successful DID update result, execute the `resolve` command as outlined in Step 1.

## Next steps

DID update operations can be complicated, especially when changing or updating keys, using multiple keys etc. Understand the [design of the cheqd DID method](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method) and [*Verification Relationships* in the W3C DID Core specification](https://w3c.github.io/did-core/#verification-relationships) to understand the logic that is applied in these scenarios.
