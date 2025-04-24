---
description: Paying an Issuer to unlock a Credential Status
---

# Verifier pays Issuer

## Paying an Issuer to unlock a Credential Status

A Verifier may need to pay an Issuer to unlock Status information about the presented Credential. Without meeting the payment conditions, the Verifier will not be able to ascertain whether the Credential has been revoked (or suspended) or not.&#x20;

A Verifier can choose to make a payment to the Issuer if they want to obtain this extra Credential Status information to build a higher level of trust within the Credential presented to them.

### Step 1: Locating Issuers' payment address and conditions

If a Verifier wants to unlock access to the Resource, to gain access to additional information about a Credential presented to them, such as the Credential Status, firstly, the Verifier will be presented Credential, including a link to the StatusList within the `"credentialStatus"` section of the Credential body.

Through following the link in`"credentialStatus"` section of the Credential body. the The Verifier will be directed to an on-ledger Resource, identifiable through a DID URL, for example:&#x20;

{% embed url="https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99?resourceName=revocation-list-encrypted-inverse-timelock&resourceType=StatusList2021Revocation" %}
Example of encrypted Status List identifiable via a DID URL
{% endembed %}

This on-ledger Resource will contain:

* An encrypted potion of the Resource, such as a Status List bitstring. identified by the `"encodedList"` property.
* Unencrypted metadata about the Resource, including the issuers' payment address `"feePayerAddress"` and and the Payment Conditions, `"feePaymentAmount"` and `"intervalInSeconds"`.

This gives the verifier requisite information they need in order to pay the Issuer to unlock the Credential Status. You can learn more about Access Control Conditions below.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Understanding Access Control Conditions</strong></mark></td><td>Learn what different Payment Conditions mean and how they affect a Verifier when verifying Credentials.</td><td><a href="../../../studio/payments/learn/access-control-conditions.md">access-control-conditions.md</a></td></tr></tbody></table>

### Step 2: Pay the Issuer

