---
description: >-
  Issue a Verifiable Credential referencing an Encrypted Status List  and index
  within the Credential Body.
---

# Issue Credential with Encrypted Status List

## Issue Verifiable Credential with Encrypted Bitstring Status List Resource

There is a specific transaction type for issuing Verifiable Credentials that reference encrypted Status List Resources.&#x20;

### Step 1: Create an encrypted Status List on-ledger

Follow the tutorial here to create an encrypted Bitstring Status List Resource on cheqd.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Charge for Status List</strong></mark></td><td>Create an encrypted Status List on cheqd with payment conditions, unlockable on payment of CHEQ</td><td><a href="charge.md">charge.md</a></td></tr></tbody></table>

### Step 2: Construct Credential Payload with Status List

{% hint style="info" %}
With Bitstring status lists, the indices are assigned as per the current usage state of the status list. Users do not need to pass index, as it may interfere with auto-assignments and return errors if mismatched.
{% endhint %}

When constructing the payload for issuing a Credential just specify the purpose and the encrypted status list name.

The following parameters may be included:

| Parameter             | Value        | Optional |
| --------------------- | ------------ | -------- |
| `"statusPurpose"`     | `"message"`  | No       |
| `"statusListName"`    | string       | No       |
| `"statusListVersion"` | string       | Yes      |

Below is an example of how these parameters may be included in Credential payload files:

<details>

<summary>Example Request Format</summary>

```json
{
    "issuanceOptions": {
        "credential": {
            "credentialSubject": {
                "name": "tweeddalex",
                "id": "did:key:z6MkvG4dpKVpYwYqnwcjRdw8VZ3km4Sisgxm1igaPCFzskxe"
            },
            "issuer": {
                "id": "did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99"
            },
            "type": [
                "VerifiableCredential", "Profile"
            ],
            "@context": [
                "https://www.w3.org/2018/credentials/v1",
                "https://veramo.io/contexts/profile/v1"
            ]
        },
        "proofFormat": "jwt"
    },
    "statusOptions": {
        "statusPurpose": "message",
        "statusListName": "status-list-encrypted"
    }
}

```

</details>

{% hint style="info" %}
Ensure that the `"statusPurpose"`  is `message` and `"statusListName"` is the same as the existing Status List on-ledger, [created in Step 1](issue-paid-credential.md#step-1-create-an-encrypted-status-list-on-ledger).&#x20;
{% endhint %}

### Step 3: Execute the transaction

Once you have constructed your payload file, and have saved it as a JSON file, use the transaction below to issue the Verifiable Credential.

```
veramo execute -m cheqdIssueCredentialWithStatusList --argsFile path/to/payload.json
```

### Response format

If the transaction is successful, you will get returned a formatted Verifiable Credential, including a populated `"credentialStatus"` section.&#x20;

<details>

<summary>Example Response Format</summary>

```json
Result : {
  "credentialSubject": {
    "name": "tweeddalex",
    "id": "did:key:z6MkvG4dpKVpYwYqnwcjRdw8VZ3km4Sisgxm1igaPCFzskxe"
  },
  "issuer": {
    "id": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404"
  },
  "type": [
    "VerifiableCredential",
     "Profile"
  ],
  "credentialStatus": {
    "id": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99?resourceName=status-list-encrypted&resourceType=BitstringStatusListCredential#1211",
    "type": "BitstringStatusListEntry",
    "statusPurpose": "message",
    "statusListIndex": "1211",
    "statusListCredential": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99?resourceName=status-list-encrypted&resourceType=BitstringStatusListCredential",
    "statusSize": 2,
    "statusMessage": [
      {
        "status": "0x0",
        "message": "valid"
      },
      {
        "status": "0x1",
        "message": "revoked"
      },
      {
        "status": "0x2",
        "message": "suspended"
      },
      {
        "status": "0x3",
        "message": "unknown"
      }
    ]
  },
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://veramo.io/contexts/profile/v1",
    "https://w3id.org/vc-status-list-2021/v1"
  ],
  "issuanceDate": "2023-08-18T01:39:53.000Z",
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdmVyYW1vLmlvL2NvbnRleHRzL3Byb2ZpbGUvdjEiLCJodHRwczovL3czaWQub3JnL3ZjLXN0YXR1cy1saXN0LTIwMjEvdjEiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCJdLCJjcmVkZW50aWFsU3ViamVjdCI6eyJuYW1lIjoidHdlZWRkYWxleCJ9LCJjcmVkZW50aWFsU3RhdHVzIjp7ImlkIjoiaHR0cHM6Ly9yZXNvbHZlci5jaGVxZC5uZXQvMS4wL2lkZW50aWZpZXJzL2RpZDpjaGVxZDp0ZXN0bmV0OmNjOGU1ZDlmLTA1ZjgtNGYwOS05M2M1LWI5ZGJhNGI0NTQwND9yZXNvdXJjZU5hbWU9dGVzdGluZy1wYXltZW50LXJhaWxzJnJlc291cmNlVHlwZT1TdGF0dXNMaXN0MjAyMVJldm9jYXRpb24jMTIzOTc5IiwidHlwZSI6IlN0YXR1c0xpc3QyMDIxRW50cnkiLCJzdGF0dXNQdXJwb3NlIjoicmV2b2NhdGlvbiIsInN0YXR1c0xpc3RJbmRleCI6IjEyMzk3OSJ9fSwic3ViIjoiZGlkOmtleTp6Nk1rdkc0ZHBLVnBZd1lxbndjalJkdzhWWjNrbTRTaXNneG0xaWdhUENGenNreGUiLCJuYmYiOjE2OTIzMjI3OTMsImlzcyI6ImRpZDpjaGVxZDp0ZXN0bmV0OmNjOGU1ZDlmLTA1ZjgtNGYwOS05M2M1LWI5ZGJhNGI0NTQwNCJ9.RPt5D021S0MzUscptUc0CZtK9a2sEvGYOkJkx0EO8xqQwTIQRfL7-Z-ZTbOo2OiHs-kDrXQ8SbTMf2yk1QR_Ag"
  }
}
```

</details>
