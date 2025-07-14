---
description: Set up your Decentralized Trust Chain (DTC) on cheqd.
---

# Set up Trust Chain

## Overview

A **Trust Chain** is a hierarchical structure of **Verifiable Accreditations (VAs)** that connects a **Trusted Issuer** to a **Root Trusted Accreditation Organisation (rTAO)**. This structure allows credentials to be verified as trustworthy using tools like TRAIN, by tracing authority through cryptographic delegation.

Each step in the chain is formalised using a **Verifiable Accreditation**, while the root is anchored using a **Root Authorization for Trust Chain**, which establishes the governance framework of the ecosystem.

If you're ready to issue your first accreditation, skip ahead to use cheqd Studio:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Verifiable Accreditation</strong></mark></td><td>Issue a type of Verifiable Accreditation, including authorizations for the trust chain, and subordinate accreditations</td><td><a href="issue-accreditation.md">issue-accreditation.md</a></td></tr></tbody></table>

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
| **rTAO** (Root Trusted Accreditation Organisation) | The top-level, highly trusted entity (e.g. government agency or standards body). It defines the governance framework and issues the root authorization. |
| **TAO** (Trusted Accreditation Organisation)       | An intermediary entity that is accredited by the rTAO or another TAO. It may accredit further entities.                                                 |
| **Trusted Issuer**                                 | An entity accredited by a TAO or rTAO to issue **Verifiable Credentials** to holders.                                                                   |

***

### Trust Chain Structure

```
Root Authorization for Trust Chain (published by rTAO)
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

#### 2. Publish a Root Authorization for Trust Chain

Before issuing any accreditations, the rTAO must publish a **Root Authorization for Trust Chain**, which includes:

* A URI for the **governance framework**
* A human-readable **trust framework ID**
* Supported credential schemas for the ecosystem

This authorization forms the root of the trust graph and is referenced by all downstream Verifiable Accreditations.

#### 3. Issue Verifiable Accreditations (VAs)

Use the rTAO to issue a Verifiable Accreditation to a **TAO**. This VA should:

* Reference the Root Authorization
* Define the scope of trust (e.g. what credential types or domains the TAO can operate in)
* Optionally include expiration or other constraints

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Verifiable Accreditation</strong></mark></td><td>Issue a type of Verifiable Accreditation, including authorizations for the trust chain, and subordinate accreditations</td><td><a href="issue-accreditation.md">issue-accreditation.md</a></td></tr></tbody></table>

#### 4. Delegate Further to Trusted Issuers

Each TAO may issue Verifiable Accreditations to one or more **Trusted Issuers**, who are responsible for issuing actual Verifiable Credentials to end-users.

***

### Example: Education Trust Chain

```
TAO: did:cheqd:gov-edu                    ← Department of Education
    └── Root Authorization → "cheqd Governance Framework"
    ↓
TAO:  did:cheqd:state-certifier            ← State Certification Body
    ↓
Trusted Issuer: did:cheqd:university-123   ← Accredited University
    ↓
Verifiable Credential: Bachelor of Science
```

Each entity is linked by a signed **Verifiable Accreditation**, and all references point back to the initial **Root Authorization for Trust Chain**.

***

## Optional: DNS Anchoring for rTAOs

In decentralized ecosystems, trust can be strengthened by combining blockchain-based identity with traditional Web PKI. To support this, **Root Trusted Accreditation Organisations (rTAOs)** can anchor their DIDs in DNS records, enabling domain-level verification of the root of the trust chain.

### Why Anchor Your rTAO in DNS?

Anchoring a DID in DNS provides:

* 🔐 **Cryptographic proof of domain control**
* 🌍 **Public discoverability and auditability** of the rTAO’s identity
* ✅ **Higher assurance in trust chain validation**, especially for public sector or federated environments
* 🤝 **Interoperability** with tools like [TRAIN](../train/), which can validate trust chains using DNS lookups

This optional step is highly recommended if your governance model involves domain ownership or if trust must be externally verifiable.

***

### How It Works: TDZM (Trust-DNS Zone Manager)

**TDZM** is a component that manages DNS zones where rTAOs can publish their DIDs as TXT or TLSA records. It integrates with DNS infrastructure to serve trust metadata for automated validation.

TRAIN uses TDZM to verify that:

* The rTAO controls the claimed domain
* The DID used in the trust chain is anchored in DNS
* The governance framework is consistently represented

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Deploy TRAIN and Anchor rTAO in DNS</strong></mark></td><td>Add high assurance to your root DID, anchoring it within a DNS record.</td><td><a href="../../../.gitbook/assets/fraunhofer.png">fraunhofer.png</a></td><td><a href="../train/deploy.md">deploy.md</a></td></tr></tbody></table>
