# Workflow for creating Resources

The logic for creating a new on-ledger Resource within a Collection together with its DID Document is handled through the **Resource module**.

## Flow for creating a new Resource within a Collection

### 1. Collection DID Document is created

At the point the Collection DID Document is created, the service section does not reference any particular Resource. This will therefore be a very basic DID Document.

Alternatively, any pre-existing DID Document can also be used.

### 2. Linked Resource is created

An authorised party then creates a Resource, specifying:

* A Collection ID with the same unique identifier as the Collection DID Document
* A new Resource ID
* Resource name, Resource type, Media Type
* Any other basic details needed for the Resource

The authorised party then signs this `createResource` request with the same `verificationMethod` or `authentication` keys as is specified within the Collection DID Document.

This allows for multi-signature scenarios as well, in case the controllers of the DIDDoc are distributed across multiple keys

### 3. Collection DID Document metadata includes Resource IDs

Once the Resource is created, metadata related to it will be referenced within the **Collection DID Document metadata**, in the following format.

```
"didDocumentMetadata": {
  "created": "2022-07-19T08:29:07Z",
  "versionId": "57543FA1D9C56033BABBFA3A438E0A149E01BBB89E6D666ACE1243455AA6F2BC",
  "linkedResourceMetadata": [
    {
      "resourceURI": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02",
      "resourceCollectionId": "46e2af9a-2ea0-4815-999d-730a6778227c",
      "resourceId": "0f964a80-5d18-4867-83e3-b47f5a756f02",
      "resourceName": "DegreeLaw",
      "resourceType": "CL-Schema",
      "mediaType": "application/json",
      "created": "2022-07-19T08:40:00Z",
      "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
      "previousVersionId": null, // null if no previous version, otherwise, resourceId of previous version
      "nextVersionId": null, // null if no new version, otherwise, resourceId of new version
    }
  ]
}
```

Importantly, this is again a **Resource Preview**, meaning that the actual data from the Resource will not be displayed.

To fetch the Resource, a client application would need to resolve the DID URL `did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02`

### 4. Collection DID Document updated to reference Resource (optional)

Through an update DID operation, the authorised party can **update the Service section** within the **Collection DID Document** to **include the appropriate Collection and Resource IDs and Resource name**.

This step is _optional_, since the Resource and its metadata is already available as described above.

```
"service": [
  {
    "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeLaw",
    "type": "LinkedResource",
    "service_endpoint": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02"
  }
]
```

## Using Resources as a Credential Issuer

### 1. Referencing existing Resources from an Issuer DIDDoc

Once a Collection DID Document has been created and is tied to the appropriate Resources, an **Issuer** may want to **update their own DID Document** to demonstrate that such Resources are used by the Issuer.

An example of an Issuer DID Document which references a Collection DID Document, Collection and specific Resource is shown in the example below:

```json
{
  "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c",
  "verification_method": [
    {
      "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c",
      "public_key_multibase": "<verification-public-key-multibase>"
    }
  ],
  "authentication": [
    "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c#key1"
  ],
  "service": [
    {
      "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c#website",
      "type": "LinkedDomains",
      "service_endpoint": "https://www.cheqd.io"
    },
    {
        "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeLaw",
        "type": "LinkedResource",
        "service_endpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02"
    },
    {
        "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeMath",
        "type": "LinkedResource",
        "service_endpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/99b40fd8-fade-483c-bff4-f037b26dd810"
    }
  ]
}
```

In this case, an **Issuer can update to their DID Document**, authenticating with their appropriate verification keys and:

* Specify the **Collection DID URL** within the “`id`” fields of the ‘`service`’ sections, within which the Issuer wants to reference that particular Collection.
* Specify the `resourceName` as the **fragment (#)** appended to the end of the **service ‘`id`’ URL**
* Specify the **same URL as is defined within the Collection DID URL** to directly point-to and fetch the specific Resource, **within the `serviceEndpoint` field**.

Through linking in this way, the Issuer is able to clearly and transparently illustrate, for example, which Schemas it conforms to when issuing Verifiable Credentials. Or alternatively, the Issuer could use this to point to the canonical and latest version of their Governance Framework.

Moreover, the Resource referenced in the Issuer DID Document does not need to be controlled by the Issuer, they could show conformance to any Schema, controlled by any party. This architecture therefore provides a W3C compliant and highly flexible way to tie Resources to DIDs.

To find out more about the Indy AnonCreds design, and go deeper in the cheqd On-ledger Resources with DIDs, see below:

* [AnonCreds Design (Hyperledger Indy SDK](https://hyperledger-indy.readthedocs.io/projects/sdk/en/latest/docs/design/002-anoncreds/README.html))
* [ADR 002: On-ledger Resources](../../architecture/adr-list/adr-002-on-ledger-resources.md)
