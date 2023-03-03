# Create a DID

Follow these instructions to create a new DID and publish the associated DIDDoc using DID registrar. This tutorial will use the cheqd did regisrtar swagger api's and the veramo cli.

<details>
<summary>Swagger UI</summary>

<figure><img src="../../.gitbook/assets/cheqd-did-registrar-swagger.png"></figure>

</details>

> ⚠️ **Before you begin...**
> Make sure you've completed the [veramo cli setup](./setup-tutorial.md) for this tutorial for Veramo CLI

### Generate Key Pair in veramo wallet

Enter the below command in the cli
```bash
veramo execute -m keyManagerCreate --argsJSON '{ "type": "Ed25519", "kms": "local" }'
```

<details>
<summary>Example Output</summary>

```json
{
  "type": "Ed25519",
  "kid": "d2ce308f19ee116ee810956605632ccd024bad8dedd02baf49f248d03acdaa48",
  "publicKeyHex": "d2ce308f19ee116ee810956605632ccd024bad8dedd02baf49f248d03acdaa48",
  "meta": {
    "algorithms": [
      "Ed25519",
      "EdDSA"
    ]
  },
  "kms": "local"
}
```

Note down the `kid` and `publicKeyHex` values of the generated key

</details>

<br>

### Generate DID Payload 
The DID payload can be generated using our helper api `/did-document`

Select the following options
* Select VerificationMethodType
* Select MethodSpecificAlgo
* Select network
* Enter the publicKeyHex value generated in the previous step

<details>
<summary>Request</summary>

<figure><img src="../../.gitbook/assets/cheqd-did-registrar-did-payload.png"></figure>

</details>

<details>
<summary>Response</summary>

```json
{
  "didDoc": {
    "id": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
    "controller": [
      "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed"
    ],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
        "publicKeyMultibase": "z6MkteA1x9gEkJbYTe8myArT39Q5gZBmLPqsqJLeM3htytCo"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1"
    ]
  },
  "key": {
    "verificationMethodId": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
    "publicKeyHex": "d2ce308f19ee116ee810956605632ccd024bad8dedd02baf49f248d03acdaa48"
  }
}

```
</details>

Copy the `didDoc` field from the output

<br>

### Request Create Operation
Use the `/create` to publish the DID

1. Paste the contents of `didDoc` from the previous step in the `didDocument` field of the `/create` api body
    <details>
    <summary>Request</summary>

    ```json
    {
      "didDocument": {
        "id": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
        "controller": [
        "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed"
        ],
        "verificationMethod": [
        {
            "id": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
            "type": "Ed25519VerificationKey2020",
            "controller": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
            "publicKeyMultibase": "z6MkteA1x9gEkJbYTe8myArT39Q5gZBmLPqsqJLeM3htytCo"
        }
        ],
        "authentication": [
        "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1"
        ]
      }
    }
    ```
    </details>

