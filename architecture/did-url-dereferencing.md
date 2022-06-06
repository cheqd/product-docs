# 🔀 DID URL Dereferencing

#### Resolving DID vs Dereferencing DID

It is important to explain the difference between resolving a DID and dereferencing a DID URL.

When you resolve a DID, a DID Document is returned. For example, resolving: "did:cheqd:example1234" would return the full DID Document associated with the specific DID "did:cheqd:example1234".

Dereferencing a DID URL is slightly different. When you dereference a DID URL, you are parsing the URL for specific actions, such as to take a certain path, point to a specific fragment, or query a specific resource.

For example, "did:cheqd:example1234?service=CL-Schema" can be dereferenced. In this case it will query the service of type "CL-Schema" within the DID Document and will return the resource specified at the Service Endpoint within the DID Document. It is this type of architecture that cheqd uses in this ADR to fetch schemas.
