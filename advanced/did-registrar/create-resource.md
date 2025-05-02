---
description: >-
  Create a DID-Linked Resource (DLR) linked to a cheqd DID using the DID
  Registrar.
---

# Create a DID-Linked Resource

Follow these instructions to create a new Resource linked to a DID using DID registrar. This tutorial will use the cheqd did regisrtar swagger API's and the Veramo CLI.

> ⚠️ **Before you begin...** Make sure you've completed [creating a DID](create-did.md) for this tutorial for Veramo CLI

## 1. Request Create Operation

Use the `/{did}/create-resource` api to publish a new DID-Linked Resource

1. Paste the DID in the did path parameter
2. Generate request body by providing name, type and base64 encoded data
3.  Click on execute to perform the request

    This response requests an `action` for you to sign the serialized payload again in a CLI. This is a security feature which means you are not passing your private key to the Registrar. Note down the serialized payload, jobId from the response.

## 2. Sign Payload

Sign the serialized payload in your CLI with the below command

```bash
veramo execute -m keyManagerSign
```

Fill in the prompts

1. keyRef: Enter the kid of the keyPair generated in the first step
2. algorithm: `Ed25519`
3. data to sign: Paste the serialized payload from the previous step
4. enconding: Select `base64`

<details>

<summary>Example Response</summary>

```json
    Arguments:  {
    "keyRef": "d2ce308f19ee116ee810956605632ccd024bad8dedd02baf49f248d03acdaa48",
    "algorithm": "Ed25519",
    "data": "CgtIZWxsbyBXb3JsZBIkZDRkNDQwNGItNmY3Zi00ZTAyLThkNDEtYjgxYWZiMWYwNWVkGiQxZGM1ODBmNS1hNWNhLTRhMzMtYWY4My00MzEyNmZlYTZmMmQiDFJlc291cmNlTmFtZTIMVGV4dERvY3VtZW50",
    "encoding": "base64"
    }

    Result : "RiJelgAtm26arp8-cYfLVyyYnvoAgnnaL1Lndf_6G_iuoQtrTRgt5TD1GCwJXb30y8Fc7L_jzN3hH4WZRzeqDw"
```

</details>

**NOTE:** If there are _n_ verification methods for the controller then _n_ signatures are required to publish a resource.

Copy the Result value from the response.

## 3. Submit Signature

Use the `/create-resource` api again

1. Create the payload using the following values
   * jobId
   * secret
     * signingResponse
       * verificationMethodId
       * signature
2. Click on Execute
3. The state in didState should be `finished` in the response, the DID is created successfully

## 4. Check your Resource is live

You can check your DID on the [Universal Resolver](https://dev.uniresolver.io/) or by going to: `https://resolver.cheqd.net/1.0/identifiers/{yourDID}/resources/{yourResourceId}`
