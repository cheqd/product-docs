# RTAO -> TAO

As a Root of Trust (RTAO) entity, it is possible to accredit Trusted Accreditation Organisations to issue Verifiable Accreditations or Verifiable Attestations.

The Verifiable Accreditation should include:

| Field              | Description                                                                                                         | Example                                                |
| ------------------ | ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Issuer             | DID of the Root of Trust (RTAO)                                                                                     | did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c |
| Subject            | DID of the TAO that is being accredited                                                                             | did:cheqd:testnet:a2b675de-33d0-4044-8183-0d74f210cceb |
| Credential Subject | A set of structured permissions around what credentials the TAO is accredited to issue, and in which jurisdiction.  | See below                                              |
| Terms of use       | A set of policies setting out the Governance Framework for the ecosystem                                            | See below                                              |

## Permissions

Root TAOs can set permissions under which TAOs must abide. This creates a level of codified governance for the trust ecosystem.

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
  },

```

Whereby:

| Field             | Description                                                                                                   |
| ----------------- | ------------------------------------------------------------------------------------------------------------- |
| schemaId          | Schema of the Verifiable Accreditation that the TAO is accredited to issue themselves                         |
| types             | Types of Credential that the TAO is accredited to issue                                                       |
| limitJurisdiction | Permission that the RTAO can set to limit the jurisdictional scope of the credentials issued in the ecosystem |

## Policies

The Root TAO can also set polices known as the `TrustFrameworkPolicy` within the `termsOfUse` section of the Verifiable Accreditation.

```json
"termsOfUse": {
    "type": "TrustFrameworkPolicy",
    "trustFramework": "Name of the Ecosystem Governance Framework (GF)",
    "trustFrameworkId": "https://example.com/governance-framework/125"
  },

```

Whereby:

| Field            | Description                                                     |
| ---------------- | --------------------------------------------------------------- |
| type             | Must be `TrustFrameworkPolicy`                                  |
| trustFramework   | Name of Governance Framework set by the Governance Authority    |
| trustFrameworkId | URL linking to where the written Governance Framework is stored |

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
    "VerifiableAuthorisationForTrustChain"
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
    "type": "TrustFrameworkPolicy",
    "trustFramework": "cheqd Governance Framework",
    "trustFrameworkId": "https://learn.cheqd.io/governance/start"
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

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Verifiable Accreditation</strong></mark></td><td>Issue a Verifiable Accreditation to start a trust registry on cheqd</td><td><a href="../set-up-trust-chain/issue-accreditation.md">issue-accreditation.md</a></td></tr></tbody></table>

