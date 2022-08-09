# Creating a new DID using Veramo CLI

Follow these instructions to create a new DID and publish the associated DIDDoc on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup-cli.md) for Veramo CLI

## Instructions

### 1. Prepare the DID & DIDDoc contents

Before creating a DID, you will need to prepare the `args.json` file. This file can be saved whereever you choose, but the location must be specified in the create DID command used in Step 2. (By default, it will be saved under the project root directory.)

#### Example file

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
            // add additional key(s) if required
        }
    ]
}
```

#### Parameters

* `kms` (default `local`): Key Management System (KMS) to be used for storage.
* `alias`: A human-friendly alias for the DID. Only used locally when referencing operations in Veramo CLI.
* `document`: Full body of the DID Document
* `keys`: Keys used to sign the DIDDoc. These must match the ones specified in the DIDDoc, otherwise an error will be thrown.

### 2. Create a unique identifier for the DID

```bash
veramo execute -m cheqdCreateIdentifier --argsFile path/to/args.json
```

If you do not specify the `--argsFile` in the previous step, you can also paste a JSON inline argument object by using the `--argsJSON` flag followed by the JSON payload.

## Next steps

If your transaction is successful, you'll receive a success message along with the transaction details.

### Troubleshooting

1. Bear in mind that the that transaction fees are paid by the cheqd/Cosmos account set in the agent configuration file. If that account has insufficient balance (a typical create DID transaction would be ~500k ncheq), your transaction might fail.
