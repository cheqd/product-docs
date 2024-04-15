# Verifiable Accreditations

Verifiable Accreditations are credentials that are issued from one organisation to another organisation to "accredit" that organisation to perform a particular action. These types of credentials are stored directly on-ledger as [DID-Linked Resources](../../architecture/adr-list/adr-002-did-linked-resources.md), meaning that they are persistent, sequentially versioned and highly available.&#x20;

There are two types of Verifiable Accreditation:

| Type                                 | Description                                                                                                                                                        |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Verifiable Accreditation to Accredit | This Credential verifies that an organisation has the permissions needed to accredit other organisations for issuing a particular type of Verifiable Accredittion. |
| Verifiable Accreditation to Attest   | This Credential verifies that an organisation has the permissions needed to issue Verifiable Credentials, defined by a particular schema.                          |

For a trusted ecosystem, these attestations are required to trace the legitimacy of a credential issuer to a root-of-trust.&#x20;

### Verifiable Accreditation Schema

In order to ensure consistency and interoperability within a trusted ecosystem, Trusted Accreditation Organisations (TAOs) should define schemas for the Verifiable Accreditations for their particular ecosystem. They may also reuse existing schemas that have been created for equivalent ecosystems.

<details>

<summary>Verifiable Accreditation Schema Example</summary>

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Verifiable Accreditation Record",
  "description": "Schema of an Verifiable Accreditation",
  "type": "object",
  "allOf": [
    {
      "properties": {
        "credentialSubject": {
          "description": "Defines additional information about the subject that is described by the Verifiable Accreditation",
          "type": "object",
          "properties": {
            "id": {
              "description": "Defines a unique identifier of the Verifiable Attestation",
              "type": "string",
              "format": "uri"
            },
            "reservedAttributeId": {
              "description": "Defines the attributeId this Verifiable Accreditation has been created for",
              "type": "string"
            },
            "accreditedFor": {
              "description": "Defines a list of claims that define/determine the authorisation of an Issuer to issue certain types of VCs",
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "schemaId": {
                    "description": "Schema, registered as a DID-Linked Resource, which the accredited organisation is allowed to issue, as per their accreditation",
                    "type": "string",
                    "format": "uri"
                  },
                  "types": {
                    "type": "array",
                    "items": {
                      "type": "string"
                    }
                  },
                  "limitJurisdiction": {
                    "anyOf": [
                      {
                        "description": "Defines the jurisdiction for which the accreditation is valid",
                        "type": "string",
                        "format": "uri"
                      },
                      {
                        "type": "array",
                        "description": "Defines the jurisdictions for which the accreditation is valid",
                        "items": {
                          "type": "string",
                          "format": "uri"
                        }
                      }
                    ]
                  }
                },
                "required": ["schemaId", "types", "limitJurisdiction"]
              }
            }
          },
          "required": ["id", "reservedAttributeId", "accreditedFor"]
        },
        "credentialStatus": {
          "description": "Defines revocation details for the issued credential. Further redefined by type extension",
          "type": "object",
          "properties": {
            "id": {
              "description": "Exact identity for the credential status",
              "type": "string",
              "format": "uri"
            },
            "type": {
              "description": "Defines the revocation status type",
              "type": "string",
              "const": "AccreditationEntry"
            }
          },
          "required": ["id", "type"]
        }
      },
      "required": [
        "expirationDate",
        "credentialSubject",
        "credentialStatus",
        "termsOfUse"
      ]
    }
  ]
}

```

</details>

### Verifiable Accreditation Examples

Notably, Verifiable Accreditations are credentials that are issued to organisational DIDs on cheqd. In each Verifiable Accreditation, there is also a `reservedAttributeId` that corresponds to the `resourceId` of the Accreditation as a DID-Linked Resource.

<details>

<summary>Verifiable Accreditation to Accredit Example</summary>

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "id": "urn:uuid:8568b525-a24e-4bc0-9d97-6a8459ec0130",
  "type": [
    "VerifiableCredential",
    "VerifiableAttestation",
    "VerifiableAccreditation",
    "VerifiableAccreditationToAccredit"
  ],
  "issuer": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6",
  "issuanceDate": "2021-11-01T00:00:00Z",
  "validFrom": "2021-11-01T00:00:00Z",
  "expirationDate": "2025-06-22T14:11:44Z",
  "issued": "2020-06-22T14:11:44Z",
  "credentialSubject": {
    "id": "did:cheqd:testnet:e21b63d1-a771-4eb9-9452-869cd30fd622",
    "reservedAttributeId": "05afe541-77dd-4eda-9e01-2258c74b291b",
    "accreditedFor": [
      {
        "schemaId": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/83eb0ed8-37d1-4ba6-9e0b-40d60676d4aa",
        "types": [
          "VerifiableCredential",
          "VerifiableAttestation",
          "DiplomaCredential"
        ]
      }
    ]
  },
  "credentialStatus": {
    "id": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6?resourceName=accreditationStatus&resourceType=StatusList2021Revocation",
    "type": "StatusList2021Revocation"
  },
  "termsOfUse": [
    {
      "id": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/ab670b62-469c-4901-af7e-0ce94b5cccea",
      "type": "IssuanceCertificate"
    }
  ],
  "credentialSchema": [
    {
      "id": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/83eb0ed8-37d1-4ba6-9e0b-40d60676d4aa",
      "type": "FullJsonSchemaValidator2021"
    }
  ]
}


```

</details>

<details>

<summary>Verifiable Accreditation to Attest Example</summary>

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "id": "urn:uuid:8568b525-a24e-4bc0-9d97-6a8459ec0130",
  "type": [
    "VerifiableCredential",
    "VerifiableAttestation",
    "VerifiableAccreditation",
    "VerifiableAccreditationToAttest"
  ],
  "issuer": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6",
  "issuanceDate": "2021-11-01T00:00:00Z",
  "validFrom": "2021-11-01T00:00:00Z",
  "expirationDate": "2025-06-22T14:11:44Z",
  "issued": "2020-06-22T14:11:44Z",
  "credentialSubject": {
    "id": "did:cheqd:testnet:e21b63d1-a771-4eb9-9452-869cd30fd622",
    "reservedAttributeId": "15b49499-2a36-4c73-9f5b-7409b44ce7a3",
    "accreditedFor": [
      {
        "schemaId": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/da4159f1-ff50-4a7c-b0cb-40d3a1f71003a",
        "types": [
          "VerifiableCredential",
          "VerifiableAttestation",
          "DiplomaCredential"
        ]
    ]
  },
  "credentialStatus": {
    "id": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6?resourceName=accreditationStatusToAttest&resourceType=StatusList2021Revocation",
    "type": "EbsiAccreditationEntry"
  },
  "termsOfUse": [
    {
      "id": "https://api-test.ebsi.eu/trusted-issuers-registry/../..xyz",
      "type": "IssuanceCertificate"
    }
  ],
  "credentialSchema": [
    {
      "id": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/da4159f1-ff50-4a7c-b0cb-40d3a1f71003a",
      "type": "FullJsonSchemaValidator2021"
    }
  ]
}


```

</details>

## Verifiable Accreditations as DID-Linked Resources

Storing Verifiable Accreditations as DID-Linked Resources enables each accreditation to be individually resolvable using a DID URL. It also enables Accreditations to be "chained" to a DID, allowing the traversal of the Accreditation to the root of trust.&#x20;

The diagram below shows how DID-Linked Resources can be applied to the trust hierarchy to enable DID resolvable Verifiable Accreditations:

<figure><img src="../../.gitbook/assets/Trust Chains using DLRs.png" alt=""><figcaption></figcaption></figure>
