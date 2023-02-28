# Create a DID

Follow these instructions to create a new DID and publish the associated DIDDoc on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md) for Veramo CLI

## Instructions

### 1. Prepare identity keys

For all of the following steps, you'll need identity keys to digitally sign the transaction. There are two ways to do this:

#### Using standalone keys

This will generate you a public/private key pair which you can use to sign your DIDDoc `payload.json` file with.

Follow [_Method 1_ in the key management guide](identity-key-handling.md)

```bash
veramo execute -m cheqdGenerateIdentityKeys
```

#### Create keys along with a DIDDoc template

This will automatically generate a template for your DIDDoc, including a new DID and associated public/private key pair. You can then use this template to populate your `payload.json` file.

Follow [_Method 2_ in the key management guide](identity-key-handling.md):

```bash
veramo execute -m cheqdGenerateDidDoc --argsJSON '{"verificationMethod": "Ed25519VerificationKey2018", "methodSpecificIdAlgo": "uuid", "network": "testnet"}'
```

#### Arguments

* `verificationMethod`
  * Ed25519VerificationKey2020
  * JsonWebKey2020
  * Ed25519VerificationKey2018

* `methodSpecificIdAlgo`
  * base58btc
  * uuid

* `network`
  * mainnet
  * testnet


After running the above command, if you see an error below, follow our troubleshooting guide [here](did-operations-troubleshooting.md#1-when-generating-keys-along-with-a-diddoc-template) to fix it.

```bash
Unexpected token v in JSON at position 1
```

### 2. Prepare/edit DIDDoc contents

Before creating a DID, you will need to prepare the `payload.json` file. This file can be saved where ever you choose, but the location must be specified in the create DID command used in Step 3. (By default, it will be saved under the project root directory.)

If you used _Method 2_ in the previous step, you can use the output of that as an initial starting template for your `payload.json` file.

#### Example of an `payload.json` file

````json
```json
{
  "kms": "local",
  "alias": "Veramo specific name of your DIDDoc",
  "document": {
    "context": [],
    "id": "did:cheqd:testnet:<uniqueId>",
    "controller": [
      "did:cheqd:testnet:<uniqueId>"
    ],
    "verificationMethod": [{
        "id": "did:cheqd:testnet:<uniqueId>#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:<uniqueId>",
        "publicKeyMultibase": "<uniqueKeyMultibase>",
        "publicKeyJwk": [],
        "publicKeyBase58": "<uniqueKeyBase58>"      
    }],
    "authentication": [
      "did:cheqd:testnet:<uniqueId>#key-1"
    ],
    "assertionMethod": [],
    "capabilityInvocation": [],
    "capabilityDelegation": [],
    "keyAgreement": [],
    "alsoKnownAs": [],
    "service": []
  },
  "keys": [{
    "publicKeyHex": "<public_key_in_hex_encoding>",
    "privateKeyHex": "<private_key_in_hex_encoding>",
    "kid": "<equal_to_public_key_hex>",
    "type": "Ed25519"
    // add additional key(s) if required
  }]
}
````

#### Parameters

* `kms` (default `local`): Key Management System (KMS) to be used for storage.
* `alias`: A human-friendly alias for the DID. Only used locally when referencing operations in Veramo CLI.
* `document`: Full body of the DID Document
* `keys`: Keys used to sign the DIDDoc. These must match the ones specified in the DIDDoc, otherwise an error will be thrown.
* `versionId` (optional): Custom versionId for the DID Document
* `fee` (optional): [Custom fee](../custom-fee.md)

### 3. Create new DID and publish DIDDoc

```bash
veramo execute -m cheqdCreateIdentifier --argsFile path/to/payload.json
```

If you do not specify the `--argsFile` in the previous step, you can also paste a JSON inline argument object by using the `--argsJSON` flag followed by the JSON payload.

## Next steps

If your transaction is successful, you'll receive a success message along with the transaction details.

### Troubleshooting

1. Bear in mind that the that transaction fees are paid by the cheqd/Cosmos account set in the `agent.yml` configuration file. If that account has insufficient balance your transaction might fail.&#x20;
2. If you are using testnet, you can top-up your testnet balance using our [faucet](http://testnet-faucet.cheqd.io/).
3. If you are using mainnet, you can [purchase CHEQ tokens here](https://cheqd.io/buy).
4. Check out our [troubleshooting guide for Creating DID](did-operations-troubleshooting.md) to see common errors and fixes.
