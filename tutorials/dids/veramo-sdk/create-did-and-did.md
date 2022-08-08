# Creating a DID + DID Doc with the Veramo SDK for cheqd

## Overview

This tutorial offers step-by-step guidance for creating a DID + DIDDoc with the Veramo SDK for cheqd. This can be achieved through both the Veramo CLI, or directly through an application, setup to read and write to the cheqd ledger, using the Veramo SDK for cheqd.

For full information on the architecture, setup and cofig, check did-provider-cheqd.

## Pre-requisites

For full information on the architecture, setup and cofig, check [`did-provider-cheqd`](https://github.com/cheqd/did-provider-cheqd).

>Note: Ensure you have saved the [`agent.yml`](https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml) file in your local project directory.

## Steps

### Step 1

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

### Step 2

Create a cheqd DID (Identifier) using:

```bash
veramo execute -m cheqdCreateIdentifier --argsFile ./args.json
```

If you do not specify the --argsFile in the previous step, you can simple paste a JSON inline argument object by using the `--argsJSON`  flag, followed by the JSON payload.
