
# Generating identity keys using the Veramo SDK CLI

Follow these instructions to:

1. Generate identity key pairs (public and private keys)
2. Generate identity keys WITH a DIDDoc
3. Convert the identity key pairs generated within the Veramo agent into your own choice of key encoding

## Before you begin

* Make sure you've [done the pre-requisite setup](README.md)

## Generating identity keys

These keys are generated in a plug and play format ready to be inserted into the DID create of DID update method.

Use the command below to generate identity keys:

```bash
veramo execute -m cheqdGenerateIdentityKeys
```

This should return:

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

## Generating DIDDoc & identity keys

Use the command below to generate identity keys WITH a DIDDOC

```bash
veramo execute -m cheqdGenerateDidDoc --argsJSON '{"verificationMethod": "Ed25519VerificationKey2020", "methodSpecificIdAlgo": "base58btc", "methodSpecificIdLength": 16, "network": "testnet"}'
```

This should return:

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

## Converting identity keys

If after you have generated keys using the approach above, you need to use them outside of the Veramo agent, you can use your own choice of key encoding by converting them.

For example, if you would like to convert from `hex` to `base64` you can use the following generic approach:

```typescript
import { fromString, toString } from 'uint8arrays'

const keyPairBase64: IKeyPair = {
    publicKey: toString(fromString(keyPair.publicKeyHex, 'hex'), 'base64'),
    privateKey: toString(fromString(keyPair.privateKeyHex, 'hex'), 'base64')
}
```

This works with other encoding conversion libraries (i.e. `multiformats`).
