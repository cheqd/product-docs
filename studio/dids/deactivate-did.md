---
description: Deactivate a did:cheqd Decentralized Identifier (DID) using cheqd Studio.
---

# Deactivate a DID

Users are also able to deactivate DID Documents to prevent further updates and to provide client applications the relevant information that the DID Document is no longer actively used.&#x20;

A `/did/deactivate` request must be signed by all of the Verification Method keys listed in the DID Document.&#x20;

{% openapi-operation spec="cheqd-studio-api" path="/did/deactivate/{did}" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

{% openapi-schemas spec="cheqd-studio-api" schemas="DeactivatedDidResolution,DeactivatedDidDocumentMetadata,DidDeactivateRequest" grouped="true" %}
[Broken link](broken-reference)
{% endopenapi-schemas %}
