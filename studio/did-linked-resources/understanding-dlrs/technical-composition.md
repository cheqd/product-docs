---
description: Understand the technical composition of DID-Linked Resources (DLRs) on cheqd.
---

# Technical composition of DID-Linked Resources

## Formatting a Resource

**"Resources"** are identified with a [`did:cheqd` Decentralized Identifier](../../../architecture/adr-list/adr-002-did-linked-resources.md) with a [Universally Unique Identifier (UUID)](https://www.uuidgenerator.net/) that acts as a permanently-accessible link to fetch the resources from the cheqd ledger. We refer to this as the "**resource ID**".

Using UUIDs, we can have a **high level of confidence that no two identical resource IDs will ever be created**. This is important for ensuring the integrity and uniqueness of each individual resource.

![Image showing a formatted DID URL for a cheqd resource](<../../../.gitbook/assets/Formatting resource diagram.png>)

_**Figure 1**: DID-linked Resource DID URL path_

This will be explained further in the section on DID URL dereferencing to fetch a resource.

## Understanding the DID and Collection relationship

Resources are organised into groups called "**Collections**". Each DID may have an associated Collection, and the Collection ID is **derived from the unique identifier of the DID**.

Collections can store any type of Resource, but for the purpose of this documentation we will focus on the use case where the Collection is _used for storing a set of schemas_.

The most important concept used in this design is that each on-ledger **Collection** is **identified using a DID** and is **described using a DID Document**.

The DID Document acts as metadata, providing information about the **Collection**, such as who is able to update it, when it was created and what are the latest and deprecated versions of **Resources** within the **Collection**.

For example, the following DID:

did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d

will derive the Collection ID: **1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d**

![Relation between a DID and Resource Collection](<../../../.gitbook/assets/DID and Collection relationship.png>)

_**Figure 2**: Relationship between a DID and Resource Collection_

A Collection is created using a `createResource` transaction, and specifying the Collection ID as the same identifier as that of the parent DID.

> Note that the Collection ID may take the syntactical form of a 'Hyperledger Indy' DID identifier **or** may be a [Universally Unique Identifier (UUID)](https://www.uuidgenerator.net/). This is described in the [cheqd DID method](../../../architecture/adr-list/adr-001-cheqd-did-method.md).

## Creating a Resource inside a Collection, associated with a DID

To create a **"DID-Linked Resource"**, you must already have created a 'parent' DID, from which the Collection ID can be derived. When you carry out the `createResource` transaction, you must:

1. Generate **a new, unique UUID** for the Resources
2. Specify the **same Collection ID** as the unique identifier of the parent DID
3. Sign the `createResource` transaction with the **Verification Method keys** of the parent DID.

This is shown in the diagram below:

![Relationship between a DID and DID-linked Resource](<../../../.gitbook/assets/DID and Resource relationship.png>)

_**Figure 3**: Relationship between a DID and DID-linked Resource_

Example of `createResource` transaction using Veramo SDK:

```json
{
    "kms": "local",
    "payload": {
        "collectionId": "1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
        "id": "f3d39687-69f5-4046-a960-3aae86a0d3ca",
        "name": "PassportSchema",
        "version": "", // optional
        "resourceType": "CL-Schema",
        "alsoKnownAs": [], // optional alternative URIs
        "data": "SGVsbG8sIHdvcmxk" // base 64 encoded file 
    },
    "signInputs": [{
        "verificationMethodId": "did:cheqd:testnet:z4ZUuPbs1xyK7y8d#key-1",
        "keyType": "Ed25519",
        "privateKeyHex": "0f5c124886178037952e87e0cdc55d185732577fca19ae877e64ac9ab24a0cc534e5326e70f1a42d785d93048aee806c359ec75a7b06f39253befd1746708438"
    }]
}
```

## Linking DIDs to Resources and Collections

Multiple, DID-Linked Resources can be stored in a **Collection**, for example, this could be different versions of the same Resource over a period of time or semantically-linked resources. This enables unique resources to be stored directly on-ledger and be **retrievable through DID resolution** and **dereferencing**.

Once you have created a resource, the DID Document will automatically reference the resource and the collection within the `didDocumentMetadata` in a newly defined section called `linkedResourceMetadata`.

This relationship is shown in the diagram below:

![DID Document metadata with linked Resource metadata](../../../.gitbook/assets/DIDDocumentMetadata.png)

_**Figure 4**: DID Document metadata with DID-linked Resource metadata_

For simplicity, we will focus on the use case where a Resource is a **schema**. The same logic used in fetching schemas from the ledger can be applied to any of the aforementioned types of Resources.

The syntax of a Resource metadata for a single schema is as follows:

```json
"linkedResourceMetadata": [
  {
    "resourceURI": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d/resources/f3d39687-69f5-4046-a960-3aae86a0d3ca",
    "resourceCollectionId": "1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
    "resourceId": "f3d39687-69f5-4046-a960-3aae86a0d3ca",
    "resourceName": "PassportSchema", // First version of a Resource called PassportSchema
    "resourceType": "CL-Schema",
    "mediaType": "application/json",
    "created": "2022-07-19T08:40:00Z",
    "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
    "previousVersionId": "", // empty string if no previous version, otherwise, resourceId of previous version
    "nextVersionId": "", // null if no new version, otherwise, resourceId of new version
  }
]
```

### Media Types allowed in Resources

Any [valid IANA Media Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) (colloquially known as "file type") is allowed as an on-ledger Resource. The only restriction is that the Resource should fit within a block, which de-facto restricts it to \~190KB to fit within the \~200KB block limit. Any files larger than this are recommended to be stored and linked via long-term immutable file discovery mechanisms such as [IPFS](https://ipfs.io/).

A [Golang library is used to derive and set media type](https://ipfs.io/) based on the **file extension of provided resource file**. This makes it much simpler to maintain, since there is no list of file types that the cheqd ledger needs to gatekeep.

## Resource Request Parameters

The following list defines which specific parameters a resource request format may contain:

| Parameter                  | Description                                                                                                                                                                                                                                                                                                    |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`resourceUri`**          | A string or a map that conforms to the rules of [RFC3986 URIs](https://www.w3.org/TR/did-core/#dfn-uri) which SHOULD directly lead to a location where the resource can be accessed from. For example: `dic:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02` |
| **`resourceCollectionId`** | A string that conforms to a cheqd-supported unique identifier format. For example a UUID: `46e2af9a-2ea0-4815-999d-730a6778227c`                                                                                                                                                                               |
| **`resourceId`**           | A string that uniquely identifies the resource, cheqd uses UUIDs. For example a UUID: `0f964a80-5d18-4867-83e3-b47f5a756f02`                                                                                                                                                                                   |
| **`resourceName`**         | A string that uniquelt names and identifies a resource. This property, along with the resourceType below, can be used to track version changes within a resource.                                                                                                                                              |
| **`resourceType`**         | A string that identifies the type of resource. This property, along with the resourceName above, can be used to track version changes within a resource. Not to be confused with media type.                                                                                                                   |
| **`resourceVersion`**      | (Optional) A string that identifies the version of resource. This property is provided by the client and can be any value                                                                                                                                                                                      |
| **`alsoKnownAs`**          | (Optional) An array that describes alternative URIs for the resource.                                                                                                                                                                                                                                          |

## Resource Response Parameters

The following list defines which specific parameters a resource response format may contain:

| Parameter                  | Description                                                                                                                                                                                                                                                                                                    |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`resourceUri`**          | A string or a map that conforms to the rules of [RFC3986 URIs](https://www.w3.org/TR/did-core/#dfn-uri) which SHOULD directly lead to a location where the resource can be accessed from. For example: `dic:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02` |
| **`resourceCollectionId`** | A string that conforms to a cheqd-supported unique identifier format. For example a UUID: `46e2af9a-2ea0-4815-999d-730a6778227c`                                                                                                                                                                               |
| **`resourceId`**           | A string that uniquely identifies the resource, cheqd uses UUIDs. For example a UUID: `0f964a80-5d18-4867-83e3-b47f5a756f02`                                                                                                                                                                                   |
| **`resourceName`**         | A string that uniquelt names and identifies a resource. This property, along with the resourceType below, can be used to track version changes within a resource.                                                                                                                                              |
| **`resourceType`**         | A string that identifies the type of resource. This property, along with the resourceName above, can be used to track version changes within a resource. Not to be confused with media type.                                                                                                                   |
| **`resourceVersion`**      | (Optional) A string that identifies the version of resource. This property is provided by the client and can be any value                                                                                                                                                                                      |
| **`alsoKnownAs`**          | (Optional) An array that describes alternative URIs for the resource.                                                                                                                                                                                                                                          |
| **`mediaType`**            | A string that identifies the IANA-media type of the resource.                                                                                                                                                                                                                                                  |
| **`created`**              | A string that identifies the time the resource was created in XML date-time.                                                                                                                                                                                                                                   |
| **`updated`**              | (Optional) A string that identifies the time the resource was updated in XML date-time.                                                                                                                                                                                                                        |
| **`checksum`**             | A string that may be used to prove that the resource has not been tampered.                                                                                                                                                                                                                                    |
| **`previousVersionId`**    | (Optional) A string that identifies the previous version of the resource.                                                                                                                                                                                                                                      |
| **`nextVersionId`**        | (Optional) A string that identifies the next version of the resource.                                                                                                                                                                                                                                          |

## Example of a resolved DID with an associated Resource

Let’s take a look at a fully resolved **output response** for a **DID with a Collection and single associated Resource:**

```json
{
  "@context": "https://w3id.org/did-resolution/v1",
  "didResolutionMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2022-11-28T05:01:50Z",
    "did": {
      "didString": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
      "methodSpecificId": "1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
      "method": "cheqd"
    }
  },
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1"
    ],
    "id": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
    "controller": [
      "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d"
    ],
    "verificationMethod": [
      {
        "id": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d#verKey1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
        "publicKeyMultibase": "zB5wPyMGYL4LbT424Z7yXHm6nZrrLqZZg9eWtVmedodys"
      },
    ],
    "authentication": [
      "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d#verKey1"
    ]
  },
  "didDocumentMetadata": {
    "created": "2015-04-10T11:51:40Z",
    "versionId": "ea2b76cf-a118-403a-8f49-244e56c9dcb8",
    "linkedResourceMetadata": [
      { // First version of a Resource called PassportSchema
        "resourceURI": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d/resources/f3d39687-69f5-4046-a960-3aae86a0d3ca",
        "resourceCollectionId": "1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d", // Derived Collection ID
        "resourceId": "f3d39687-69f5-4046-a960-3aae86a0d3ca",
        "resourceName": "PassportSchema",
        "resourceType": "CL-Schema",
        "mediaType": "application/json",
        "created": "2015-04-16T14:01:42Z",
        "checksum": "72f9d4f96c6f4fedcfd5d1691b10d60d14a008cace269ddb35342aa8d43a30fc",
        "previousVersionId": null, // No previous or next versions
        "nextVersionId": null // No previous or next versions
      }
    ]
  }
}
```

## Example of DID Document with multiple versions of the same resource

Let’s take a look at a fully resolved **output response** for a **DID with a Collection and multiple associated Resources:**

```json
{
  "@context": "https://w3id.org/did-resolution/v1",
  "didResolutionMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2022-11-28T05:01:50Z",
    "did": {
      "didString": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
      "methodSpecificId": "1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
      "method": "cheqd"
    }
  },
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1"
    ],
    "id": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
    "controller": [
      "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d"
    ],
    "verificationMethod": [
      {
        "id": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d#verKey1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d",
        "publicKeyMultibase": "zB5wPyMGYL4LbT424Z7yXHm6nZrrLqZZg9eWtVmedodys"
      },
    ],
    "authentication": [
      "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d#verKey1"
    ]
  },
  "didDocumentMetadata": {
    "created": "2015-04-10T11:51:40Z",
    "versionId": "9D760202FF2BD4A12344283627FF251BE6C48812C7626C3564C1C2843CAB9085",
    "linkedResourceMetadata": [
      { // First version of a Resource called PassportSchema
        "resourceURI": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d/resources/f3d39687-69f5-4046-a960-3aae86a0d3ca",
        "resourceCollectionId": "1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d", // Derived Collection ID
        "resourceId": "f3d39687-69f5-4046-a960-3aae86a0d3ca",
        "resourceName": "PassportSchema", // Resource name remains the same
        "resourceType": "CL-Schema", // Resource type remains the same
        "mediaType": "application/json",
        "created": "2015-04-16T14:01:42Z",
        "checksum": "72f9d4f96c6f4fedcfd5d1691b10d60d14a008cace269ddb35342aa8d43a30fc",
        "previousVersionId": null,
        "nextVersionId": "8f86d7aa-dc6a-4cee-ac37-97956542d587"
      },
      { // Second version of a Resource called PassportSchema
        "resourceURI": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d/resources/8f86d7aa-dc6a-4cee-ac37-97956542d587",
        "resourceCollectionId": "1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d", // Derived Collection ID
        "resourceId": "8f86d7aa-dc6a-4cee-ac37-97956542d587",
        "resourceName": "PassportSchema", // Resource name remains the same
        "resourceType": "CL-Schema", // Resource type remains the same
        "mediaType": "application/json",
        "created": "2020-06-16T14:02:39Z",
        "checksum": "ec54f8019b869d5511b42678ea859b9dc185f487bf1776cb079fda0930331689",
        "previousVersionId": "f3d39687-69f5-4046-a960-3aae86a0d3ca",
        "nextVersionId": "bd128013-636d-4240-b48b-fc88bf9ee8de"
      },
      { // Third version of a Resource called PassportSchema
        "resourceURI": "did:cheqd:mainnet:1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d/resources/bd128013-636d-4240-b48b-fc88bf9ee8de",
        "resourceCollectionId": "1f8e08a2-eeb6-40c3-9e01-33e4a0d1479d", // Derived Collection ID
        "resourceId": "bd128013-636d-4240-b48b-fc88bf9ee8de",
        "resourceName": "PassportSchema", // Resource name remains the same
        "resourceType": "CL-Schema", // Resource type remains the same
        "mediaType": "application/json",
        "created": "2022-09-16T14:10:46Z",
        "checksum": "cc187364f3bf071e5411cb6074d9c44a1b416a32b8eea581d113e486d1d586cf",
        "previousVersionId": "8f86d7aa-dc6a-4cee-ac37-97956542d587",
        "nextVersionId": null
      }
    ]
  }
}
```

Collections are identified by a **Collection ID** which is a **unique identifier** of the **linked, parent DID**. Within the _DID Document Metadata_ of the _Collection DIDDoc_, the Linked Resource metadata describes Resources within this Collection:

Note that the Linked Resource output above does not show the actual data / schema attributes when displaying all Resources in this Collection. It only shows Resource _metadata_.

This logic prevents `GetResourceCollection` requests returning large quantities of data, which may be stored across multiple Resources within a Collection.

In order to fetch the actual data, it is necessary to query **the specific Resource**, rather than **the entire Collection**.

For more information about the particulars of requests and responses, please refer to our [**ADR on Resources on ledger**](../../../architecture/adr-list/adr-002-did-linked-resources.md).

## Versioning and Archiving Resources

As shown in the examples above, there may be **previous** and **next** versions of the **Resource ID**.

Whenever a Resource is updated, a new UUID must be generated. The new Resource references the older version, so the UUID is effectively a version number.

Importantly, the `collectionId,` `resourceName` and the `resourceType` of the Resource **must remain the same**.

For example, a Resource with the name `PassportSchema` must always have the **same** **name** and **resource type** to be considered for previous/next version linking.

This could be used, for example, to find the version active at a particular point in time:

## Query based dereferencing

Fetching resources using DID resolvers and DID URLs is hugely desirable. Up until this point, we have shown resources identified using **path-based syntax**.

However, query-based syntax should also be enabled to **allow more granular and specific searches within a particular Collection**.

To enable combined resolution/dereferencing behavior, cheqd are defining multiple query-based [DID URL](https://www.w3.org/TR/did-core/#dfn-did-urls) parameters to fetch `resource` or associated metadata. If a [DID method](https://www.w3.org/TR/did-core/#dfn-did-methods) specification supports these parameters, and if a [DID URL](https://www.w3.org/TR/did-core/#dfn-did-urls) using that method includes the parameter with a valid value, then when a resolver calls the associated [VDR](https://www.w3.org/TR/did-core/#dfn-verifiable-data-registry) using that [DID URL](https://www.w3.org/TR/did-core/#dfn-did-urls), the VDR returns the identified digital resource, **not** the [DID document](https://www.w3.org/TR/did-core/#dfn-did-documents).

> **IMPORTANT**: DID URL queries should be fully qualified so that they **uniquely identify a single resource, or single resource version unless expressly specified**.
