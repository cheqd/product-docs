# Managing identity keys

Follow these instructions to:

1. Generate [identity keys on-the-fly](identity-key-handling.md#method-1-generating-identity-keys-on-the-fly-with-diddocs) WITH a draft DIDDoc
2. Generate [standalone identity public/private keypairs](identity-key-handling.md#method-2-standalone-key-creation)
3. [Convert identity key pairs](identity-key-handling.md#converting-identity-keys) generated using Veramo agent to other key encoding formats

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md) for Veramo CLI

## Method 1: Generating identity keys on-the-fly with DIDDocs

Instead of creating identity keys standalone, you can generate them along with a DIDDoc template. This makes it far easier to handle different inputs and arguments, such as generating DID Docs with different key types.&#x20;

### Command

You can use the following command in your CLI to create your DIDDoc and associated identity keys:

```bash
veramo execute -m cheqdGenerateDidDoc --argsJSON '{"verificationMethod": "<exampleVerificationMethod>", "methodSpecificIdAlgo": "<exampleAlgo>", "network": "exampleNetwork"}'
```

### Inputs and arguments

Within this command, you are able to choose from the following inputs below to produce different types of DIDDocs:

<details>

<summary>verificationMethod</summary>

* Ed25519VerificationKey2020
* JsonWebKey2020
* Ed25519VerificationKey2018

</details>

<details>

<summary>methodSpecificIdAlgo</summary>

* base58btc
* uuid

</details>

<details>

<summary>network</summary>

* mainnet
* testnet

</details>

### Expected output

Once you have submitted the command above, you should receive a DID Document draft template, including a set of identity keys, including your chosen inputs and arguments. You can find an example of this output below:

<details>

<summary>Example output</summary>

```json
Method:  cheqdGenerateDidDoc

Arguments:  {
  "argsObj": {
    "verificationMethod": "Ed25519VerificationKey2020",
    "methodSpecificIdAlgo": "base58btc",
    "methodSpecificIdLength": 16,
    "network": "testnet"
  }
}

Result : {
  "didDoc": {
    "context": [],
    "id": "did:cheqd:testnet:e43f36e4-9fa6-40a4-a8f9-7f7b49eb44db",
    "controller": [
      "did:cheqd:testnet:e43f36e4-9fa6-40a4-a8f9-7f7b49eb44db"
    ],
    "authentication": [
      "did:cheqd:testnet:e43f36e4-9fa6-40a4-a8f9-7f7b49eb44db#key-1"
    ],
    "assertionMethod": [],
    "capabilityInvocation": [],
    "capabilityDelegation": [],
    "keyAgreement": [],
    "alsoKnownAs": [],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:e43f36e4-9fa6-40a4-a8f9-7f7b49eb44db#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:e43f36e4-9fa6-40a4-a8f9-7f7b49eb44db",
        "publicKeyMultibase": "z2yJuNbhoUpRn7ypAugSLzkCc8QEw146RJ8DD3jzCZQ6A",
        "publicKeyJwk": []
      }
    ],
    "service": []
  },
  "keys": {
    "publicKeyHex": "XXXX",
    "privateKeyHex": "XXXXXXXX",
    "kid": "XXXX",
    "type": "Ed25519"
  }
}
```

</details>

### Unexpected output

After running the above command, if you see an unexpected error, follow our [DID Operations Troubleshooting Guide](did-operations-troubleshooting.md) to fix it. A common error is:

```bash
Unexpected token v in JSON at position 1
```

## Method 2: Standalone key creation

You can generate identity keys standalone in a plug-and-play format for future use in [create DID](./) or [update DID](update-did.md) transactions.

#### Command

```bash
veramo execute -m cheqdGenerateIdentityKeys
```

<details>

<summary>Output</summary>

```bash
Method:  cheqdGenerateIdentityKeys

Arguments:  {
  "argsObj": {
    "args": {}
  }
}

Result : {
  "publicKeyHex": "XXXX",
  "privateKeyHex": "XXXXXXXXX",
  "kid": "XXXX",
  "type": "Ed25519"
}
```

</details>

## Converting identity keys

If you want to use identity keys outside of Veramo CLI, you can convert keys from one format to another.

For example, if you would like to convert from `hex` to `base64`, you can use the following generic approach:

```typescript
import { fromString, toString } from 'uint8arrays'

const keyPairBase64: IKeyPair = {
    publicKey: toString(fromString(keyPair.publicKeyHex, 'hex'), 'base64'),
    privateKey: toString(fromString(keyPair.privateKeyHex, 'hex'), 'base64')
}
```

This works with other encoding conversion libraries (i.e. `multiformats`).
