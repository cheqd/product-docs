---
description: >-
  Verifiable Accreditations from a Root of Trust (rTAO) to a subordinate entity
  (TAO).
---

# RTAO -> TAO

A **Root Trusted Accreditation Organisation (rTAO)** can delegate trust by issuing **Verifiable Accreditations** to **Trusted Accreditation Organisations (TAOs)**. These accreditations define the permissions and scope under which the TAO can operate.

The Verifiable Accreditation should include:

| Field              | Description                                                                                                         | Example                                                |
| ------------------ | ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Issuer             | DID of the Root of Trust (RTAO)                                                                                     | did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c |
| Subject            | DID of the TAO that is being accredited                                                                             | did:cheqd:testnet:a2b675de-33d0-4044-8183-0d74f210cceb |
| Credential Subject | A set of structured permissions around what credentials the TAO is accredited to issue, and in which jurisdiction.  | See below                                              |
| Terms of use       | A set of policies setting out the Governance Framework for the ecosystem                                            | See below                                              |

## Permissions

The `credentialSubject` of the accreditation, issued by `issuer`  defines what the TAO is authorised to do — including which credential types they can issue and in which jurisdictions.

```json
"credentialSubject": {
  "id": "did:cheqd:testnet:a2b675de-33d0-4044-8183-0d74f210cceb",
  "accreditedFor": [
    {
      "schemaId": "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c/resources/b10146d7-0d0f-41e0-8ee3-c76db64890be",
      "types": [
        "VerifiableCredential",
        "VerifiableAccreditation",
        "VerifiableAttestation",
        "VerifiableAccreditationToAccredit"
      ],
      "limitJurisdiction": "https://publications.europa.eu/resource/authority/atu/FIN"
    }
  ]
}
```

**Field descriptions:**

| Field               | Description                                                                           |
| ------------------- | ------------------------------------------------------------------------------------- |
| `schemaId`          | The schema the TAO is authorised to use when issuing accreditations or credentials    |
| `types`             | Credential types the TAO is permitted to issue                                        |
| `limitJurisdiction` | (Optional) A geographic or regulatory restriction on where the accreditation is valid |

## Policies

The Root TAO can also set polices known as the `AccreditationPolicy` within the `termsOfUse` section of the Verifiable Accreditation.

```json
"termsOfUse": {
    "type": "AccreditationPolicy",
    "parentAccreditation": "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c/resources/18de60ec-bed1-42e5-980c-601c432bc60b",
    "rootAuthorisation": "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c/resources/18de60ec-bed1-42e5-980c-601c432bc60b"
  }

```

Whereby:

| Field               | Description                                                                       |
| ------------------- | --------------------------------------------------------------------------------- |
| type                | Must be `AccreditationPolicy`                                                     |
| parentAccreditation | The DID URL of the Accreditation issued by another TAO or the Root TAO to the TAO |
| rootAuthoroisation  | The DID URL of the Root of Trust Verifiable Authorsation                          |

### Example of fully formed Accreditation

The example below shows a Verifiable Accreditation that is issued by an rTAO to a TAO, specifying a schema under which the recipient is able to use for issuing their own accreditations.

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://schema.org/schema.jsonld",
    "https://veramo.io/contexts/profile/v1"
  ],
  "issuer": {
    "id": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e"
  },
  "type": [
    "VerifiableCredential",
    "VerifiableAccreditationToAccredit"
  ],
  "issuanceDate": "2024-08-07T02:08:30.000Z",
  "credentialSubject": {
    "accreditedFor": [
      {
        "schemaId": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAccreditation&resourceType=JSONSchemaValidator2020",
        "types": [
          "VerifiableCredential",
          "VerifiableAccreditation",
          "VerifiableAttestation",
          "VerifiableAccreditationToAccredit"
        ]
      }
    ],
    "id": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad"
  },
  "termsOfUse": {
    "type": "AccreditationPolicy",
    "parentAccreditation": "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c/resources/18de60ec-bed1-42e5-980c-601c432bc60b",
    "rootAuthorisation": "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c/resources/18de60ec-bed1-42e5-980c-601c432bc60b"
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vc2NoZW1hLm9yZy9zY2hlbWEuanNvbmxkIiwiaHR0cHM6Ly92ZXJhbW8uaW8vY29udGV4dHMvcHJvZmlsZS92MSJdLCJ0eXBlIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImFjY3JlZGl0ZWRGb3IiOlt7InNjaGVtYUlkIjoiZGlkOmNoZXFkOnRlc3RuZXQ6OGVhMDM2ZGEtZjM0MC00ODBkLTg5NTItZjU1NjFlYTE3NjNjL3Jlc291cmNlcy9iMTAxNDZkNy0wZDBmLTQxZTAtOGVlMy1jNzZkYjY0ODkwYmUiLCJ0eXBlcyI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlZlcmlmaWFibGVBY2NyZWRpdGF0aW9uIiwiVmVyaWZpYWJsZUF0dGVzdGF0aW9uIiwiVmVyaWZpYWJsZUFjY3JlZGl0YXRpb25Ub0FjY3JlZGl0Il0sImxpbWl0SnVyaXNkaWN0aW9uIjoiaHR0cHM6Ly9wdWJsaWNhdGlvbnMuZXVyb3BhLmV1L3Jlc291cmNlL2F1dGhvcml0eS9hdHUvRklOIn1dfX0sInN1YiI6ImRpZDpjaGVxZDp0ZXN0bmV0OjZhZjQxMmQ3LTJmMDQtNGUxMi1hNDI0LWU2NzE5ZGI0ODdhZCIsIm5iZiI6MTcyMjk5NjUxMCwiaXNzIjoiZGlkOmNoZXFkOnRlc3RuZXQ6YjAwM2RmNmYtZWM4ZS00OGRkLTlhMmItNzAxMWM1Y2YwYTVlIn0.CrEirG-Yki2HCfY6GjNR_Oqx0ZV6uJr1NPpTTLnVWfG1Q9R3YASj7IyEZ3FtVGRmdSNhQ9v2pmPijVxeBnwPAg"
  }
}
```

For all Verifiable Accreditations, the accreditations are stored as DID-Linked Resources (DLRs), linked to the DID of the Accreditor. This means that the Accreditations are publically available, fully DID resolvable and are signed by the `authentication` keys within the DID Document of the Accreditor.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DID-Linked Resources</strong></mark></td><td>Learn about cheqd's approach to implementing DID-Linked Resources</td><td><a href="../../../architecture/adr-list/adr-002-did-linked-resources.md">adr-002-did-linked-resources.md</a></td></tr></tbody></table>

To issue a Verifiable Accreditation, follow the tutorial below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Verifiable Accreditation</strong></mark></td><td>Issue a Verifiable Accreditation to start a trust registry on cheqd</td><td><a href="../set-up/issue-accreditation.md">issue-accreditation.md</a></td></tr></tbody></table>

t
