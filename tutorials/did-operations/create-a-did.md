# Create a DID

Follow these instructions to create a new DID and publish the associated DID Document on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/sdk/veramo-sdk-for-cheqd/setup.md) for Veramo CLI

## Instructions

## 1. Create your identity keys within a DID Document template

The first step is generating a template for your DID Document, including a new DID and associated public/private key pair. This process also aligns with method 1 [in the key management guide](identity-key-handling.md).

> It is important to note that this step does not create your DID on ledger, but it generates your identity keys as a draft DID Document for you to use in the on-ledger transaction in [Step 3](create-a-did.md#3-create-new-did-and-publish-diddoc).

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
  "versionId": ""07109dba-7582-491e-9c17-8fadbdabb304",
  "keys": {
    "publicKeyHex": "XXXX",
    "privateKeyHex": "XXXXXXXX",
    "kid": "XXXX",
    "type": "Ed25519"
  }
}
```

</details>

You can then use this template to populate your `payload.json` file in [Step 2](create-a-did.md#2-prepareedit-diddoc-contents).

### Unexpected output

After running the above command, if you see an unexpected error, follow our [DID Operations Troubleshooting Guide](did-operations-troubleshooting.md) to fix it. A common error is:

```bash
Unexpected token v in JSON at position 1
```

## 2. Prepare/edit DIDDoc contents

Before creating a DID on-ledger, you will need to prepare the `payload.json` file. This file can be saved where ever you choose, but the location must be specified in the create DID command used in [Step 3](create-a-did.md#3-create-new-did-and-publish-diddoc). (By default, it will be saved under the project root directory.)

You should copy and paste the output of [Step 1](create-a-did.md#1-create-your-identity-keys-within-a-did-document-template) as an initial starting template for your `payload.json` file.

### Example of `payload.json` files

The below examples show the variation in syntax for different verification method key types in the DIDDoc payload file itself. Note that each key type has a slightly different output.

<details>

<summary>Ed25519VerificationKey2018</summary>

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
        "type": "Ed25519VerificationKey2018",
        "controller": "did:cheqd:testnet:<uniqueId>",
        "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"     
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
  "versionId": "<uuid>", // optional
  "keys": [{
    "publicKeyHex": "<public_key_in_hex_encoding>",
    "privateKeyHex": "<private_key_in_hex_encoding>",
    "kid": "<equal_to_public_key_hex>",
    "type": "Ed25519"
    // add additional key(s) if required
  }],
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

</details>

<details>

<summary>Ed25519VerificationKey2020</summary>

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
        "publicKeyMultibase": "z2yJuNbhoUpRn7ypAugSLzkCc8QEw146RJ8DD3jzCZQ6A"     
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
  "versionId": "<uuid>", // optional
  "keys": [{
    "publicKeyHex": "<public_key_in_hex_encoding>",
    "privateKeyHex": "<private_key_in_hex_encoding>",
    "kid": "<equal_to_public_key_hex>",
    "type": "Ed25519"
    // add additional key(s) if required
  }],
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

</details>

<details>

<summary>JSONWebKey2020</summary>

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
        "type": "JsonWebKey2020",
        "controller": "did:cheqd:testnet:<uniqueId>",
        "publicKeyJwk": {
          "kty": "OKP", // external (property name)
          "crv": "Ed25519", // external (property name)
          "x": "VCpo2LMLhn6iWku8MKvSLg2ZAoC-nlOyPVQaO3FxVeQ" // external (property name)
        }
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
  "versionId": "<uuid>", // optional
  "keys": [{
    "publicKeyHex": "<public_key_in_hex_encoding>",
    "privateKeyHex": "<private_key_in_hex_encoding>",
    "kid": "<equal_to_public_key_hex>",
    "type": "Ed25519"
    // add additional key(s) if required
  }],
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

</details>

### Parameters

* `kms` (default `local`): Key Management System (KMS) to be used for storage.
* `alias`: A human-friendly alias for the DID. Only used locally when referencing operations in Veramo CLI.
* `document`: Full body of the DID Document
* `versionId`: (optional): Custom versionId for the DID Document. If this is not set manually, then a UUID will be automatically generated for the DID Document version.
* `keys`: Keys used to sign the DIDDoc. These must match the ones specified in the DIDDoc, otherwise an error will be thrown.
* `fee`
  * `amount`: An array of coins, coins are represented as an object with 2 fields
    * `denom`: ncheq (smallest denomination classification)
    * `amount`: **50000000000** (This is **50 CHEQ by default** and will not work with a different value)
  * `gas`: Each transaction must specify the maximum amount of gas it may consume.
  * `payer` (optional): The cheqd fee payer address
  * `granter` (optional): The cheqd fee granter address, Provided the grantee has an allowance by the granter

> Note that transaction fees are paid by the cheqd account set in the `agent.yml` configuration file, [setup here](../../guides/sdk/veramo-sdk-for-cheqd/setup.md). Each of cheqd's on-ledger identity transactions has a **fixed fee,** [the pricing for cheqd DIDs and DID-Linked Resources can be found here](https://docs.cheqd.io/node/architecture/adr-list/adr-005-genesis-parameters#cheqd-module-did-module). If your account has insufficient balance the transaction will fail.

### 3. Create new DID and publish DIDDoc

```bash
veramo execute -m cheqdCreateIdentifier --argsFile path/to/payload.json
```

If you do not specify the `--argsFile`, you can also paste a JSON inline argument object by using the `--argsJSON` flag followed by the JSON payload.

## Next steps

If your transaction is successful, you'll receive a success message along with the transaction details. You can [query your DID using the instructions here to see the representation of your DID Document](query-did.md) on-ledger.

### Troubleshooting

1. If you are using testnet, you can top-up your testnet balance using our [testnet faucet](https://testnet-faucet.cheqd.io/).
2. You can also configure your Keplr wallet to [support cheqd testnet using the instructions here](https://docs.cheqd.io/identity/guides/sdk/veramo-sdk-for-cheqd/setup-cli#2.5.-add-cheqd-testnet-to-your-keplr-wallet).
3. If you are using mainnet, you can [purchase CHEQ tokens here](https://cheqd.io/buy).
4. Check out our [troubleshooting guide for DID Operations](did-operations-troubleshooting.md) to see common errors and fixes.
