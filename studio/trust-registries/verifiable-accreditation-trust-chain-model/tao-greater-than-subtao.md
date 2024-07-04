# TAO -> SubTAO

As a Trusted Accreditation Organisation (TAO), it is possible to accredit Sub-Trusted Accreditation Organisations (SubTAOs) to issue Verifiable Accreditations or Verifiable Attestations.

The Verifiable Accreditation should include:

| Field              | Description                                                                                                            | Example                                                |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Issuer             | DID of the TAO                                                                                                         | did:cheqd:testnet:a2b675de-33d0-4044-8183-0d74f210cceb |
| Subject            | DID of the SubTAO that is being accredited                                                                             | did:cheqd:testnet:e66a9416-d03e-4ced-95e3-07af16e25bc5 |
| Credential Subject | A set of structured permissions around what credentials the SubTAO is accredited to issue, and in which jurisdiction.  | See below                                              |
| Terms of use       | A set of policies setting out the scope of Trust Chain for Relying parties to validate against.                        | See below                                              |

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

| Field             | Description                                                                                                  |
| ----------------- | ------------------------------------------------------------------------------------------------------------ |
| schemaId          | Schema of the Verifiable Accreditation that the SubTAO is accredited to issue themselves                     |
| types             | Types of Credential that the SubTAO is accredited to issue                                                   |
| limitJurisdiction | Permission that the TAO can set to limit the jurisdictional scope of the credentials issued in the ecosystem |

## Policies

The Root TAO can also set polices known as the `AccreditationPolicy` within the `termsOfUse` section of the Verifiable Accreditation.

```json
"termsOfUse": {
    "type": "AccreditationPolicy",
    "parentAccreditation": "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c/resources/18de60ec-bed1-42e5-980c-601c432bc60b",
    "rootAuthorisation": "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c/resources/18de60ec-bed1-42e5-980c-601c432bc60b",
    "trustFramework": "Name of the Governance Framework (GF)",
    "trustFrameworkId": "https://example.com/governance-framework/124"
  }

```

Whereby:

| Field               | Description                                                                       |
| ------------------- | --------------------------------------------------------------------------------- |
| type                | Must be `AccreditationPolicy`                                                     |
| parentAccreditation | The DID URL of the Accreditation issued by another TAO or the Root TAO to the TAO |
| rootAuthoroisation  | The DID URL of the Root of Trust Verifiable Authorsation                          |
| trustFramework      | Name of Governance Framework set by the Governance Authority                      |
| trustFrameworkId    | URL linking to where the written Governance Framework is stored                   |

