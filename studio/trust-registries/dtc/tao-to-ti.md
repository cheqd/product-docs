---
description: >-
  Learn about structuring Verifiable Accreditations from a Trusted Accreditation
  Organisation (TAO) to a Trusted Issuer (TI) on cheqd.
---

# TAO -> Trusted Issuer (TI)

A **Trusted Accreditation Organisation (TAO)** can delegate authority to **Trusted Issuers (TIs)** by issuing them **Verifiable Accreditations**. These accreditations grant permission to issue specific types of **Verifiable Attestations** — such as diplomas, licences, or professional credentials — within the boundaries of a defined governance framework.

The Verifiable Accreditation should include:

| Field              | Description                                                                                                                    | Example                                                |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------ |
| Issuer             | DID of the TAO                                                                                                                 | did:cheqd:testnet:e66a9416-d03e-4ced-95e3-07af16e25bc5 |
| Subject            | DID of the Trusted Issuer that is being accredited                                                                             | did:cheqd:testnet:f6e731f0-5bfb-429b-b2c7-e65a951d7b5e |
| Credential Subject | A set of structured permissions around what credentials the Trusted Issuer is accredited to issue, and in which jurisdiction.  | See below                                              |
| Terms of use       | A set of policies setting out the scope of Trust Chain for Relying parties to validate against.                                | See below                                              |

## Permissions

Root TAOs can set permissions under which TAOs must abide. This creates a level of codified governance for the trust ecosystem.

```json
"credentialSubject": {
    "id": "did:cheqd:testnet:e66a9416-d03e-4ced-95e3-07af16e25bc5",
    "accreditedFor": [
      {
        "schemaId": "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c/resources/b10146d7-0d0f-41e0-8ee3-c76db64890be",
        "types": [
          "VerifiableCredential",
          "VerifiableAttestation",
          "DiplomaCredential"
        ],
        "limitJurisdiction": "https://publications.europa.eu/resource/authority/atu/FIN"
      }
    ]
  },

```

Field descriptions:

| Field               | Description                                                    |
| ------------------- | -------------------------------------------------------------- |
| `id`                | The DID of the Trusted Issuer receiving the accreditation      |
| `schemaId`          | The schema the TI is authorised to use for issuing credentials |
| `types`             | Credential types the TI is allowed to issue                    |
| `limitJurisdiction` | (Optional) Limits issuance to a specific jurisdiction          |

## Policies

The `termsOfUse` field contains the **AccreditationPolicy**, which provides governance context by linking to both the TAO’s parent accreditation and the root authorisation of the trust chain.

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

For all Verifiable Accreditations, the accreditations are stored as DID-Linked Resources (DLRs), linked to the DID of the Accreditor. This means that the Accreditations are publically available, fully DID resolvable and are signed by the `authentication` keys within the DID Document of the Accreditor.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DID-Linked Resources</strong></mark></td><td>Learn about cheqd's approach to implementing DID-Linked Resources</td><td><a href="../../../architecture/adr-list/adr-002-did-linked-resources.md">adr-002-did-linked-resources.md</a></td></tr></tbody></table>

To issue a Verifiable Accreditation, follow the tutorial below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Verifiable Accreditation</strong></mark></td><td>Issue a Verifiable Accreditation to start a trust registry on cheqd</td><td><a href="../set-up/issue-accreditation.md">issue-accreditation.md</a></td></tr></tbody></table>
