---
description: >-
  Issue and present SD-JWT VC credentials signed by cheqd Decentralized
  Identifiers (DIDs), using Credo.
---

# SD-JWT VC

SD-JWT Verifiable Credentials enable selective disclosure and unlinkability, combining the compact JWT format with privacy-preserving cryptographic techniques. They are optimized for privacy, mobile compatibility, and integration with modern identity standards.

In **Credo**, SD-JWT credentials are issued using **OpenID for Verifiable Credential Issuance (OID4VCI)** and presented using **OpenID for Verifiable Presentations (OID4VP)**—ensuring secure, interoperable credential exchange based on open standards.

> ✅ This approach is fully aligned with the **EU Digital Identity Wallet** standards and protocols, and the developing **EU Architecture and Reference Framework (ARF)**.

***

## Get Started

Issue and present SD-JWT credentials in Credo using the tutorials below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue a Verifiable Credential</strong></mark></td><td>Issue SD-JWT Credentials signed by cheqd DIDs using Credo and OpenID4VCI.</td><td><a href="issue-credential.md">issue-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Present a Verifiable Credential</strong></mark></td><td>Present  SD-JWT Credentials signed by cheqd DIDs using Credo and OpenID4VP.</td><td><a href="present-credential.md">present-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Verify a Verifiable Credential</strong></mark></td><td>Verify SD-JWT Credentials signed by cheqd DIDs using Credo and OpenID4VCI.</td><td><a href="verify-credential.md">verify-credential.md</a></td></tr></tbody></table>

***

### Why Use SD-JWT?

SD-JWT credentials are ideal for ecosystems that require:

* **Privacy-preserving credential exchange**\
  Reveal only the claims you choose—no more, no less
* **Unlinkability across presentations**\
  Each presentation is cryptographically unique, preventing correlation by relying parties
* **Optimized for mobile and constrained environments**\
  Compact format, ideal for mobile wallets and low-bandwidth scenarios
* **Standards-compliant**\
  Built on IETF SD-JWT, OID4VCI, and OID4VP specifications
* **Interoperability with identity wallets**\
  Enables seamless interaction with both SSI wallets and federated identity providers supporting OpenID standards
* **EU Wallet-ready**\
  Fully compatible with the EU Digital Identity Wallet and ARF requirements

***

### Extensibility for cheqd Functionality

While SD-JWT focuses on minimal disclosure, it can integrate cheqd-native functionality through linked references:

🔗 **DID-Linked Resources**\
Reference schemas, legal terms, or trust frameworks hosted on the cheqd ledger via out-of-band metadata\
Use `credential_metadata` or presentation definitions to point to these resources

🏛️ **Trust Registries**\
Issuers can prove authorisation by referencing cheqd-based trust registries—linked via DID-Linked Resources or OpenID Federation metadata

🚫 **Status Lists (Revocation)**\
Revocation is supported via cheqd-compatible Bitstring Status Lists\
These can be referenced externally without compromising SD-JWT’s privacy guarantees

📦 These integrations maintain SD-JWT’s compact, privacy-first design while adding verifiability and governance via cheqd infrastructure.

***

### Features Supported in Credo

#### Supported DID Methods

Credo supports SD-JWT credential issuance and key binding using:

* `did:key`
* `did:web`
* `did:cheqd`

#### Supported Protocols

* **Issuance**: OID4VCI (OpenID for Verifiable Credential Issuance)
* **Presentation**: OID4VP (OpenID for Verifiable Presentations)
