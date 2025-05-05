---
description: >-
  Validate the trust chain of a credential using TRAIN, to recursively traverse
  back to a Root of Trust, and determine the accreditations and authorizations
  of an issuer Decentralized Identifier (DID).
---

# Validate Trust Chain

## Overview

**TRAIN** is a trust validation service that evaluates the **trustworthiness of Verifiable Credentials (VCs)** by checking whether the **issuer's Decentralized Identifier (DID)** can be traced back to a trusted source—known as a **root of trust**.

This page explains how TRAIN performs trust validation for credentials issued within the [cheqd ecosystem](https://docs.cheqd.io/product/studio/trust-registries/dtc), using **Decentralized Trust Chains (DTCs)** to verify each step in the chain from the credential issuer to a recognized root authority.

### Get started with TRAIN TTV API

Use the TRAIN APIs below to validate your trust chain:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>TRAIN TTV API</strong></mark></td><td>Validate your Trust Chain using the TRAIN Trust Validator APIs.</td><td><a href="https://dev-train.trust-scheme.de/swagger_cheqd_train/#/TrustedContentResolver/">https://dev-train.trust-scheme.de/swagger_cheqd_train/#/TrustedContentResolver/</a></td></tr></tbody></table>

### What TRAIN Does

TRAIN's **Trust Validator (TTV)** validates credentials by:

1. Taking a Verifiable Credential (VC) as input.
2. Identifying the **issuer DID** from the credential.
3. Following the credential's **trust chain**, resolving links between DIDs, Verifiable Accreditations, and Trust Anchors.
4. Verifying whether the top-level (root) entity in the chain is a **trusted source** (e.g., DNS-anchored entity, government body, industry group).
5. Producing a trust assessment (e.g., valid/invalid, verified/unverified) that can be consumed by relying parties.

### How TRAIN Uses the cheqd Trust Anchor

Within the cheqd network, **Trust Anchors** represent root entities that can authorize other issuers via **Verifiable Accreditations**. These accreditations form **Decentralized Trust Chains**, which TRAIN evaluates to determine if a credential is trustworthy.

TRAIN integrates the **cheqd Trust Anchor model** by:

* Recognizing `VerifiableAccreditation` credentials as establishing authority between entities.
* Resolving these links recursively up the chain until it reaches a **root-level DID**.
* Checking whether the root DID is associated with a **DNS-anchored Trust Anchor** using `DNSTrustFrameworkPointer` entries.

### Validation Logic

When TRAIN evaluates a credential issued within cheqd's ecosystem, it performs the following checks:

1. **Credential Verification**\
   Validates the signature and schema of the input Verifiable Credential.
2. **Trust Chain Resolution**\
   Follows the `termsOfUse` field and associated `VerifiableAccreditation` resources to build the credential’s trust chain.
3. **Anchor Resolution**\
   Locates the root DID and evaluates whether it has a valid `DNSTrustFrameworkPointer` or other proof-of-authority reference (e.g., X.509 linkage).
4. **Anchor Validation**\
   Confirms that the root DID’s association with a domain name is cryptographically anchored using DNS-based proofs (e.g., DNS TXT records or TLSA).
5. **Policy Compliance (optional)**\
   Validates that the trust chain complies with local or domain-specific policy requirements (e.g., only accept credentials rooted in `.gov` domains).

## TRAIN Trust Validator (TTV) Request Format&#x20;

The following fields are used when submitting a request to the **TRAIN Trustworthiness Validator (TTV)**. This validator checks whether the issuer of a Verifiable Credential is trustworthy by tracing their accreditations up a trust chain. The fields below are passed as JSON in the request body and instruct TRAIN on how to resolve and validate the issuer's authority and compliance with a defined trust policy.

| **Field**                   | **Required** | **Description**                                                                                                                                    | **Example Value**                                                                                                                             |
| --------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `issuer`                    | ✅ Yes        | The **DID of the issuer** of the Verifiable Credential being validated. Must be a valid `did:cheqd` DID.                                           | `did:cheqd:testnet:975f1941-9313-41d4-ac8b-88fedda7ce80`                                                                                      |
| `type`                      | ✅ Yes        | An array of credential types. The first must always be `"VerifiableCredential"`; any additional values represent the VC subtype.                   | `[ "VerifiableCredential", "MuseumPassCredential" ]`                                                                                          |
| `termsofuse`                | ✅ Yes        | The **policy name or type** under which the credential is issued. Indicates which validation logic TRAIN should apply.                             | `"AttestationPolicy"`                                                                                                                         |
| `parentAccreditation`       | ✅ Yes        | A **DID URI** pointing to the `VerifiableAccreditation` that proves the issuer was accredited by a higher authority (i.e. part of a trust chain).  | `did:cheqd:testnet:07b4e2cb-b0b8-444e-8ed4-b0920115a45e?resourceName=TrustedIssuerAccreditation&resourceType=VerifiableAccreditationToAttest` |
| `credentialSchema`          | ❌ Optional   | URI for a JSON schema to validate the structure of the credential. If not provided, TRAIN will use the schema defined in the accreditation itself. | `https://resolver.cheqd.net/.../resourceName=MuseumPassCredentialSchema&resourceType=JsonSchemaValidator2018`                                 |
| `DNSTrustFrameworkPointers` | ❌ Optional   | A list of **DNS domains** used to verify that the root DID is also DNS-anchored. If not provided, TRAIN will still validate the root DID alone.    | `[ "cheqd.testtrain.trust-scheme.de" ]`                                                                                                       |

### TRAIN Trust Valdiator (TTV) Response Format

When a trust validation request is submitted, the TRAIN Trust Validator returns a structured JSON response describing the outcome of the trust chain evaluation. This includes the result of DNS anchoring checks (if requested) and the discovered trust framework that governs the credential's validation.

Below is a breakdown of each field returned in the response:

| **Field**                                                           | **Type**   | **Description**                                                                                                                                                  | **Example**                                                          |
| ------------------------------------------------------------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| `VerificationStatus`                                                | `boolean`  | Indicates whether the **root DID was successfully matched against a DNS record**. Will only be `true` if DNS verification was performed and succeeded.           | `true`                                                               |
| `VerificationResult`                                                | `object`   | Contains detailed information about the accreditation chain, root DID, and DNS verification process.                                                             | —                                                                    |
| `VerificationResult.AccreditorDIDs`                                 | `string[]` | An ordered array of **DIDs** representing each entity in the accreditation chain, from the VC issuer up to the root authority.                                   | `[ "did:cheqd:issuer", "did:cheqd:intermediary", "did:cheqd:root" ]` |
| `VerificationResult.FoundRootIssuerDID`                             | `string`   | The **root DID** at the top of the trust chain (i.e. the ultimate Trust Anchor).                                                                                 | `"did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e"`           |
| `VerificationResult.TrustFramework`                                 | `string`   | A URL pointing to the **governance or policy framework** that governs this trust chain. This is sourced from the root authorisation (`VerifiableAuthorisation`). | `"https://learn.cheqd.io/governance/start"`                          |
| `VerificationResult.TrustFrameworkId`                               | `string`   | A human-readable **name or ID** of the trust framework, also derived from the root authorisation metadata.                                                       | `"cheqd Governance Framework"`                                       |
| `VerificationResult.FindingCorrespondingDNSTrustFrameworkInitiated` | `boolean`  | Indicates whether TRAIN attempted to look up a DNS pointer associated with the root DID.                                                                         | `true`                                                               |
| `VerificationResult.VerifyRootIssuerDIDinDNS`                       | `boolean`  | Indicates whether the root DID was **successfully verified via a DNS TXT/TLSA record**.                                                                          | `true`                                                               |

### Benefits of Using TRAIN with cheqd

* **Decentralized Assurance**: No need to trust a single registry—chains of accreditations are independently verifiable.
* **DNS Anchoring**: Leverages globally resolvable domain infrastructure to provide high-assurance validation.
* **Interoperable**: Built on open standards like DIDs, VCs, and JSON-LD for compatibility with other ecosystems.

## Sequence Diagram for Validation

Below is a sequence diagram showing how a request is fully validated.

<figure><img src="../../../.gitbook/assets/TRAIN sequence diagram.png" alt=""><figcaption></figcaption></figure>
