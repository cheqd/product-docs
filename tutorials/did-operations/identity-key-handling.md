# Managing identity keys

Follow these instructions to:

1. Generate identity public/private keypairs
2. Generate identity keys WITH a DIDDoc
3. Convert identity key pairs generated using Veramo agent to other key encoding formats

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/setup-cli.md) for Veramo CLI

## Generating identity keys

### Method 1: Standalone key creation

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
<br>

### Method 2: Generating identity keys on-the-fly with DIDDocs

Instead of creating identity keys standalone, you can also generate them along with a DIDDoc template.

#### Command

```bash
veramo execute -m cheqdGenerateDidDoc --argsJSON '{"verificationMethod": "Ed25519VerificationKey2020", "methodSpecificIdAlgo": "base58btc", "network": "testnet"}'
```

<details>
<summary>Output</summary>

```bash
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
    "id": "did:cheqd:testnet:z2yJuNbhoUpRn7yp",
    "controller": [
      "did:cheqd:testnet:z2yJuNbhoUpRn7yp"
    ],
    "authentication": [
      "did:cheqd:testnet:z2yJuNbhoUpRn7yp#key-1"
    ],
    "assertionMethod": [],
    "capabilityInvocation": [],
    "capabilityDelegation": [],
    "keyAgreement": [],
    "alsoKnownAs": [],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:z2yJuNbhoUpRn7yp#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:z2yJuNbhoUpRn7yp",
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

<br>

The command `cheqdGenerateDidDoc` requires three arguments which have the following possible values

* verificationMethod
  * Ed25519VerificationKey2020
  * JsonWebKey2020
  * Ed25519VerificationKey2018

* methodSpecificIdAlgo
  * base58btc
  * uuid

* network
  * mainnet
  * testnet


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
