---
description: Deactivate a did:cheqd Decentralized Identifier (DID) using cheqd Studio.
---

# Deactivate a DID

Deactivating a DID marks it as **no longer active or in use**. This process updates the DID Document on the ledger to signal to verifiers, wallets, and other clients that the DID should no longer be trusted for interactions such as credential issuance or presentation.

Once a DID is deactivated:

* ✅ It cannot be updated again
* ❌ It cannot be reactivated
* 🛑 Applications resolving the DID will see its status marked as **deactivated**

***

## When to Deactivate a DID

You should deactivate a DID when:

* The associated identity (e.g. organisation, credential issuer) is no longer operating
* The cryptographic keys have been compromised or rotated to a new DID
* You want to enforce a clean break with no further updates to the DID Document

***

## Security Requirement

To prevent unauthorized deactivations, a `deactivate` request **must be signed by all of the Verification Method keys** listed in the current DID Document.

This ensures that **only the current controller(s)** of the DID can authorize its deactivation.

> 🔐 If even one required key is missing or invalid, the request will be rejected.

***

## How to Deactivate a DID

Use the API below to submit a request to deactivate a `did:cheqd` DID.

{% openapi-operation spec="cheqd-studio-api" path="/did/deactivate/{did}" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

{% openapi-schemas spec="cheqd-studio-api" schemas="DeactivatedDidResolution,DeactivatedDidDocumentMetadata,DidDeactivateRequest" grouped="true" %}
[Broken link](broken-reference)
{% endopenapi-schemas %}
