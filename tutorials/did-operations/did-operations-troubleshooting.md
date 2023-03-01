# DID Operations Troubleshooting

This guide provides common errors and potential fixes for them if you encountered errors when [creating a DID](create-a-did.md).

## 1. When generating keys along with a DIDDoc template

When you try to generate `keys` along with `DIDDoc` template using command below

```bash
veramo execute -m cheqdGenerateDidDoc --argsJSON '{"verificationMethod": "Ed25519VerificationKey2020", "methodSpecificIdAlgo": "uuid", "network": "testnet"}'
```

If you encounter an error below

`Unexpected token v in JSON at position 1`

Create a `.json` file (e.g. name it something like `identity-keys-on-the-fly-with-did-docs.json` in same working directory level).&#x20;

Copy and paste the content from below into your newly created `.json` file. You can choose from any of the inputs and arguments below:

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

For example:

```json
{
  "verificationMethod": "Ed25519VerificationKey2020",
  "methodSpecificIdAlgo": "uuid",
  "network": "testnet"
}
```

Then, instead of passing it as argument, we'll pass it as a file. Use command below to do it.

```bash
veramo execute -m cheqdGenerateDidDoc --argsFile identity-keys-on-the-fly-with-did-docs.json
```

The above command will output something like this (i.e. a template DID DOC with the keys)

```bash
{ argsFile: 'identity-keys-on-the-fly-with-did-docs.json' }

Method:  cheqdGenerateDidDoc

Arguments:  {
  "verificationMethod": "Ed25519VerificationKey2020",
  "methodSpecificIdAlgo": "uuid",
  "network": "testnet"
}

Result : {
  "didDoc": {
    "context": [],
    "id": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
    "controller": [
      "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590"
    ],
    "authentication": [
      "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590#key-1"
    ],
    "assertionMethod": [],
    "capabilityInvocation": [],
    "capabilityDelegation": [],
    "keyAgreement": [],
    "alsoKnownAs": [],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
        "publicKeyMultibase": "z6MkuBcREn1LjuDCe9FfSa7WLa3yY51KFoSEg2fUifzQqVy1"
      }
    ],
    "service": []
  },
  "keys": {
    "publicKeyHex": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
    "privateKeyHex": "84ecb7d289dd5c5e82071f8a16503ba5d7b5b3174a619186f430918a6ab00e3b074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
    "kid": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
    "type": "Ed25519"
  }
}
```

Then you can go back to your `payload.json` file (if you didn't have one you can simply create one manually, and paste content below as template) which should be something like this at first.

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

Then you're going to update `keys` and `document` section of it (`payload.json`) with `keys` and `didDoc` you generated in above step.

After populating `keys` and `document` section. Your `payload.json` file should look something like this:

```json
{
  "kms": "local",
  "alias": "your-alias",
  "document": {
    "context": [],
    "id": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
    "controller": ["did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590"],
    "authentication": ["did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590#key-1"],
    "assertionMethod": [],
    "capabilityInvocation": [],
    "capabilityDelegation": [],
    "keyAgreement": [],
    "alsoKnownAs": [],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
        "publicKeyMultibase": "z6MkuBcREn1LjuDCe9FfSa7WLa3yY51KFoSEg2fUifzQqVy1"
      }
    ],
    "service": []
  },
  "keys": [
    {
      "publicKeyHex": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "privateKeyHex": "84ecb7d289dd5c5e82071f8a16503ba5d7b5b3174a619186f430918a6ab00e3b074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "kid": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "type": "Ed25519"
    }
  ]
  "versionId": [
    "<uuid>" // optional
    ]
  "fee": {
    "amount": [{
      "denom": "ncheq",
      "amount": "50000000000"
      }],
    "gas": "400000",
    "payer": "cheqd1rnr5jrt4exl0samwj0yegv99jeskl0hsxmcz96"
  }
}
```

Assuming you have enough `ncheq` tokens inside your wallet, you can now run the command below to create your DID.

```bash
veramo execute -m cheqdCreateIdentifier --argsFile path/to/payload.json
```

If the above `cmd` resulted something like this:

`Account does not exist on chain. Send some tokens there before trying to query sequence.`

This means you don't have enough tokens in your wallet.

You can go to cheqd's [testnet faucet](http://testnet-faucet.cheqd.io/) to get your account some test tokens.

After successfully creating your DID, you should see something like this:

```bash
Result : {
  "did": "did:cheqd:testnet:3e6bd814-6851-4c8a-b114-c64f035ef590",
  "controllerKeyId": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
  "keys": [
    {
      "type": "Ed25519",
      "kid": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "publicKeyHex": "074035480cdcf09c33b1e8066deb55c75822c8c3b27f1c100717eb413bc08e06",
      "meta": {
        "algorithms": [
          "Ed25519",
          "EdDSA"
        ]
      },
      "kms": "local"
    }
  ],
  "services": [],
  "provider": "did:cheqd:testnet",
  "alias": "your-alias"
}
```

Notice, `controllerKeyId` is now the `publicKeyHex` you generated the firstly. i.e did is know associated to you via `publickKeyHex` or you're the controller of this the DID you created. 😄
