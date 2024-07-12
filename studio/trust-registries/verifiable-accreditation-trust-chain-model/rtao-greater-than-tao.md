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
