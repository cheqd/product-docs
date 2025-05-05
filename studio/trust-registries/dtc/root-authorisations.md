---
description: Learn about establishing Root Authorisations for Trust Registries on cheqd.
---

# Root Authorisations

## What is a Root Authorisation?

A **Root Authorisation** — formally a **Verifiable Authorisation for Trust Chain** — defines the **governance framework and trust rules** for an entire decentralized trust ecosystem.

It serves as the **starting point** for all Verifiable Accreditations and Verifiable Credentials issued within a trust chain.\
Every accreditation and attestation must ultimately trace back to a valid Root Authorisation to establish its legitimacy.

The Root Authorisation **anchors** the root entity — the **Root Trusted Accreditation Organisation (rTAO)** — to a specific **Trust Framework Policy**, and enables verifiers to traverse the full chain of trust.

***

### Purpose of a Root Authorisation

| **Function**      | **Description**                                                                                         |
| ----------------- | ------------------------------------------------------------------------------------------------------- |
| Define governance | Specifies the trust framework, operational rules, and any regulatory requirements for the ecosystem     |
| Anchor trust      | Establishes a verifiable starting point for all trust chains                                            |
| Enable validation | Allows verifiers to confirm that any credential ultimately aligns with an approved governance framework |

***

### Key Characteristics

* **Credential Type**:\
  Must be of type `VerifiableAuthorisationForTrustChain`.
* **Issuer**:\
  The DID of the Root Trusted Accreditation Organisation (rTAO).
* **Credential Subject**:\
  The DID being authorised — this can either be:
  * **The same DID as the issuer** (self-authorisation), or
  * **A different DID** (delegated root authority to another trusted organisation).
* **Terms of Use**:\
  Must include a **TrustFrameworkPolicy**, referencing:
  * The name of the governance framework
  * A link (URL) to the full, published governance framework document

***

### Required Fields

| **Field**              | **Description**                                                                                             | **Example**                                              |
| ---------------------- | ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| **Issuer**             | DID of the rTAO                                                                                             | `did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e` |
| **Credential Subject** | DID of the entity being root-authorised (same as issuer for self-authorisation, or a different trusted DID) | `did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad` |
| **termsOfUse**         | Must include a `TrustFrameworkPolicy` with a governance framework reference                                 | See Policies                                             |

***

### Example of a Root Authorisation

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1"
  ],
  "issuer": {
    "id": "did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213"
  },
  "type": [
    "VerifiableCredential",
    "VerifiableAuthorisationForTrustChain"
  ],
  "issuanceDate": "2025-04-01T07:19:55.000Z",
  "credentialSubject": {
    "id": "did:cheqd:testnet:0a35d559-00ff-41b6-81ad-f64faa522771",
    "accreditedFor": [
      {
        "schemaId": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213?resourceName=AIAgentAuthorisation&resourceType=JSONSchemaValidator2020",
        "types": [
          "VerifiableCredential",
          "AIAgentAuthorisation"
        ]
      },
      {
        "schemaId": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAccreditation&resourceType=JSONSchemaValidator2020",
        "types": [
          "VerifiableCredential",
          "VerifiableAccreditation",
          "VerifiableAccreditationToAccredit"
        ]
      },
      {
        "schemaId": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAttestation&resourceType=JSONSchemaValidator2020",
        "types": [
          "VerifiableCredential",
          "VerifiableAttestation",
          "VerifiableAccreditationToAttest"
        ]
      }
    ]
  },
  "termsOfUse": {
    "type": "TrustFrameworkPolicy",
    "trustFramework": "DAIAA Governance Framework",
    "trustFrameworkId": "https://medium.com/quantum-economics/why-we-started-the-decentralized-ai-agent-alliance-6eb0938d7bc5"
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9..."
  }
}
```

***

### Important Notes

* **Self-Authorisation**:\
  When the issuer and subject are the **same DID**, the rTAO self-declares adherence to the trust framework.
* **Delegated Root Authorisation**:\
  When the subject is a **different DID**, the rTAO is immediately empowering another trusted entity to operate under the framework.
* **Policy Binding**:\
  All downstream Verifiable Accreditations and Attestations must reference a chain of authorisations and accreditations **back to this Root Authorisation**.
* **DID-Linked Resource**:\
  The Root Authorisation should be published as a **DID-Linked Resource (DLR)** attached to the rTAO’s DID for discoverability and validation.

***

### Visual Flow

```plaintext
Root Authorisation (rTAO defines framework)
         ↓
Verifiable Accreditation (TAO is authorised to operate)
         ↓
Verifiable Accreditation (Trusted Issuer is authorised)
         ↓
Verifiable Credential (End-user receives attestation)
```

***

### Summary

| **Concept**     | **Root Authorisation**                               |
| --------------- | ---------------------------------------------------- |
| Defines         | The trust framework and governance for the ecosystem |
| Issued by       | rTAO                                                 |
| Subject         | Either rTAO itself or another trusted entity         |
| Credential Type | `VerifiableAuthorisationForTrustChain`               |
| Linked Policy   | Trust Framework Policy                               |
