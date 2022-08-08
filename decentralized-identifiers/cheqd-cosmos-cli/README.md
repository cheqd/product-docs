# Creating DID + DID Doc with cheqd Cosmos CLI

The purpose of this document is to describe how a DID (and associated DIDDoc) can be created using [the `cheqd-node` Cosmos CLI](https://docs.cheqd.io/node/docs/cheqd-cli).

> **NOTE: The procedures below are only recommended for development purposes!**
>
> Using `cheqd-node` Cosmos CLI for real-world production usage is not recommended, since the identity keys are passed in raw form to the CLI. This is fine in development/testing usage, but is not recommend for mainnet.
>
> Developers are encouraged to use [production-grade CLI tools such as Veramo SDK for cheqd](../../veramo-sdk-for-cheqd/README.md) or look at [our developer guide on how to integrate custom applications](../developer-guide/README.md).

## Setup

### Pre-requisites

1. Access to a [`cheqd-node` Cosmos CLI binary](https://github.com/cheqd/cheqd-node/releases). You can either get this from [our Github releases](https://github.com/cheqd/cheqd-node/releases), or [download the `cheqd-cli` Docker container image](https://github.com/cheqd/cheqd-node/pkgs/container/cheqd-cli).
2. Understand how to use cheqd [accounts](https://docs.cheqd.io/node/docs/cheqd-cli/cheqd-cli-accounts) and [Cosmos SDK keys](https://docs.cheqd.io/node/docs/cheqd-cli/cheqd-cli-key-management).
3. Tokens to pay for identity transactions, since all ledger transactions are metered.
   1. For testing purposes, CHEQ test tokens can be acquired from [our testnet faucet](https://testnet-faucet.cheqd.io/).
   2. Mainnet CHEQ tokens can be [acquired from any marketplace where the $CHEQ token is traded](https://learn.cheqd.io/getting-set-up-on-cheqd/where-to-find-usdcheq).

For the remainder of this tutorial, it's assumed that the DID + DIDDoc is being created on testnet. These commands can easily be modified for mainnet.

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

***Note**: Keep this key safe! It is used to create the DIDDoc, and to update it in the future. Normally, you should be careful when `cat`-ing such keys as it reveals the private key as well.*

### 2. Encode the identity key to Multibase58

Encode the identity key's *public* key to `public_key_multibase`, as this will be later required in the `verification_method` section:

```bash
cheqd-noded debug encoding base64-multibase58 <pub_key_base_64>
```

For example:

```bash
$ cheqd-noded debug encoding base64-multibase58 MnrTheU+vCrN3W+WMvcpBXYBG6D1HrN5usL1zS6W7/k=
z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe3Nmod35uua9TE
```

### 3. Create a unique identifier for the DID

To create a `unique-id` for our DID, we can use first 32 symbols of `multibase58` representation of our public key.

For example, we can truncate previous output as:

```bash
printf '%.32s\n' `cheqd-noded debug encoding base64-multibase58 <pub_key_base_64>`
```

The result for our example will be `z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe`, so let's use it as our `unique-id` in our DIDDoc.

### 4. Populate DIDDoc contents

Copy-paste the template below into your terminal into a blank file (e.g., using `nano`). We will add additional required information into the blank fields `<xxxxx>` in the next steps.

```bash
$ nano diddoc.json
{
  "id": "did:cheqd:<namespace>:<unique-id>",
  "verification_method": [
    {
      "id": "did:cheqd:<namespace>:<unique-id>#<key-alias>",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:<namespace>:<unique-id>",
      "public_key_multibase": "<verification-public-key-multibase>"
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

In this template, you'll need to replace some values (as described in the [cheqd DID method](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method)):

* `<namespace>`: Can be `testnet` or `mainnet`. For this example, we can use `testnet`.
* `<unique-id>`: Unique identifier, created in step #3
* `<key-alias>`: A key alias for the verification method identifier, e.g., `#key1`
* `<verification-public-key-multibase>`: Result of step #2 above
* `<auth-key-alias>`: Alias of authentication key. Can be a reference to an existing verification method.
* `<service-key>`: Alias for service property. This is an *optional* section but useful to understand the power of DIDDocs.
* `<URI-to-object>`: A valid URI that can act as a service endpoint.

For example, the populated DIDDoc file might look like:

```jsonc
{
  "id": "did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe",
  "verification_method": [
    {
      "id": "did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe#key1",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe",
      "public_key_multibase": "z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe3Nmod35uua9TE"
    }
  ],
  "authentication": [
    "did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe#key1"
  ],
  "service": [{
    "id":"did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe#linked-domain",
    "type": "LinkedDomains",
    "serviceEndpoint": "https://bar.example.com"
  }]
}
```

We recommend you save this DIDDoc file (e.g., in a file called `diddoc.json`) for the following steps.

### 5. Submitting DID creation request to the ledger

Now that we have our DIDDoc prepared, we can send a **create DID** request to the ledger:

```bash
cheqd-noded tx cheqd create-did "$(cat diddoc.json)" "did:cheqd:testnet:zJ5EDiiiKWDyo79n#key1" "FxaJOy4HFoC2Enu1SizKtU0L+hmBRBAEp+B4TopfQoyetOF5T68Ks3db5Yy9ykFdgEboPUes3m6wvXNLpbv+Q==" --from <alias-to-cosmos-key>  --node https://rpc.testnet.cheqd.network:443 --chain-id cheqd-testnet-4 --fees 5000000ncheq
```

Where:

* `"did:cheqd:testnet:zJ5EDiiiKWDyo79n#key1"` is the `id` of `verification_method` section
* `FxaJOy4HFoC2Enu1SizKtU0L+hmBRBAEp+B4TopfQoyetOF5T68Ks3db5Yy9ykFdgEboPUes3m6wvXNLpbv+Q==` is the private key (`priv_key_base_64` in Step #1) for signing the DIDDoc
* `--from`: Should be an alias of a cheqd/Cosmos key, which will be used to pay for the ledger transaction.

After you execute the command, you will receive `"code": 0"` if the DID was successfully written to the ledger.

Otherwise, the `raw_logs` field in the response can help figure out why something went wrong. For example:

```bash
"code":1201,"data":"","raw_log":"failed to execute message; message index: 0: id:cheqd:testnet:fcbarcelona: DID Doc not found"
```

### 6. Query the DID from ledger after successful creation

Finally, to check that the DID was successfully written we can use the following query:

```bash
cheqd-noded query cheqd did "<identifier-of-your-DIDDoc>" --node https://rpc.testnet.cheqd.network:443
```

where:

* `<identifier-of-your-DIDDoc>`: Fully-qualified DID with `<unique-id>`

For example:

```bash
cheqd-noded query cheqd did "did:cheqd:testnet:z4Q41kvWsd1JAuPFBff8Dti7P6fLbPZe" --node https://rpc.testnet.cheqd.network:443
```

**Congratulations!** You've created, hopefully, the first of many DIDs on cheqd!
