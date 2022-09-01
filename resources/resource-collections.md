# ↣ Collections of Resources

## Metadata for a _single_ Resource

Resources are identified with a [`did:cheqd` Decentralized Identifier](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method) with a unique identifier (UUID) that acts as a permanently-accessible link to fetch the resources from the cheqd ledger.

Multiple, linked resources can be stored in a **Collection**, for example, different versions of the same Resource over a period of time or semantically-linked resources. This enables unique resources to be stored directly on-ledger and be **retrievable through DID resolution** and **dereferencing**.

For simplicity, we will focus on the use case where a Resource is a **schema**. The same logic used in fetching schemas from the ledger can be applied to any of the aforementioned types of Resources.

The syntax of a Resource metadata is as follows:

```json
"linkedResourceMetadata": [
  {
    "resourceURI": "did:cheqd:testnet:DAzMQo4MDMxCjgwM/resources/44547089-170b-4f5a-bcbc-06e46e0089e4",
    "resourceCollectionId": "DAzMQo4MDMxCjgwM",
    "resourceId": "44547089-170b-4f5a-bcbc-06e46e0089e4",
    "resourceName": "PassportSchema",
    "resourceType": "CL-Schema",
    "mediaType": "application/json",
    "created": "2022-07-19T08:40:00Z",
    "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
    "previousVersionId": null, // null if no previous version, otherwise, resourceId of previous version
    "nextVersionId": null, // null if no new version, otherwise, resourceId of new version
  }
]
```

### Media Types allowed in Resources

