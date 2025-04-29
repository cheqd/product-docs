---
description: >-
  Include Trust Registries and Verifiable Accreditations within the body of
  Verifiable Credentials.
---

# Referencing Trust Registry within a Verifiable Credential

To ensure a Verifiable Credential (VC) is not only technically valid but also issued by an **authorised and trusted party**, it must include metadata that links back to its origin in a **trust registry**.

This is done using the `termsOfUse` property, where the **Trusted Issuer includes an `AttestationPolicy`** referencing:

* The **Verifiable Accreditation** that granted them permission
* The **Root Authorisation** that anchors the governance framework

## Why reference a trust registry?

Including a reference to a trust registry enables verifiers to:

* **Validate** that the issuer is accredited to issue this type of credential.
* **Trace** the credential’s trust lineage back to a Root Trusted Accreditation Organisation (rTAO).
* **Enforce** domain-specific governance or regulatory policies.

## Structure of `termsOfUse`

The `termsOfUse` field uses the `AttestationPolicy` type and typically includes:

| Field                 | Description                                                                           |
| --------------------- | ------------------------------------------------------------------------------------- |
| `type`                | Must be `"AttestationPolicy"`                                                         |
| `parentAccreditation` | DID URL pointing to the Verifiable Accreditation that granted authority to the issuer |
| `rootAuthorisation`   | DID URL referencing the original Root Authorisation for the trust chain               |

### Example: Credential referencing its trust registry via `AttestationPolicy`

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://www.w3.org/ns/credentials/examples/v2"
  ],
  "id": "http://university.example/credentials/3732",
  "type": ["VerifiableCredential", "ExampleDegreeCredential"],
  "issuer": "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0",
  "validFrom": "2010-01-01T00:00:00Z",
  "credentialSubject": {
    "id": "did:cheqd:testnet:b4902745-5b5b-423e-820a-0773b033f2b9",
    "degree": {
      "type": "ExampleBachelorDegree",
      "name": "Bachelor of Science and Arts"
    }
  },
  "termsOfUse": {
    "type": "AttestationPolicy",
    "parentAccreditation": "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0/resources/58c01595-f884-4a3b-add4-8c691e16b8ee",
    "rootAuthorisation": "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0/resources/58c01595-f884-4a3b-add4-8c691e16b8ee"
  }
}

```

### What this enables

This embedded trust policy allows verifiers to:

* Look up the parent accreditation and root authorisation.
* Confirm that the issuer was permitted to issue the attestation type.
* Automate or enforce policy-based trust decisions.

## Validating Trust Chains Using TRAIN

Once a Verifiable Credential includes an `AttestationPolicy` referencing the trust registry, **TRAIN** (TRust mAnagement INfrastructure) can be used to automatically validate the issuer’s authority against a decentralized trust chain.

### What does TRAIN do?

TRAIN is a trust validator that:

* Accepts a credential (or its metadata) as input.
* Resolves the **issuer’s accreditation**.
* Traverses the trust chain to the **root authorisation**.
* Optionally checks **DNS anchoring** of the root DID for higher assurance.
* Returns a result that confirms if the issuer is trusted under the specified framework.

***

### TRAIN Validation Input

Here’s a simplified example of the request body TRAIN accepts:

```json
{
  "issuer": "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0",
  "type": ["VerifiableCredential", "ExampleDegreeCredential"],
  "termsofuse": "AttestationPolicy",
  "parentAccreditation": "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0/resources/58c01595-f884-4a3b-add4-8c691e16b8ee",
  "DNSTrustFrameworkPointers": ["example.trust-scheme.org"]
}
```

{% hint style="info" %}
_Note: `DNSTrustFrameworkPointers` is optional. If omitted, TRAIN will still validate trust up to the root authorisation._
{% endhint %}

***

### &#x20;TRAIN Validation Output

TRAIN returns a response like this:

```json
{
  "VerificationStatus": true,
  "VerificationResult": {
    "AccreditorDIDs": [
      "did:cheqd:testnet:c2f18b6b-32e2-48d1-a5a8-5f5d2d9798f0",
      "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c"
    ],
    "FoundRootIssuerDID": "did:cheqd:testnet:8ea036da-f340-480d-8952-f5561ea1763c",
    "TrustFramework": "Example Trust Framework",
    "TrustFrameworkId": "https://example.com/governance-framework/124",
    "VerifyRootIssuerDIDinDNS": true
  }
}
```

{% hint style="info" %}
✅ `VerificationStatus: true` indicates that the issuer is **properly accredited** and the trust chain is intact. Although this will **only** be true if the root DID is also included in a DNS record.
{% endhint %}

***

### Why use TRAIN?

* Automates trust validation using standardized credential metadata.
* Provides **clear trust decisions** for wallets, agents, and verifiers.
* Enables integration with public DNS for added assurance of root DIDs.
* Compatible with all credentials that follow cheqd’s trust chain model.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Get Started with TRAIN</strong></mark></td><td>Deploy TRAIN to verify Decentralized Trust Chains back to a Root of Trust DID and DNS Record.</td><td></td><td><a href="../train/">train</a></td><td><a href="../../../.gitbook/assets/fraunhofer.png">fraunhofer.png</a></td></tr></tbody></table>