Using one of [cheqd's supported wallets](../../../network/wallets/), Verifiers can make a payment of the amount specified in the `"feePaymentAmount"` to the `"feePayerAddress"`.

This payment should be made in CHEQ.&#x20;

{% hint style="info" %}
Note that the `"feePaymentAmount"` may be specified in ncheq. This is lowest denomination of the CHEQ token, "nano" CHEQ which is 1 x 10^-9 CHEQ.
{% endhint %}

### Step 3: Constructing a payload to verify the Credential

Once the Verifier makes a payment of the specified amount in the payment conditions back to the Issuer, the [Access Control Conditions](../../../studio/payments/learn/access-control-conditions.md) will be met.

{% hint style="info" %}
Note the `"intervalInSeconds"` which indicates how long after making the payment the Verifier has to verify the Credential.&#x20;
{% endhint %}

The Verifier should input the presented Credential into a payload file, including the parameter:

`"fetchList": true`

This indicates that the Verifier wants to claim that they have met the Access Control Conditions, and for the nodes to query whether the Access Control Condition has been met. For example:

<details>

<summary>Example Request Format</summary>

```json
{
    "credential": {
        "credentialSubject": {
            "name": "eengineer1",
            "id": "did:key:z6MkvG4dpKVpYwYqnwcjRdw8VZ3km4Sisgxm1igaPCFzskxe"
        },
        "issuer": {
            "id": "did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99"
        },
        "type": [
            "VerifiableCredential"
        ],
        "credentialStatus": {
            "id": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99?resourceName=revocation-list-encrypted-inverse-timelock&resourceType=StatusList2021Revocation#79444",
            "type": "StatusList2021Entry",
            "statusPurpose": "revocation",
            "statusListIndex": "79444"
        },
        "@context": [
            "https://www.w3.org/2018/credentials/v1",
            "https://veramo.io/contexts/profile/v1",
            "https://w3id.org/vc-status-list-2021/v1"
        ],
        "issuanceDate": "2023-06-30T10:04:01.000Z",
        "proof": {
            "type": "JwtProof2020",
            "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdmVyYW1vLmlvL2NvbnRleHRzL3Byb2ZpbGUvdjEiLCJodHRwczovL3czaWQub3JnL3ZjLXN0YXR1cy1saXN0LTIwMjEvdjEiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCJdLCJjcmVkZW50aWFsU3ViamVjdCI6eyJuYW1lIjoiZWVuZ2luZWVyMSJ9LCJjcmVkZW50aWFsU3RhdHVzIjp7ImlkIjoiaHR0cHM6Ly9yZXNvbHZlci5jaGVxZC5uZXQvMS4wL2lkZW50aWZpZXJzL2RpZDpjaGVxZDp0ZXN0bmV0OjMyMjc2MWVhLTU4N2QtNDU0YS1hOTU1LTc0NTIwMDMwMWI5OT9yZXNvdXJjZU5hbWU9cmV2b2NhdGlvbi1saXN0LWVuY3J5cHRlZC1pbnZlcnNlLXRpbWVsb2NrJnJlc291cmNlVHlwZT1TdGF0dXNMaXN0MjAyMVJldm9jYXRpb24jNzk0NDQiLCJ0eXBlIjoiU3RhdHVzTGlzdDIwMjFFbnRyeSIsInN0YXR1c1B1cnBvc2UiOiJyZXZvY2F0aW9uIiwic3RhdHVzTGlzdEluZGV4IjoiNzk0NDQifX0sInN1YiI6ImRpZDprZXk6ejZNa3ZHNGRwS1ZwWXdZcW53Y2pSZHc4Vloza200U2lzZ3htMWlnYVBDRnpza3hlIiwibmJmIjoxNjg4MTE5NDQxLCJpc3MiOiJkaWQ6Y2hlcWQ6dGVzdG5ldDozMjI3NjFlYS01ODdkLTQ1NGEtYTk1NS03NDUyMDAzMDFiOTkifQ.fWCayAbmL4qivVfMz97o6tEFN2W411OFaF3wgZAb5gqoBkJ1uoV2Cp_31LI60ehSPXcDXiIUmDJbFP3S8bnNDg"
        }
    },
    "fetchList": true
}

```

</details>

### Step 4: Submitting the Credential verify transaction

Using the Veramo CLI. Verifiers can submit the following transaction, alongside the payload file to verify the Credential:

```bash
veramo execute -m cheqdVerifyCredential --argsFile path/to/payload.json
```

If successful, the Verifier will obtain the keys to decrypt the Status List and access the Credential Status information. The Verifier will receive a response indicating whether the Credential:

1. Is verified and untampered
2. Has been revoked (or suspended) or not

<details>

<summary>Example Response Format</summary>

```json
Result : {
  "verified": true,
  "payload": {
    "vc": {
      "@context": [
        "https://www.w3.org/2018/credentials/v1",
        "https://veramo.io/contexts/profile/v1",
        "https://w3id.org/vc-status-list-2021/v1"
      ],
      "type": [
        "VerifiableCredential"
      ],
      "credentialSubject": {
        "name": "tweeddalex"
      },
      "credentialStatus": {
        "id": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404?resourceName=testing-payment-rails&resourceType=StatusList2021Revocation#123979",
        "type": "StatusList2021Entry",
        "statusPurpose": "revocation",
        "statusListIndex": "123979"
      }
    },
    "sub": "did:key:z6MkvG4dpKVpYwYqnwcjRdw8VZ3km4Sisgxm1igaPCFzskxe",
    "nbf": 1692322793,
    "iss": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404"
  },
  "didResolutionResult": {
    "didDocumentMetadata": {
      "created": "2023-08-17T08:26:26Z",
      "versionId": "c9fed6ca-22c5-4656-af06-dcfe41e4b3b5",
      "linkedResourceMetadata": [
        {
          "resourceURI": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404/resources/08990db4-b759-4b7b-bd9b-823ccddbab83",          "resourceCollectionId": "cc8e5d9f-05f8-4f09-93c5-b9dba4b45404",
          "resourceId": "08990db4-b759-4b7b-bd9b-823ccddbab83",
          "resourceName": "testing-payment-rails",
          "resourceType": "StatusList2021Revocation",
          "mediaType": "application/json",
          "resourceVersion": "2023-08-17T08:39:47.206Z",
          "created": "2023-08-17T08:39:52Z",
          "checksum": "3a505b5902a797c892a1e88e4bc086067f292df65b3b2c63b404f6f4c2d84e02",
          "previousVersionId": null,
          "nextVersionId": "fa9ff872-64c3-4045-95fb-bba50b25fcc1"
        },
        {
          "resourceURI": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404/resources/fa9ff872-64c3-4045-95fb-bba50b25fcc1",          "resourceCollectionId": "cc8e5d9f-05f8-4f09-93c5-b9dba4b45404",
          "resourceId": "fa9ff872-64c3-4045-95fb-bba50b25fcc1",
          "resourceName": "testing-payment-rails",
          "resourceType": "StatusList2021Revocation",
          "mediaType": "application/json",
          "resourceVersion": "2023-08-18T03:05:52.818Z",
          "created": "2023-08-18T03:05:54Z",
          "checksum": "9c8a18e451366cc4518c6a6db99d24705e3a15fd2b127b3ece89a7749adb2935",
          "previousVersionId": "08990db4-b759-4b7b-bd9b-823ccddbab83",
          "nextVersionId": null
        }
      ]
    },
    "didResolutionMetadata": {
      "contentType": "application/did+ld+json",
      "retrieved": "2023-08-18T03:06:40Z",
      "did": {
        "didString": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404",
        "methodSpecificId": "cc8e5d9f-05f8-4f09-93c5-b9dba4b45404",
        "method": "cheqd"
      }
    },
    "didDocument": {
      "@context": [
        "https://www.w3.org/ns/did/v1",
        "https://w3id.org/security/suites/jws-2020/v1"
      ],
      "id": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404",
      "controller": [
        "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404"
      ],
      "verificationMethod": [
        {
          "id": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404#key-1",
          "type": "JsonWebKey2020",
          "controller": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404",
          "publicKeyJwk": {
            "crv": "Ed25519",
            "kty": "OKP",
            "x": "BikmGt6Jjtpk0qCXjzhYI3ZwEL9JxgQXetg33seTPxY"
          }
        }
      ],
      "authentication": [
        "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404#key-1"
      ]
    },
    "@context": "https://w3id.org/did-resolution/v1"
  },
  "issuer": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404",
  "signer": {
    "id": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404#key-1",
    "type": "JsonWebKey2020",
    "controller": "did:cheqd:testnet:cc8e5d9f-05f8-4f09-93c5-b9dba4b45404",
    "publicKeyJwk": {
      "crv": "Ed25519",
      "kty": "OKP",
      "x": "BikmGt6Jjtpk0qCXjzhYI3ZwEL9JxgQXetg33seTPxY"
    }
  },
  "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdmVyYW1vLmlvL2NvbnRleHRzL3Byb2ZpbGUvdjEiLCJodHRwczovL3czaWQub3JnL3ZjLXN0YXR1cy1saXN0LTIwMjEvdjEiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCJdLCJjcmVkZW50aWFsU3ViamVjdCI6eyJuYW1lIjoidHdlZWRkYWxleCJ9LCJjcmVkZW50aWFsU3RhdHVzIjp7ImlkIjoiaHR0cHM6Ly9yZXNvbHZlci5jaGVxZC5uZXQvMS4wL2lkZW50aWZpZXJzL2RpZDpjaGVxZDp0ZXN0bmV0OmNjOGU1ZDlmLTA1ZjgtNGYwOS05M2M1LWI5ZGJhNGI0NTQwND9yZXNvdXJjZU5hbWU9dGVzdGluZy1wYXltZW50LXJhaWxzJnJlc291cmNlVHlwZT1TdGF0dXNMaXN0MjAyMVJldm9jYXRpb24jMTIzOTc5IiwidHlwZSI6IlN0YXR1c0xpc3QyMDIxRW50cnkiLCJzdGF0dXNQdXJwb3NlIjoicmV2b2NhdGlvbiIsInN0YXR1c0xpc3RJbmRleCI6IjEyMzk3OSJ9fSwic3ViIjoiZGlkOmtleTp6Nk1rdkc0ZHBLVnBZd1lxbndjalJkdzhWWjNrbTRTaXNneG0xaWdhUENGenNreGUiLCJuYmYiOjE2OTIzMjI3OTMsImlzcyI6ImRpZDpjaGVxZDp0ZXN0bmV0OmNjOGU1ZDlmLTA1ZjgtNGYwOS05M2M1LWI5ZGJhNGI0NTQwNCJ9.RPt5D021S0MzUscptUc0CZtK9a2sEvGYOkJkx0EO8xqQwTIQRfL7-Z-ZTbOo2OiHs-kDrXQ8SbTMf2yk1QR_Ag",
  "policies": {
    "expirationDate": true,
    "audience": true,
    "credentialStatus": false,
    "issuanceDate": true,
    "aud": true
  },
  "verifiableCredential": {
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
  },
  "revoked": false
}
```

</details>

This provides the Verifier the full information to make a trust decision, having paid the Issuer for the Credential Status information.
