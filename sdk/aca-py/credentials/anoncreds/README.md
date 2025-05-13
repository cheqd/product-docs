---
description: >-
  Issue, present and revoke AnonCreds signed by cheqd Decentralized Identifiers
  (DIDs), using ACA-Py.
---

# AnonCreds

**AnonCreds** is a privacy-preserving Verifiable Credential format designed for **selective disclosure**, **non-correlatable presentations**, and **revocation support**. In **Aries Cloud Agent Python (ACA-Py)**, AnonCreds is fully supported using the **Issue Credential v1/v2** and **Present Proof v1/v2** protocols.

ACA-Py allows agents to issue, hold, and verify AnonCreds credentials using a range of supported ledgers—including full integration with the **cheqd network**, a modern, scalable alternative to legacy Indy-based networks.

## Get started

Issue, present and revoke AnonCreds signed by cheqd Decentralized Identifiers (DIDs), using the tutorials below:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue AnonCreds Credentials</strong></mark></td><td>Issue AnonCreds Credentials signed by cheqd DIDs using ACA-Py.</td><td><a href="issue-credential.md">issue-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Present AnonCreds Credentials</strong></mark></td><td>Present AnonCreds Credentials using ACA-Py.</td><td><a href="present-credential.md">present-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Revoke AnonCreds</strong></mark></td><td>Revoke AnonCreds Credentials signed by cheqd DIDs using ACA-Py.</td><td><a href="revoke-credential.md">revoke-credential.md</a></td></tr></tbody></table>

***

### Why Use AnonCreds?

AnonCreds credentials are purpose-built for high-assurance, privacy-respecting use cases that require:

* **Selective disclosure** of individual claims
* **Zero-knowledge proofs**
* **Revocation support via AnonCreds Status Lists**
* **Credential definitions and revocation registries anchored on-ledger**

***

### AnonCreds + cheqd Support

ACA-Py now supports **`did:cheqd`** for issuing AnonCreds credentials using the cheqd AnonCreds Object Method. This removes dependency on Indy-based networks while retaining the proven AnonCreds credential exchange flow.

#### With ACA-Py + cheqd, you can:

* ✅ Register `did:cheqd` identifiers for use in credential issuance
* ✅ Publish **Schemas and** **Credential Definitions** as **DID-Linked Resources**
* ✅ Issue and verify AnonCreds credentials signed with `did:cheqd`
* ✅ Enable revocation using **AnonCreds Status List Definitions and Entries**, also stored on-ledger as DID-Linked Resources

> ✅ This allows developers to migrate off the deprecated Sovrin network without changing their existing flows or protocols.

***

### Features Supported in ACA-Py

| Feature                                               | Supported in ACA-Py |
| ----------------------------------------------------- | ------------------- |
| Issue AnonCreds credentials                           | ✅ Yes               |
| Present and verify credentials                        | ✅ Yes               |
| Revocation support via cheqd AnonCreds Status Lists   | ✅ Yes               |
| DID-Linked Resource anchoring (schemas, defs, status) | ✅ Yes               |
| Use `did:cheqd` for AnonCreds                         | ✅ Yes               |
| Use other Indy-compatible ledgers                     | ✅ Yes               |

### AnonCreds Object Method

Take a deep dive into the cheqd AnonCreds Object Method below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd AnonCreds Object Method</strong></mark></td><td>Understand how cheqd supports AnonCreds Objects using DID-Linked Resources.</td><td><a href="../../../../advanced/anoncreds/">anoncreds</a></td></tr></tbody></table>
