---
description: >-
  Issue and present JSON-LD credentials signed by cheqd Decentralized
  Identifiers (DIDs), using ACA-Py.
---

# JSON-LD

**JSON-LD Verifiable Credentials** follow the [W3C Verifiable Credentials Data Model](https://www.w3.org/TR/vc-data-model/), enabling decentralized, semantically rich, and interoperable credential exchange.

In **Aries Cloud Agent Python (ACA-Py)**, you can issue, verify, and present JSON-LD credentials using the **Issue Credential v2** and **Present Proof v2** protocols—alongside support for **Linked Data Proofs** and various signature suites.

## Get Started

Issue and present JSON-LD credentials with ACA-Py using the tutorials below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue JSON-LD Credentials</strong></mark></td><td>Issue JSON-LD Credentials signed by cheqd DIDs using ACA-Py.</td><td><a href="issue-credential.md">issue-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Present JSON-LD Credentials</strong></mark></td><td>Present JSON-LD Credentials using ACA-Py.</td><td><a href="present-credential.md">present-credential.md</a></td></tr></tbody></table>

***

### Why Use JSON-LD?

JSON-LD credentials are ideal for ecosystems that require:

* Standards-aligned, interoperable credentials
* Richly structured, linked data
* Flexible signature types (e.g. Ed25519, BBS+)
* Integration with W3C-conformant wallets and identity providers
* Compatibility with **OpenID for Verifiable Credentials (OID4VC)** and **EU Digital Identity Wallet** initiatives

***

### Extensibility for cheqd Functionality

Thanks to the flexibility of the **W3C VC data model**, JSON-LD credentials can be extended to support advanced **cheqd-native features**, including:

#### 🔗 DID-Linked Resources

* Credentials can reference **schemas**, **governance frameworks**, or **legal documents** published as **DID-Linked Resources** on the cheqd ledger
* Use the `termsOfUse` or `credentialSchema` fields to point to cheqd-hosted resources

#### 🏛️ Trust Registries

* A credential can reference a **trust registry** (also a DID-Linked Resource) that defines the rules, requirements, or authorisations under which the credential was issued

#### 🚫 Status Lists (Revocation)

* cheqd-compatible **Bistring Status List** entries can be referenced using the `credentialStatus` field, enabling scalable and private revocation
* These status lists can also be stored as DID-Linked Resources on cheqd

> 🔧 These extensions follow W3C patterns and require no change to the base data model—making them portable, verifiable, and standards-compliant.

***

### Features Supported in ACA-Py

| Feature                                | Supported in ACA-Py                                          |
| -------------------------------------- | ------------------------------------------------------------ |
| Issue JSON-LD credentials              | ✅ Yes                                                        |
| Present and verify JSON-LD credentials | ✅ Yes                                                        |
| Support for multiple proof types       | ✅ Yes (`Ed25519Signature2018`, `Ed25519Signature2020`, etc.) |
| Use of external or on-chain contexts   | ✅ Yes                                                        |
| Support for DID-Linked Resources       | ⚠️ Via extensions and custom fields                          |

***

### Supported DID Methods

ACA-Py supports signing JSON-LD credentials using DIDs such as:

* `did:key`
* `did:web`
* `did:peer`
* `did:cheqd` _(via plugin)_
* Custom methods via the [Universal Registrar](https://github.com/decentralized-identity/universal-registrar)

> ✅ For cheqd DIDs, ACA-Py allows full DID resolution and key binding required for signing JSON-LD credentials, assuming a supported verification method (e.g. `Ed25519VerificationKey2020`).