Any [valid IANA Media Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics\_of\_HTTP/MIME\_types) (colloquially known as "file type") is allowed as an on-ledger Resource. The only restriction is that the Resource should fit within a block, which de-facto restricts it to \~190KB to fit within the \~200KB block limit. Any files larger than this are recommended to be stored and linked via long-term immutable file discovery mechanisms such as [IPFS](https://ipfs.io/).

A [Golang library is used to derive and set media type](https://ipfs.io/) based on the **file extension of provided resource file**. This makes it much simpler to maintain, since there is no list of file types that the cheqd ledger needs to gatekeep.

## Resources within Collections

A **Collection** is a group of **Resources** stored directly on-ledger, and therefore, their authenticity is secured by the consensus of the ledger.

Collections can store any type of Resource, but for the purpose of this documentation we will focus on the use case where the Collection is _used for storing a set of schemas_.

The most important concept used in this design is that each on-ledger **Collection** is **identified using a DID** and is **described using a **_**DID Document**_.

The DID Document acts as metadata, providing information about the **Collection**, such as who is able to update it, when it was created and what are the latest and deprecated versions of **Resources** within the **Collection**.

Moreover, each specific **Resource** within a **Collection** can be fetched via a DID Document relating to a Collection, through the use of the `‘service’` section.

![cheqd resources overview diagram](../.gitbook/assets/resource-overview-diagram.png)

_Figure 1_: Overview diagram of cheqd on-ledger resources, including where different layers of the structure interact with each other

Since the overall structure of our resource implementation is complex and has multiple layers, we will explain each component in turn.

### Collection DID Document

Let’s take a look at a **DID Document for a Collection**, and break it down:

```json
{
  "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c",
  "verificationMethod": [
    {
      "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c",
      "publicKeyMultibase": "<verification-public-key-multibase>"
    }
  ],
  "authentication": [
    "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#key1"
  ],
  "service": [
    {
      "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeLaw",
      "type": "LinkedResource",
      "serviceEndpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02"
    },
    {
      "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeMath",
      "type": "LinkedResource",
      "serviceEndpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/99b40fd8-fade-483c-bff4-f037b26dd810" 
    },
    {
      "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#JSON-Schema",
      "type": "LinkedResource",
      "serviceEndpoint": "https://schema.org/Person"
    },
    {
      "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#Markdown",
      "type": "LinkedResource",
      "serviceEndpoint": "https://github.com/cheqd/node/docs/SCHEMA.md"
    }
  ]
}
```

### Service section in DIDDoc

#### `id` of the Service references the Resource Name and Collection ID

The DID URL (“id”) of the Collection DID Document carries out two functions:

* Firstly, it exists as a regular DID, anchored on cheqd, and resolvable through any compatible DID Resolver;
* Secondly, it directly references the Collection ID of the Collection of Resources associated with the DID.

The completed string itself is a DID, e.g., `did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c`

Whereas, the unique ID on its own is the Collection ID, e.g., `46e2af9a-2ea0-4815-999d-730a6778227c`

And the resource name is a DID URL fragment, e.g., `DegreeLaw`

Therefore, the **same unique identifier** is also used as the **identifier of an on-ledger Collection of Resources**.

This means that if this UUID alone was queried on the cheqd ledger, along with the `did:cheqd:mainnet:`, it would return all Resources within a specific Collection.

#### `type` denotes that the Service Endpoint leads to a Resource

The `type` within the Service section denotes the Service Endpoint is of type Linked Resource. Service Types should be registered in [DID Spec Registries](https://www.w3.org/TR/did-spec-registries/).

The _actual_ Resource Type is specified in the Resource Metadata.

```jsonc
{
  // ...
  "resourceType": "CL-Schema"
  // ...         
}
```

#### `serviceEndpoint` provides a URL where the Resource can be fetched

The contents in the `serviceEndpoint` is intended to directly link to or resolve to a specific Resource. This can be specified _either_ as a DID URL _or_ an HTTP URL. In both scenarios, the link is a permanently-accessible identifier that can be accessed in multiple forms.

If the Service Endpoint is a DID URL, the client application would need to be capable of handling DID URLs and how to resolve them. In practice, this might meant having access to an instance of [Universal Resolver](https://github.com/decentralized-identity/universal-resolver) with the `did:cheqd` driver.

```jsonc
{
  "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeLaw",
  "type": "LinkedResource",
  // Client application would need to resolve DID URL below
  "serviceEndpoint": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02"  
}
```

For compatibility with a wider range of applications, our recommendation is to specify this as an HTTP URL. Note that the `resolver.cheqd.net` prefix can easily be replaced by a client application to utilise a different Universal Resolver instance instead (similar to ["gateways" in IPFS](https://docs.ipfs.tech/concepts/ipfs-gateway/#overview)).

```jsonc
{
  "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeLaw",
  "type": "LinkedResource",
  // Any client application capable of handling HTTP can fetch this resource
  "serviceEndpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02"  
}
```

While we prefer that [Resources are created on ledger](./) for the benefits they offer, the same technique of referencing via a DID URL can also apply to Service Endpoints that aren't necessarily stored on cheqd ledger (or other ledgers:

```jsonc
{
  "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#Markdown",
  "type": "LinkedResource",
  // HTTP URL to a non-legder endpoint
  "serviceEndpoint": "https://github.com/cheqd/node/docs/SCHEMA.md"
}
```

## Resource metadata within Collections

Collections are identified by a **Collection ID** which is a **unique identifier** of the **linked DID**. Within the _DID Document Metadata_ of the _Collection DIDDoc_, the Linked Resource metadata describes Resources within this Collection:

```jsonc
"linkedResourceMetadata": [
  { // First version of a Resource called PassportSchema
    "resourceURI": "did:cheqd:testnet:DAzMQo4MDMxCjgwM/resources/44547089-170b-4f5a-bcbc-06e46e0089e4",
    "resourceCollectionId": "DAzMQo4MDMxCjgwM", // Common collection ID
    "resourceId": "44547089-170b-4f5a-bcbc-06e46e0089e4", // Old Resource ID and version number
    "resourceName": "PassportSchema", // Resource name remains the same
    "resourceType": "CL-Schema",
    "mediaType": "application/json",
    "created": "2022-07-19T08:40:00Z",
    "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298", // Old version checksum
    "previousVersionId": null, // null, since no previous version
    "nextVersionId": "bb2118f3-5e55-4510-b420-33ef9e1726d2", // Points to next version below
  },
  { // Second version of a Resource called PassportSchema
    "resourceURI": "did:cheqd:testnet:DAzMQo4MDMxCjgwM/resources/bb2118f3-5e55-4510-b420-33ef9e1726d2",
    "resourceCollectionId": "DAzMQo4MDMxCjgwM", // Common collection ID
    "resourceId": "bb2118f3-5e55-4510-b420-33ef9e1726d2", // New Resource ID and version number
    "resourceName": "PassportSchema", // Resource name remains the same
    "resourceType": "CL-Schema",
    "mediaType": "application/json",
    "created": "2022-08-07T08:40:00Z",
    "checksum": "9123dcbb0b42652b0e105956c68d3ca2ff34584f324fa41a29aedd32b883e131", // New version checksum
    "previousVersionId": "44547089-170b-4f5a-bcbc-06e46e0089e4", // Points to previous version above
    "nextVersionId": null, // null if no new version
  }
]
```

Note that the Linked Resource output above does not show the actual data / schema attributes when displaying all Resources in this Collection. It only shows Resource _metadata_.

This logic prevents `GetResourceCollection` requests returning large quantities of data, which may be stored across multiple Resources within a Collection.

In order to fetch the actual data, it is necessary to query **the specific Resource**, rather than **the entire Collection**.

For more information about the particulars of requests and responses, please refer to our [**ADR on Resources on ledger**](https://github.com/cheqd/node-docs/blob/adr-008-resources-updates/architecture/adr-list/adr-008-ledger-resources.md).

## Versioning and Archiving Resources

As shown in the example above, there may be **previous** and **next** versions of the **Resource ID**.

Whenever a Resource is updated, a new UUID must be generated. The new Resource references the older version, so the UUID is effectively a version number.

Importantly, the `resourceName` and the `resourceType` of the Resource must remain the same.

For example, a Resource with the name `DegreeLaw` must always have the same name and resource type to be considered for previous/next version linking.

This could be used, for example, to find the version active at a particular point in time:

`did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeLaw?versionAt=2020-10-17T02:41:00Z`

Therefore, this design **easily allows historic Resources to be fetched, recovered and verified against**. For example, if you were presented a `DegreeLaw` Credential from 2015, you would still be able to prove that it conformed to the correct schema at the time of its issuance, even if the latest schema had been updated in 2022.
