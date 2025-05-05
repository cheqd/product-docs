---
description: Update a did:cheqd Decentralized Identifier (DID) using cheqd Studio.
---

# Update or Deactivate DID

Users are able to update DID Documents for various reasons, such as to include a new section or to rotate the Verification Method keys within the DID Document.&#x20;

With the `/did/update` API, users are able to input either:

1. &#x20;The section of the DID Document they would like to update; or
2. The full updated DID Document body.

{% openapi-operation spec="cheqd-studio-api" path="/did/update" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

Users are also able to deactivate DID Documents to prevent further updates and to provide client applications the relevant information that the DID Document is no longer actively used.&#x20;

A `/did/deactivate` request must be signed by all of the Verification Method keys listed in the DID Document.&#x20;

{% openapi-operation spec="cheqd-studio-api" path="/did/deactivate/{did}" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}
