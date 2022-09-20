# 🆔 Create a DID

Follow these instructions to create a new DID and publish the associated DIDDoc on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup-cli.md) for Veramo CLI

## Instructions

### 1. Prepare identity keys

For all of the following steps, you'll need identity keys to digitally sign the transaction. There are two ways to do this:

#### Using standalone keys

Follow [_Method 1_ in the key management guide](identity-key-handling.md)

```bash
veramo execute -m cheqdGenerateIdentityKeys
```

#### Create keys along with a DIDDoc template

Follow [_Method 2_ in the key management guide](identity-key-handling.md):

```bash
veramo execute -m cheqdGenerateDidDoc --argsJSON '{"verificationMethod": "Ed25519VerificationKey2020", "methodSpecificIdAlgo": "base58btc", "methodSpecificIdLength": 16, "network": "testnet"}'
```

### 2. Prepare/edit DIDDoc contents

Before creating a DID, you will need to prepare the `args.json` file. This file can be saved whereever you choose, but the location must be specified in the create DID command used in Step 2. (By default, it will be saved under the project root directory.)

If you used _Method 2_ in the previous step, you can use the output of that as a starting point for this file.

#### Example file

```json
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

- `kms` (default `local`): Key Management System (KMS) to be used for storage.
- `alias`: A human-friendly alias for the DID. Only used locally when referencing operations in Veramo CLI.
- `document`: Full body of the DID Document
- `keys`: Keys used to sign the DIDDoc. These must match the ones specified in the DIDDoc, otherwise an error will be thrown.

### 3. Create new DID and publish DIDDoc

```bash
veramo execute -m cheqdCreateIdentifier --argsFile path/to/args.json
```

If you do not specify the `--argsFile` in the previous step, you can also paste a JSON inline argument object by using the `--argsJSON` flag followed by the JSON payload.

## Next steps

If your transaction is successful, you'll receive a success message along with the transaction details.

### Troubleshooting

1. Bear in mind that the that transaction fees are paid by the cheqd/Cosmos account set in the agent configuration file. If that account has insufficient balance (a typical create DID transaction would be \~500k ncheq), your transaction might fail. You can get your self some fake tokens here from our [faucet](http://testnet-faucet.cheqd.io/).

2. Check out our [troubleshooting guide for Creating DID](did-operations-troubleshooting.md) to see common errors and fixes.
