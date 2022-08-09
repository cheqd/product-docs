# Update an existing cheqd DID using Veramo CLI

Follow these instructions to update an existing `did:cheqd` entry on ledger using the Veramo CLI.

## Before you begin

* Make sure you've [done the pre-requisite setup](README.md)

## Steps

### 1. Fetch current state of did doc

Fetch a did from the list stored in local KMS

> Use `veramo did list` to fetch the DID from your DID list. If you already have the DID URL there is no need to do this.

Use `veramo did resolve` to resolve did and get the latest version of the did document.

e.g.:

```bash
veramo did resolve did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX
```

### 2. Prepare the updated DID + DIDDoc contents

Before updating a DID, you will need to prepare the updated `args.json` file.

This file can be saved whereever you choose, but the location must be specified in the update DID command used in the later step (by default it will be saved under the project root directory).

To do this, see the example `args.json` file below, ensuring you specify:

* `kms`
* `did`
* `document`
* `keys`

In addition to the changes made to the DIDDoc itself, you must also add the `versionId` in the DIDDoc `document` field.

The `versionId` refers to the transaction hash that you need to fetch the DID version from (this should be in the Tendermint TX hash format).

e.g. `EF072CC45F2839652A60BF392BAB4D1913A9D2728CCAFD32AF7384DBD13F7FE5`.

> Note: `keys` must match those specified in the DIDDoc used, otherwise an error will be thrown.

```jsonc
{
    "kms": "local",
    "did": "`<didURL>",
    "document": {}, // updated DIDDoc
    "keys": [
        {
            "publicKeyHex": "<public_key_in_hex_encoding>",
            "privateKeyHex": "<private_key_in_hex_encoding>",
            "kid": "<equal_to_public_key_hex>",
            "type": "Ed25519"
        },
        {
            // add additional key if required
        }
    ]
}
```

### 3. Update existing DID

Use the command below to construct and broadcast update transaction.

```bash
veramo execute -m cheqdUpdateIdentifier --argsFile ./args.json
```

If you would like to fetch the updated DIDDoc body following the successful DID update result, follow the DID resolution steps (detailed in step 1).
