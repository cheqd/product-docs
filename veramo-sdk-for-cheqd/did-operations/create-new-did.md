# Creating a new DID using Veramo CLI

Follow these instructions to create a new DID and publish the associated DIDDoc on cheqd ledger.

## Before you begin

* Make sure you've [done the pre-requisite setup](README.md)

## Creating a new DID on cheqd ledger

### 1. Prepare the DID & DIDDoc contents

Before creating a DID, you will need to prepare the `args.json` file.

This file can be saved whereever you choose, but the location must be specified in the create DID command used in Step 2 (by default it will be saved under the project root directory).

To do this, see the example `args.json` file below, ensuring you specify:

* `kms`
* `alias`
* `document`
* `keys`

> Note: `keys` must match those specified in the DIDDoc used, otherwise an error will be thrown.

```jsonc
{
    "kms": "local",
    "alias": "veramo-specific-alias-refers-to-did",
    "document": {}, // DIDDoc
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

### 2. Generate a unique identifier for the DID

```bash
veramo execute -m cheqdCreateIdentifier --argsFile ./args.json
```

If you do not specify the --argsFile in the previous step, you can simple paste a JSON inline argument object by using the `--argsJSON`  flag, followed by the JSON payload.
