---
description: Anchor DIDs in DNS records and validate trust chains using TRAIN.
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Get Started with TRAIN

## What is TRAIN?

**TRAIN (TRust mAnagement INfrastructure)** is a framework, led by the team at the [Fraunhofer Institute](https://www.hci.iao.fraunhofer.de/), for establishing and validating decentralized trust. It allows ecosystems to verify whether **Verifiable Credentials (VCs)** were issued by authorized and trustworthy entities through cryptographically linked **trust chains**.

TRAIN includes two core components:

* **TRAIN Trust Validator (TTV)**: A service that validates the issuer of a Verifiable Credential by tracing Verifiable Accreditations up to a trusted root authority.
* **TDZM (Trust-DNS Zone Manager)**: A DNS component that enables **Root Trusted Accreditation Organisations (rTAOs)** to publicly anchor their Decentralized Identifiers (DIDs) in DNS.

Together, these components allow for **governance-aware, high-assurance validation** of digital credentials without centralized trust registries.

***

### How TDZM and the TRAIN Trust Validator Work Together

| Component                       | Purpose                                                                                         |
| ------------------------------- | ----------------------------------------------------------------------------------------------- |
| **TDZM**                        | Anchors rTAO DIDs in DNS, establishing a verifiable and auditable trust root                    |
| **TTV** (TRAIN Trust Validator) | Validates VCs by following Verifiable Accreditations and optionally confirming the rTAO via DNS |

When combined, they allow you to:

* Establish a cryptographically linked trust hierarchy
* Publish root DIDs (rTAOs) in DNS
* Automatically validate credentials against published governance frameworks
* Support scalable, decentralized ecosystems without compromising on assurance

***

## Step-by-Step: Setting Up Trust and Validation

### 1. Deploy **Trust-DNS Zone Manager (**&#x54;DZM)

Run the **TDZM backend and UI** using:

* **Docker Compose** (for testing or development)
* **Helm Charts in Kubernetes** (for production)

TDZM includes:

* A DNS nameserver to manage your trust zone
* A backend API and UI for managing records
* Optional OIDC authentication

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Deploy TRAIN and Anchor rTAO in DNS</strong></mark></td><td>Add high assurance to your root DID, anchoring it within a DNS record.</td><td><a href="deploy.md">deploy.md</a></td></tr></tbody></table>

***

### 2. Delegate DNS Control to TDZM

In your parent DNS zone (e.g. `federation1.com`):

* Add an **NS record** pointing your trust subdomain (e.g. `trust.federation1.com`) to TDZM
* Add an **A record** to resolve the nameserver’s domain to its IP

Example:

```
trust.federation1.com. NS ns1.trust.federation1.com.
ns1.trust.federation1.com. A 203.0.113.10
```

***

### 3. Anchor the rTAO DID in DNS

Use TDZM to publish a **TXT or TLSA DNS record** that links your **rTAO’s DID** to the trust domain.

Example:

```
_did.trust.federation1.com. TXT "did:cheqd:mainnet:rtao123"
```

This enables the TRAIN Trust Validator to resolve and verify the rTAO’s authenticity.

***

### 4. Build the Trust Chain

* Publish a **Root Authorisation for Trust Chain** from the rTAO
* Issue **Verifiable Accreditations** from rTAO → TAOs → Trusted Issuers
* Define governance rules and credential schema policies as needed

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up Trust Chain</strong></mark></td><td>Design and build a trust chain for establishing a trust hierarchy in your ecosystem.</td><td><a href="../set-up/">set-up</a></td></tr></tbody></table>

***

### 5. Use the TRAIN Trust Validator (TTV)

Send a JSON request to TTV with the credential’s issuer, type, accreditation path, and optional DNS anchors. TTV will:

* Traverse the Verifiable Accreditation chain
* Verify structural and policy compliance
* Optionally confirm the root via **DNS lookups**
* Return a structured verification result

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Validate Trust Chain</strong></mark></td><td>Validate Trust Chain to a root of trust using the TRAIN Trust Validator (TTV).</td><td><a href="validate.md">validate.md</a></td></tr></tbody></table>

***

### Summary

| Goal                    | Component                      |
| ----------------------- | ------------------------------ |
| Anchor rTAO in DNS      | 🌐 TDZM                        |
| Manage trust zones      | 🛠️ TDZM Backend & UI          |
| Define & delegate trust | 📜 Verifiable Accreditations   |
| Validate credentials    | 🔎 TRAIN Trust Validator (TTV) |

***

By combining DNS-based assurance with credential-level verification, the **TRAIN infrastructure** provides a flexible and scalable solution for **decentralized trust governance**.
