# Set up Trust Chain

### Overview

A **Trust Chain** is a hierarchical structure of **Verifiable Accreditations (VAs)** that connects a **Trusted Issuer** to a **Root Trusted Accreditation Organisation (rTAO)**. This structure allows credentials to be verified as trustworthy using tools like TRAIN, by tracing authority through cryptographic delegation.

Each step in the chain is formalised using a **Verifiable Accreditation**, while the root is anchored using a **Root Authorisation for Trust Chain**, which establishes the governance framework of the ecosystem.

If you're ready to issue your first accreditation, skip ahead:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Verifiable Accreditation</strong></mark></td><td>Issue a type of Verifiable Accreditation, including authorisations for the trust chain, and subordinate accreditations</td><td><a href="issue-accreditation.md">issue-accreditation.md</a></td></tr></tbody></table>

### Why Build a Trust Chain?

Trust Chains enable decentralized ecosystems to:

* Delegate authority without centralized registries
* Define and enforce governance frameworks
* Enable **TRAIN** to validate credentials against trusted policies
* Optionally anchor trust using **DNS or X.509** proofs

This is especially useful in domains like education, health, supply chain, or finance where **hierarchical authority** is well established.

***

### Key Roles in a Trust Chain

| **Role**                                           | **Description**                                                                                                                                         |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **rTAO** (Root Trusted Accreditation Organisation) | The top-level, highly trusted entity (e.g. government agency or standards body). It defines the governance framework and issues the root authorisation. |
| **TAO** (Trusted Accreditation Organisation)       | An intermediary entity that is accredited by the rTAO or another TAO. It may accredit further entities.                                                 |
| **Trusted Issuer**                                 | An entity accredited by a TAO or rTAO to issue **Verifiable Credentials** to holders.                                                                   |

***

### Trust Chain Structure

```
Root Authorisation for Trust Chain (published by rTAO)
    ↓
Verifiable Accreditation from rTAO to TAO
    ↓
Verifiable Accreditation from TAO to Trusted Issuer
    ↓
Verifiable Credential (Attestation) issued to subject
```

***

### Steps to Set Up a Trust Chain

#### 1. Create an rTAO DID

Register a DID to represent your **Root Trusted Accreditation Organisation (rTAO)**. This should be a recognised, high-trust entity.

Optionally, **anchor this DID in DNS** using a TXT or TLSA record for added assurance in tools like TRAIN.

#### 2. Publish a Root Authorisation for Trust Chain

Before issuing any accreditations, the rTAO must publish a **Root Authorisation for Trust Chain**, which includes:

* A URI for the **governance framework**
* A human-readable **trust framework ID**
* Supported credential schemas for the ecosystem

This authorisation forms the root of the trust graph and is referenced by all downstream Verifiable Accreditations.

#### 3. Issue Verifiable Accreditations (VAs)

Use the rTAO to issue a Verifiable Accreditation to a **TAO**. This VA should:

* Reference the Root Authorisation
* Define the scope of trust (e.g. what credential types or domains the TAO can operate in)
* Optionally include expiration or other constraints

👉 [**Issue Verifiable Accreditations**](issue-accreditation.md)

#### 4. Delegate Further to Trusted Issuers

Each TAO may issue Verifiable Accreditations to one or more **Trusted Issuers**, who are responsible for issuing actual Verifiable Credentials to end-users.

***

### Example: Education Trust Chain

```
TAO: did:cheqd:gov-edu                    ← Department of Education
    └── Root Authorisation → "cheqd Governance Framework"
    ↓
TAO:  did:cheqd:state-certifier            ← State Certification Body
    ↓
Trusted Issuer: did:cheqd:university-123   ← Accredited University
    ↓
Verifiable Credential: Bachelor of Science
```

Each entity is linked by a signed **Verifiable Accreditation**, and all references point back to the initial **Root Authorisation for Trust Chain**.

***

### Optional: DNS Anchoring for rTAOs

The rTAO can include **DNSTrustFrameworkPointers** in its DID Document or Root Authorisation. These enable validators like TRAIN to confirm:

* Domain control
* Authenticity of the rTAO’s identity
* Consistency with the governance framework published at that domain
