---
description: >-
  Resolve a did:cheqd Decentralized Identifier (DID) to fetch the associated DID
  Document, using cheqd Studio.
---

# Resolve a DID

**Resolving a DID** means retrieving the associated **DID Document**, which contains the public keys, service endpoints, and metadata that describe and verify the identity. In decentralized identity systems, DID resolution is the foundation for:

* Verifying the **authenticity** of a DID
* Retrieving **public keys** for signature validation
* Accessing **service endpoints** (e.g. credential issuance APIs)
* Dereferencing to **DID-Linked Resources (DLRs)**, such as status lists or trust registries.

A user is able to input a **DID (string)** as a request format and resolve the DID to fetch the associated **DID Document**, **DID Resolution Metadata** and **DID Document metadata**.

{% openapi-operation spec="cheqd-studio-api" path="/did/search/{did}" method="get" %}
[Broken link](broken-reference)
{% endopenapi-operation %}
