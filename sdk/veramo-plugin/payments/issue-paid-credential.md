---
description: Issue Verifiable Credential with Encrypted Status List 2021 and index
---

# Issue Credential with Encrypted Status List

## Issue Verifiable Credential with Encrypted Status List 2021 Resource

There is a specific transaction type for issuing Verifiable Credentials that reference encrypted Status List Resources.&#x20;

### Step 1: Create an encrypted Status List on-ledger

Follow the tutorial here to create an encrypted Status List 2021 Resource on cheqd.

* [Charge for Status List](charge.md)

### Step 2: Choose index number for Credential

Each encrypted Status List is a [bitstring](https://en.wikipedia.org/wiki/Bit\_array), where each bit represents a Credential which is either revoked or unrevoked (or suspended or unsuspended).

{% hint style="info" %}
Users should maintain and manage a list of which Credentials match with which specific bitstring indices to be able to revoke or suspend Credentials in the future, and to avoid collisions.
{% endhint %}

When issuing a Credential, you have the choice of whether to manually specify an index for the Credential within the bitstring, or randomly generate an index number anywhere in the bitstring, or within a given range.

### Step 2: Construct Credential Payload with Status List

As mentioned above, when constructing the payload for your Credential, including the encrypted Resource, you have a choice of how you would like to specify the index.

The following parameters may be included:

| Parameter                | Value                            | Optional |
| ------------------------ | -------------------------------- | -------- |
| `"statusPurpose"`        | `"revocation"` or `"suspension"` | No       |
| `"statusListName"`       | string                           | No       |
| `"statusListVersion"`    | string                           | Yes      |
| `"statusListRangeStart"` | number                           | Yes      |
| `"statusListRangeEnd"`   | number                           | Yes      |
| `"statusListVersion"`    | number, or set of numbers        | Yes      |

Below are a set of examples of how these parameters may be included in Credential payload files for different purposes:

<details>

<summary>Example Request Format: Random Bitstring index</summary>

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
                "VerifiableCredential"
            ],
            "@context": [
                "https://www.w3.org/2018/credentials/v1",
                "https://veramo.io/contexts/profile/v1"
            ]
        }
    },
    "statusOptions": {
        "statusPurpose": "revocation",
        "statusListName": "status-list-2021-encrypted"
    }
}

```

</details>

<details>

<summary>Example Request Format: Specified Bitstring index</summary>

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
                "VerifiableCredential"
            ],
            "@context": [
                "https://www.w3.org/2018/credentials/v1",
                "https://veramo.io/contexts/profile/v1"
            ]
        }
    },
    "statusOptions": {
        "statusPurpose": "revocation",
        "statusListName": "status-list-2021-encrypted",
        "statusListIndex": 1456
    }
}

```

</details>

<details>

<summary>Example Request Format: Bitstring index within a given range</summary>

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
                "VerifiableCredential"
            ],
            "@context": [
                "https://www.w3.org/2018/credentials/v1",
                "https://veramo.io/contexts/profile/v1"
            ]
        }
    },
    "statusOptions": {
        "statusPurpose": "revocation",
        "statusListName": "status-list-2021-encrypted",
        "statusListRangeStart": 2000,
        "statusListRangeEnd": 3000
    }
}

```

</details>

<details>

<summary>Example Request Format: Bitstring including omitted bits </summary>

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
                "VerifiableCredential"
            ],
            "@context": [
                "https://www.w3.org/2018/credentials/v1",
                "https://veramo.io/contexts/profile/v1"
            ]
        }
    },
    "statusOptions": {
        "statusPurpose": "revocation",
        "statusListName": "status-list-2021-encrypted",
        "statusListRangeStart": 10,
        "statusListRangeEnd": 3000,
        "indexNotIn": 13, 1807, 2434
    }
}

```

</details>

{% hint style="info" %}
Ensure that the `"statusPurpose"` and `"statusListName"` is the same as the existing Status List on-ledger, [created in Step 1](issue-paid-credential.md#step-1-create-an-encrypted-status-list-on-ledger).&#x20;
{% endhint %}

### Step 3: Execute the transaction

Once you have constructed your payload file, and have saved it as a JSON file, use the transaction below to issue the Verifiable Credential.

```
veramo execute -m cheqdIssueRevocableCredentialWithStatusList2021 --argsFile path/to/payload.json
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
    "VerifiableCredential"
  ],
  "credentialStatus": {
    "id": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404?resourceName=testing-payment-rails&resourceType=StatusList2021Revocation#123979",
    "type": "StatusList2021Entry",
    "statusPurpose": "revocation",
    "statusListIndex": "123979"
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
