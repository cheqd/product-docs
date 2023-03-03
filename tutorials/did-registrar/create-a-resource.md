# Create a DID

Follow these instructions to create a new Resource for DID using DID registrar. This tutorial will use the cheqd did regisrtar swagger api's and the veramo cli.

> ⚠️ **Before you begin...**
> Make sure you've completed [creating a DID](./create-a-did.md) for this tutorial for Veramo CLI

### Request Create Operation
Use the `/{did}/create-resource` api to publish a resource

1. Paste the DID in the did path parameter
2. Generate request body by providing name, type and base64 encoded data
    <details>
    <summary>Request</summary>

    ```json
      {
        "data": "SGVsbG8gV29ybGQ=",
        "name": "ResourceName",
        "type": "TextDocument"
      }
    ```
    </details>

2. Click on execute to perform the request
    <details>
    <summary>Response</summary>

    ```json
    {
    "jobId": "79638705-da04-492c-aeb4-5e66cfaba0f8",
    "resourceState": {
        "did": "d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
        "state": "action",
        "action": "signPayload",
        "description": "Please sign the following payload with the keys in verificationMethod and Add the signingResponse in secret",
        "signingRequest": [
        {
            "kid": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
            "type": "Ed25519VerificationKey2020",
            "alg": "EdDSA",
            "serializedPayload": "CgtIZWxsbyBXb3JsZBIkZDRkNDQwNGItNmY3Zi00ZTAyLThkNDEtYjgxYWZiMWYwNWVkGiQxZGM1ODBmNS1hNWNhLTRhMzMtYWY4My00MzEyNmZlYTZmMmQiDFJlc291cmNlTmFtZTIMVGV4dERvY3VtZW50"
        }
        ],
        "secret": {
        "signingResponse": [
            "e.g. { verificationMethodId: did:cheqd:testnet:qsqdcansoica#key-1, signature: aca1s12q14213casdvaadcfas }"
        ]
        }
      }
    }
    ```
    </details>

    If you notice the state in didState should be in `action` and the description should request to `sign the payload`

    Note down the serialized payload, jobId from the response

<br>

### Sign Payload

Enter the below command in the cli
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

```bash
    Arguments:  {
    "keyRef": "d2ce308f19ee116ee810956605632ccd024bad8dedd02baf49f248d03acdaa48",
    "algorithm": "Ed25519",
    "data": "CgtIZWxsbyBXb3JsZBIkZDRkNDQwNGItNmY3Zi00ZTAyLThkNDEtYjgxYWZiMWYwNWVkGiQxZGM1ODBmNS1hNWNhLTRhMzMtYWY4My00MzEyNmZlYTZmMmQiDFJlc291cmNlTmFtZTIMVGV4dERvY3VtZW50",
    "encoding": "base64"
    }

    Result : "RiJelgAtm26arp8-cYfLVyyYnvoAgnnaL1Lndf_6G_iuoQtrTRgt5TD1GCwJXb30y8Fc7L_jzN3hH4WZRzeqDw"
```
</details>


**NOTE: If there are n verification methods for the controller then n signatures are required to publish a DID**


Copy the Result value from the response

<br>

### Submit Signature

Use the `/create-resource` api again

1. Create the payload using as below
    * jobId
    * secret 
       * signingResponse 
            * verificationMethodId
            * signatue

    <details>
    <summary>Request</summary>

    ```json
    {
        "jobId": "79638705-da04-492c-aeb4-5e66cfaba0f8",
        "secret": {
            "signingResponse": [{
                "verificationMethodId": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
                "signature": "RiJelgAtm26arp8-cYfLVyyYnvoAgnnaL1Lndf_6G_iuoQtrTRgt5TD1GCwJXb30y8Fc7L_jzN3hH4WZRzeqDw"
            }]
        }
    }
    ```
    </details>

2. Click on Execute

    <details>
    <summary>Response</summary>

    ```json
    {
    "jobId": "79638705-da04-492c-aeb4-5e66cfaba0f8",
    "resourceState": {
        "resourceId": "1dc580f5-a5ca-4a33-af83-43126fea6f2d",
        "state": "finished",
        "secret": {
        "signingResponse": [
            {
            "verificationMethodId": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
            "signature": "RiJelgAtm26arp8-cYfLVyyYnvoAgnnaL1Lndf_6G_iuoQtrTRgt5TD1GCwJXb30y8Fc7L_jzN3hH4WZRzeqDw"
            }
        ]
        },
        "resource": {
        "collectionId": "d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
        "id": "1dc580f5-a5ca-4a33-af83-43126fea6f2d",
        "name": "ResourceName",
        "resourceType": "TextDocument",
        "data": {
            "0": 72,
            "1": 101,
            "2": 108,
            "3": 108,
            "4": 111,
            "5": 32,
            "6": 87,
            "7": 111,
            "8": 114,
            "9": 108,
            "10": 100
        }
        }
    }
    }
    ```
    </details>

3. The state in didState should be `finished` in the response, the DID is created successfully