2. Click on execute to perform the request
    <details>
    <summary>Response</summary>

    ```json
    {
    "jobId": "9e703d3b-f6a6-4fc1-8694-150609ee8ff4",
    "didState": {
        "did": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
        "state": "action",
        "action": "signPayload",
        "description": "Please sign the following payload with the keys in verificationMethod and Add the signingResponse in secret",
        "signingRequest": [
            {
                "kid": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
                "type": "Ed25519VerificationKey2020",
                "alg": "EdDSA",
                "serializedPayload": "EjZkaWQ6Y2hlcWQ6dGVzdG5ldDpkNGQ0NDA0Yi02ZjdmLTRlMDItOGQ0MS1iODFhZmIxZjA1ZWQaNmRpZDpjaGVxZDp0ZXN0bmV0OmQ0ZDQ0MDRiLTZmN2YtNGUwMi04ZDQxLWI4MWFmYjFmMDVlZCLEAQo8ZGlkOmNoZXFkOnRlc3RuZXQ6ZDRkNDQwNGItNmY3Zi00ZTAyLThkNDEtYjgxYWZiMWYwNWVkI2tleS0xEhpFZDI1NTE5VmVyaWZpY2F0aW9uS2V5MjAyMBo2ZGlkOmNoZXFkOnRlc3RuZXQ6ZDRkNDQwNGItNmY3Zi00ZTAyLThkNDEtYjgxYWZiMWYwNWVkIjB6Nk1rdGVBMXg5Z0VrSmJZVGU4bXlBclQzOVE1Z1pCbUxQcXNxSkxlTTNodHl0Q28qPGRpZDpjaGVxZDp0ZXN0bmV0OmQ0ZDQ0MDRiLTZmN2YtNGUwMi04ZDQxLWI4MWFmYjFmMDVlZCNrZXktMWIkMjQ3MGZlOWEtMTQ3Ny00M2E0LTk1ZWYtZDg2ZTYxZTA1NWJi"
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
    "data": "EjZkaWQ6Y2hlcWQ6dGVzdG5ldDpkNGQ0NDA0Yi02ZjdmLTRlMDItOGQ0MS1iODFhZmIxZjA1ZWQaNmRpZDpjaGVxZDp0ZXN0bmV0OmQ0ZDQ0MDRiLTZmN2YtNGUwMi04ZDQxLWI4MWFmYjFmMDVlZCLEAQo8ZGlkOmNoZXFkOnRlc3RuZXQ6ZDRkNDQwNGItNmY3Zi00ZTAyLThkNDEtYjgxYWZiMWYwNWVkI2tleS0xEhpFZDI1NTE5VmVyaWZpY2F0aW9uS2V5MjAyMBo2ZGlkOmNoZXFkOnRlc3RuZXQ6ZDRkNDQwNGItNmY3Zi00ZTAyLThkNDEtYjgxYWZiMWYwNWVkIjB6Nk1rdGVBMXg5Z0VrSmJZVGU4bXlBclQzOVE1Z1pCbUxQcXNxSkxlTTNodHl0Q28qPGRpZDpjaGVxZDp0ZXN0bmV0OmQ0ZDQ0MDRiLTZmN2YtNGUwMi04ZDQxLWI4MWFmYjFmMDVlZCNrZXktMWIkMjQ3MGZlOWEtMTQ3Ny00M2E0LTk1ZWYtZDg2ZTYxZTA1NWJi",
    "encoding": "base64"
    }

    Result : "1ubhK2CRWfB4AsqzsxVeoVwZ2yb7OpNMOe7assJXhnitoPkClm5ZFjT9zV7s6OETMKSVsUtFwIp4XCoj2xl4Bw"
```
</details>


**NOTE: If there are n verification methods for the controller then n signatures are required to publish a DID**


Copy the Result value from the response

<br>

### Submit Signature

Use the `/create` api again

1. Create the payload using the noted 
    * jobId
    * verificationMethodId
    * signatue

    <details>
    <summary>Request</summary>

    ```json
    {
        "jobId": "9e703d3b-f6a6-4fc1-8694-150609ee8ff4",
        "options": {
            "network": "testnet"
        },
        "secret": {
            "signingResponse": [
            {
                "verificationMethodId": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
                "signature": "1ubhK2CRWfB4AsqzsxVeoVwZ2yb7OpNMOe7assJXhnitoPkClm5ZFjT9zV7s6OETMKSVsUtFwIp4XCoj2xl4Bw"
            }
            ]
        }
    }
    ```
    </details>

2. Click on Execute

    <details>
    <summary>Response</summary>

    ```json
    {
    "jobId": "9e703d3b-f6a6-4fc1-8694-150609ee8ff4",
    "didState": {
        "did": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
        "state": "finished",
        "secret": {
        "signingResponse": [
            {
            "verificationMethodId": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
            "signature": "1ubhK2CRWfB4AsqzsxVeoVwZ2yb7OpNMOe7assJXhnitoPkClm5ZFjT9zV7s6OETMKSVsUtFwIp4XCoj2xl4Bw"
            }
        ]
        },
        "didDocument": {
        "id": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
        "controller": [
            "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed"
        ],
        "verificationMethod": [
            {
            "id": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1",
            "type": "Ed25519VerificationKey2020",
            "controller": "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed",
            "publicKeyMultibase": "z6MkteA1x9gEkJbYTe8myArT39Q5gZBmLPqsqJLeM3htytCo"
            }
        ],
        "authentication": [
            "did:cheqd:testnet:d4d4404b-6f7f-4e02-8d41-b81afb1f05ed#key-1"
        ]
        }
      }
    }
    ```
    </details>

3. The state in didState should be `finished` in the response, the DID is created successfully



