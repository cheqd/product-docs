---
description: >-
  Create a DID-Linked Resource (DLR), linked to a Decentralized Identifier (DID)
  on cheqd, using Veramo.
---

# Create a DID-Linked Resource

Follow these instructions to create a new DID-Linked Resource on cheqd mainnet or testnet.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup/) for Veramo CLI

## Instructions

### 1. Create a DID

Follow [this](../dids/) tutorial to generate keys and create a DID.

### 2. Create your Resource content and save the file locally

DID-Linked Resources can be any type of file or content that is **below \~200kb in size**.

For the purpose of an example, lets use an AnonCreds schema (JSON file) as the resource:

```json
{
  "name": "degreeSchema",
  "version": "1.5.7",
  "attrNames": ["name", "age", "degree", "grade"]
 }
```

Save this file locally and call it something like `resource.json.`

### 3. Create a UUID for the Resource

[UUIDs are used to identify DID-Linked Resources](https://en.wikipedia.org/wiki/Universally_unique_identifier). On Unix systems, the `uuidgen` tool can be used to generate a new UUID:

```bash
$ uuidgen
e7b662f8-d3f8-4a83-bd00-2cdcd6cc50ab
```

### 4. Choose an option for passing the resource that best suits you

#### Option 1: Passing resource as Base64

Prepare a file with resource and encode it into `base64`. On Unix systems, you can use the following command input:

```bash
$ base64 -w 0 resource.json
<path-to-the-resource-file>
```

Expected output:

```bash
$ base64 -w 0 resource.json
SGVsbG8sIHdvcmxk
```

You will need to paste the output of the Base64 into the `data` field of the `payload.json` file as shown in step 5.

#### **Option 2: Passing resource as file**

Once you have saved your Resource file locally, you can copy the path and enter it in the `file` field of the `payload.json` file as shown in step 5.

### 5. Prepare your payload file

Before carrying out a Resource transaction, you will need to prepare the `payload.json` file. This file can be saved wherever you choose, but the location must be specified in the create Resource command. (By default, it will be saved under the project root directory.)

#### Parameters

* `kms` (default `local`): Key Management System (KMS) to be used for storage;
* `payload`: Resource definition
  * `collectionId`: The last fragment of the corresponding DIDDoc
  * `id`: Use output from the previous step here
  * `name`: Arbitrary human-readable string used to identify the resource
  * `resourceType`: Resource type, such as `CL-Schema`, `JSONSchema2020`, etc
  * `version`: (Optional) client-given resource version
  * `alsoKnownAs`: (Optional) alternative URI(s) provided for the resource
  * `data`: (Optional) base64 encoded data of resource file
* `signInputs`: Keys used to sign the corresponding DIDDoc. These must match the ones specified in the DIDDoc, otherwise an error will be thrown
  * `verificationMethodId`: Verification method ID from DIDDoc where key is published
  * `keyType` should match values that you used to create the DIDDoc
  * `privateKeyHex` should match values that you used to create the DIDDoc
* `file`: (Optional) path to resource file
* `fee:`
  * `amount`: An array of coins, coins are represented as an object with 2 fields
    * `denom`: ncheq (smallest denomination classification)
    * `amount`: **See section below on fees**
  * `gas`: Each transaction must specify the maximum amount of gas it may consume.
  * `payer` (Optional): The cheqd fee payer address
  * `granter` (Optional): The cheqd fee granter address, Provided the grantee has an allowance by the granter

The Payload file can include a Resource passed either as a file or a base64 encoded string:

<details>

<summary>Example Payload file for passing Resource as Base64 encoded string</summary>

```json
{
    "kms": "local",
    "payload": {
        "collectionId": "d3e515cf-81af-40cb-9ac1-154827986d29", // unique ID of associated 'parent' DID
        "id": "e7b662f8-d3f8-4a83-bd00-2cdcd6cc50ab",
        "name": "TestResource",
        "resourceType": "JsonDocument",
        "version": "",
        "alsoKnownAs": [],
        "data": "SGVsbG8sIHdvcmxk" // note that the base64 encoded content is passed here
    },
    "network": "testnet",
    "signInputs": [{
        "verificationMethodId": "did:cheqd:testnet:d3e515cf-81af-40cb-9ac1-154827986d29#key-1",
        "keyType": "Ed25519",
        "privateKeyHex": "0f5c124886178037952e87e0cdc55d185732577fca19ae877e64ac9ab24a0cc534e5326e70f1a42d785d93048aee806c359ec75a7b06f39253befd1746708438"
    }],
    "fee": {
      "amount": [{
        "denom": "ncheq",
        "amount": "2500000000" // 2.5 CHEQ is the fixed fee for a JSON transaction
        }],
      "gas": "1000000",
      "payer": "cheqd1rnr5jrt4exl0samwj0yegv99jeskl0hsxmcz96" // must match the account set in the agent.yaml file
    }
}
```

</details>

<details>

<summary>Example Payload file for passing Resource as file</summary>

```json
{
    "kms": "local",
    "payload": {
        "collectionId": "d3e515cf-81af-40cb-9ac1-154827986d29",
        "id": "e7b662f8-d3f8-4a83-bd00-2cdcd6cc50ab",
        "name": "TestResource",
        "resourceType": "JsonDocument"
        "version": "",
        "alsoKnownAs": []
    },
    "network": "testnet",
    "signInputs": [{
        "verificationMethodId": "did:cheqd:testnet:d3e515cf-81af-40cb-9ac1-154827986d29#key-1",
        "keyType": "Ed25519",
        "privateKeyHex": "0f5c124886178037952e87e0cdc55d185732577fca19ae877e64ac9ab24a0cc534e5326e70f1a42d785d93048aee806c359ec75a7b06f39253befd1746708438"
    }],
    "file": "/path/to/resource.json", // note that the resource passed as file is inputted here
    "fee": {
      "amount": [{
        "denom": "ncheq",
        "amount": "2500000000" // 2.5 CHEQ is the fixed fee for a JSON transaction
        }],
      "gas": "1000000",
      "payer": "cheqd1rnr5jrt4exl0samwj0yegv99jeskl0hsxmcz96" // must match the account set in the agent.yaml file
    }
}
```

</details>

### 6. Understanding Resource Fees

{% hint style="info" %}
The maximum file size for a resource is roughly **46KB** which may require a gas fee of up to **2000000**.
{% endhint %}

The fee for a resource transaction changes depending on the file type being passed to the ledger. Currently there are three different variations on the fee:

<details>

<summary>JSON file</summary>

Within the payload file the amount should be specified as:

* `denom:` ncheq
* `amount:` 2500000000

This equates to **2.5 CHEQ.**

</details>

<details>

<summary>Image file</summary>

Within the payload file the amount should be specified as:

* `denom:` ncheq
* `amount:` 10000000000

This equates to **10 CHEQ.**

_Note that images may also require a larger 'gas' fee of up to 2000000_

</details>

<details>

<summary>Default (any other) file</summary>

Within the payload file the amount should be specified as:

* `denom:` ncheq
* `amount:` 5000000000

This equates to **5 CHEQ.**

</details>

> Note that transaction fees are paid by the cheqd account set in the `agent.yml` configuration file, [setup here](../setup/). If you do not specify a fee in the transaction, the ledger will automatically deduct the appropriate fee from the account set in your`agent.yml` configuration. Each of cheqd's on-ledger identity transactions has a **fixed fee,** [the pricing for cheqd DIDs and DID-Linked Resources can be found here](https://docs.cheqd.io/node/architecture/adr-list/adr-005-genesis-parameters#cheqd-module-did-module). If your account has insufficient balance the transaction will fail.

### 7. Create new DID-Linked Resource

```bash
veramo execute -m cheqdCreateLinkedResource --argsFile path/to/payload.json
```

If you do not specify the `--argsFile` in the previous step, you can also paste a JSON inline argument object by using the `--argsJSON` flag followed by the JSON payload.
