# 📚 Creating a DID with cheqd Cosmos CLI

The purpose of this document is to describe how a DID (and associated DIDDoc) can be created using [the `cheqd-node` Cosmos CLI](https://docs.cheqd.io/node/docs/cheqd-cli).

> **NOTE: The guidance below is only recommended to be used for testing purposes!**
>
> Using `cheqd-node` Cosmos CLI for real-world production environments is not recommended, since the identity keys are passed in raw form to the CLI. This is fine in development/testing usage, but is not recommend for mainnet.
>
> Developers are encouraged to use [production-grade CLI tools such as Veramo SDK for cheqd](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/) or look at [our developer guide on how to integrate custom applications](../developer-guide.md).

## Setup

### Pre-requisites

1. Access to a [`cheqd-node` Cosmos CLI binary](https://github.com/cheqd/cheqd-node/releases). You can either get this from [our Github releases](https://github.com/cheqd/cheqd-node/releases), or [download the `cheqd-cli` Docker container image](https://github.com/cheqd/cheqd-node/pkgs/container/cheqd-node).
2. Understand how to use cheqd [accounts](https://docs.cheqd.io/node/docs/cheqd-cli/cheqd-cli-accounts) and [Cosmos SDK keys](https://docs.cheqd.io/node/docs/cheqd-cli/cheqd-cli-key-management).
3. Tokens to pay for identity transactions, since all ledger transactions are metered.
   1. For testing purposes, CHEQ test tokens can be acquired from [our testnet faucet](https://testnet-faucet.cheqd.io/).
   2. Mainnet CHEQ tokens can be [acquired from any marketplace where the $CHEQ token is traded](https://learn.cheqd.io/getting-set-up-on-cheqd/where-to-find-usdcheq).

For the remainder of this tutorial, it's assumed that the DID and associated DID Document is being created on testnet. These commands can easily be modified for mainnet.

## Creating a new DID + DIDDoc on testnet

### 1. Generate verification key

First, we'll need to generate a verification key:

```bash
cheqd-noded debug ed25519 random >> keys.txt
```

The result should look like the following:

```bash
$ cat keys.txt
{"pub_key_base_64":"MnrTheU+vCrN3W+WMvcpBXYBG6D1HrN5usL1zS6W7/k=","pub_key_multibase_58":"",\
"priv_key_base_64":"FxaJOy4HFoC2Enu1SizKtU0L+hmBRBAEpC+B4TopfQoyetOF5T68Ks3db5Yy9ykFdgEboPUes3m6wvXNLpbv+Q=="}
```

_**Note**: Keep this key safe! It is used to create the DIDDoc, and to update it in the future. Normally, you should be careful when `cat`-ing such keys as it reveals the private key as well._

### 2. Encode the identity key to Multibase58

Encode the identity key's _public_ key to `public_key_multibase`, as this will be later required in the `verification_method` section:

```bash
cheqd-noded debug encoding base64-multibase58 <pub_key_base_64>
```

For example:

```bash
$ cheqd-noded debug encoding base64-multibase58 MnrTheU+vCrN3W+WMvcpBXYBG6D1HrN5usL1zS6W7/k=
z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe3Nmod35uua9TE
```

### 3. Create a unique identifier for the DID

A `unique-id` should only be a 16-byte encoded base58 string (Indy-style) or a uuid.

For example, we can generate uuid using `uuidgen` tool:

```bash
uuidgen
b0ca0b75-ca6a-4674-a261-45f6dd0c9c77
```

The result for our example will be `b0ca0b75-ca6a-4674-a261-45f6dd0c9c77`, so let's use it as our `unique-id` in our DIDDoc.

### 4. Populate DIDDoc contents

Copy-paste the template below into your terminal into a blank file (e.g., using `nano`). We will add additional required information into the blank fields `<xxxxx>` in the next steps.

```bash
$ nano diddoc.json
{
  "id": "did:cheqd:<namespace>:<unique-id>",
  "verificationMethod": [
    {
      "id": "did:cheqd:<namespace>:<unique-id>#<key-alias>",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:<namespace>:<unique-id>",
      "publicKeyMultibase": "<verification-public-key-multibase>"
    }
  ],
  "authentication": [
    "did:cheqd:<namespace>:<unique-id>#<auth-key-alias>"
  ],
  "service": [{
    "id":"did:cheqd:<namespace>:<unique-id>#<service-key>",
    "type": "LinkedDomains",
    "serviceEndpoint": "<URI-to-object>"
  }]
}
```

In this template, you'll need to replace some values (as described in the [cheqd DID method](../../architecture/adr-list/adr-001-cheqd-did-method.md):

* `<namespace>`: Can be `testnet` or `mainnet`. For this example, we can use `testnet`.
* `<unique-id>`: Unique identifier, created in step #3
* `<key-alias>`: A key alias for the verification method identifier, e.g., `#key1`
* `<verification-public-key-multibase>`: Result of step #2 above
* `<auth-key-alias>`: Alias of authentication key. Can be a reference to an existing verification method.
* `<service-key>`: Alias for service property. This is an _optional_ section but useful to understand the power of DIDDocs.
* `<URI-to-object>`: A valid URI that can act as a service endpoint.

For example, the populated DIDDoc file might look like:

```json
{
  "id": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
  "verificationMethod": [
    {
      "id": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
      "publicKeyMultibase": "z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe3Nmod35uua9TE"
    }
  ],
  "authentication": [
    "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1"
  ],
  "service": [{
    "id":"did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#linked-domain",
    "type": "LinkedDomains",
    "serviceEndpoint": "https://bar.example.com"
  }]
}
```

We recommend you save this DIDDoc file (e.g., in a file called `diddoc.json`) for the following steps.

### 5. Create payload file

After assembling the DID-Document JSON file we are ready to compile the final payload file with private key inside.

```json
{
  "Payload": {
    ...
  },
  "SignInputs": [
    {
      "VerificationMethodID": "<verification-method-id>",
      "PrivKey": "<private key representation>"
    }
  ]
}
```

The example of `payload.json` file:

```json
{
  "Payload": {
    "id": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77",
        "publicKeyNultibase": "z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe3Nmod35uua9TE"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1"
    ],
    "service": [{
      "id":"did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#linked-domain",
      "type": "LinkedDomains",
      "serviceEndpoint": "https://bar.example.com"
    }]
  },
  "SignInputs": [
    {
      "VerificationMethodID": "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77#key1",
      "PrivKey": "FxaJOy4HFoC2Enu1SizKtU0L+hmBRBAEpC+B4TopfQoyetOF5T68Ks3db5Yy9ykFdgEboPUes3m6wvXNLpbv+Q=="
    }
  ]
}
```

### 6. Submitting DID creation request to the ledger

Now that we have our DIDDoc prepared, we can send a **create DID** request to the ledger:

```bash
cheqd-noded tx cheqd create-did \
  --fees 50000000000ncheq \
  --gas auto \
  --gas-adjustment 1.8 \
  --from <alias-to-cosmos-key>  \
  --node https://rpc.cheqd.network:443 \
  --chain-id cheqd-testnet-6 \
  "payload.json"
```

Where:

* `--from`: Should be an alias of a cheqd/Cosmos key, which will be used to pay for the ledger transaction.

After you execute the command, you will receive `"code": 0"` if the DID was successfully written to the ledger.

Otherwise, the `raw_logs` field in the response can help figure out why something went wrong. For example:

```bash
"code":1201,"data":"","raw_log":"failed to execute message; message index: 0: id:cheqd:testnet:fcbarcelona: DID Doc not found"
```

### 7. Query the DID from ledger after successful creation

Finally, to check that the DID was successfully written to the ledger, we can use the following query:

```bash
cheqd-noded query cheqd did-document "<identifier-of-your-DIDDoc>" --node https://rpc.testnet.cheqd.network:443
```

where:

* `<identifier-of-your-DIDDoc>`: Fully-qualified DID with `<unique-id>`

For example:

```bash
cheqd-noded query cheqd did "did:cheqd:testnet:b0ca0b75-ca6a-4674-a261-45f6dd0c9c77" --node https://rpc.testnet.cheqd.network:443
```

**Congratulations!** You've created, hopefully, the first of many DIDs on cheqd!

### 8. Query specific version

Since upgrading to `1.x.y` version, we introduced versioning feature for DID Documents. This means that it is possible to get the previous version of a DID Document using the particular versionId of the document.

For querying particular version of the DID-Document the next command can be used:

```bash
cheqd-noded query cheqd did-version [id] [version-id]
```

Where:

* `id` - identifier of your DID-Document. Fully-qualified DID with `<unique-id>`
* `version-id` - particular id of version you want to get

### Example

Command:

```bash
cheqd-noded query cheqd did-version did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612 76e546ee-78cd-5372-b34e-8b47461626e1 --node https://rpc.cheqd.net:443 --output json
```

Output:

```json
{
  "value": {
    "did_doc": {
      "context": [],
      "id": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612",
      "controller": [
        "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612"
      ],
      "verification_method": [
        {
          "id": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612#key-1",
          "verification_method_type": "Ed25519VerificationKey2020",
          "controller": "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612",
          "verification_material": "z6MkuGoZyDyji4sApi9L79CG484hoQPmtuqJSTbUpdrVQAqB"
        }
      ],
      "authentication": [
        "did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612#key-1"
      ],
      "assertion_method": [],
      "capability_invocation": [],
      "capability_delegation": [],
      "key_agreement": [],
      "service": [],
      "also_known_as": []
    },
    "metadata": {
      "created": "2022-11-17T10:29:53Z",
      "updated": "0001-01-01T00:00:00Z",
      "deactivated": false,
      "version_id": "76e546ee-78cd-5372-b34e-8b47461626e1",
      "next_version_id": "",
      "previous_version_id": ""
    }
  }
}

```

### 9. Query the all the versions metadata for DID

For querying all the versions there is a command:

```bash
cheqd-noded query cheqd did-metadata [id] [flags]
```

Where:

* `id` - identifier of your DID-Document. Fully-qualified DID with `<unique-id>`

#### Example

```bash
cheqd-noded query cheqd did-metadata did:cheqd:mainnet:c82f2b02-bdab-4dd7-b833-3e143745d612 --node https://rpc.cheqd.net:443 --output json
```

Output:

```json
{
  "versions": [
    {
      "created": "2022-11-17T10:29:53Z",
      "updated": "0001-01-01T00:00:00Z",
      "deactivated": false,
      "version_id": "76e546ee-78cd-5372-b34e-8b47461626e1",
      "next_version_id": "",
      "previous_version_id": ""
    }
  ],
  "pagination": null
}
```
