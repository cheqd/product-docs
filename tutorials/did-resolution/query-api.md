# DID Resolver API

## Summary

DID Resolver API for getting DID Doc, its fragments or resources.

Resolution is executed according [Decentralized Identifier Resolution specification](https://w3c-ccg.github.io/did-resolution/).

## Endpoints

### Resolve DID or dereferencing

**URL:** `/1.0/identifiers/{did}`

**Parameters:**

- **did** (required, path, string) - DID Doc Id. _Example : "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY"_
- **fragmentId** (required, path, string) - `#` encoded to URL + DID Doc Verification Method or Service identifier. _Example : "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY%23key1"_
- **accept** (optional, header, string) - accept type
  - `application/did+json`
  - `application/did+ld+json`
  - `application/ld+json`
  - `*/*`

**Refactoring output:**

`/1.0/identifiers/did:cheqd:testnet:MjYxNzYKMjYxNzYK`

```json
{
  "@context": "https://w3id.org/did-resolution/v1",
  "didResolutionMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2022-10-19T10:32:22Z",
    "did": {
      "didString": "did:cheqd:testnet:MjYxNzYKMjYxNzYK",
      "methodSpecificId": "MjYxNzYKMjYxNzYK",
      "method": "cheqd"
    }
  },
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1"
    ],
    "id": "did:cheqd:testnet:MjYxNzYKMjYxNzYK",
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:MjYxNzYKMjYxNzYK#key1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:MjYxNzYKMjYxNzYK",
        "publicKeyMultibase": "z8KhcSunsSMJ9gYS7RnfF3ASgvgnSdCZSufSTMr8fWjYU"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:MjYxNzYKMjYxNzYK#key1"
    ]
  },
  "didDocumentMetadata": {
    "created": "2022-08-10T08:53:23Z",
    "versionId": "EAA5E61076BDD5B96878078EB8421EBD65B2837FC65A1DF5749A65BE6D8A5773",
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:MjYxNzYKMjYxNzYK/resources/60ad67be-b65b-40b8-b2f4-3923141ef382",
        "resourceCollectionId": "MjYxNzYKMjYxNzYK",
        "resourceId": "60ad67be-b65b-40b8-b2f4-3923141ef382",
        "resourceName": "test_cl_schema_resource",
        "resourceType": "JSON-Schema",
        "mediaType": "application/json",
        "created": "2022-08-17T06:16:58Z",
        "checksum": "a5abed82596e023395d0bb0c30516e78bd5eee7131245d94e54b38ab67c924e5",
        "previousVersionId": null,
        "nextVersionId": null
      }
    ]
  }
}

```

**Dereferencing output:**

`/1.0/identifiers/did:cheqd:testnet:MjYxNzYKMjYxNzYK%23key1`

```json
{
  "@context": "https://w3id.org/did-resolution/v1",
  "dereferencingMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2022-10-19T10:33:56Z",
    "did": {
      "didString": "did:cheqd:testnet:MjYxNzYKMjYxNzYK",
      "methodSpecificId": "MjYxNzYKMjYxNzYK",
      "method": "cheqd"
    }
  },
  "contentStream": {
    "@context": [
      "https://www.w3.org/ns/did/v1"
    ],
    "id": "did:cheqd:testnet:MjYxNzYKMjYxNzYK#key1",
    "type": "Ed25519VerificationKey2020",
    "controller": "did:cheqd:testnet:MjYxNzYKMjYxNzYK",
    "publicKeyMultibase": "z8KhcSunsSMJ9gYS7RnfF3ASgvgnSdCZSufSTMr8fWjYU"
  },
  "contentMetadata": {
    "created": "2022-08-10T08:53:23Z",
    "versionId": "EAA5E61076BDD5B96878078EB8421EBD65B2837FC65A1DF5749A65BE6D8A5773"
  }
}
```

### Get resource value by Id

**URL:** `/1.0/identifiers/{did}/resources/{resourceId}`

Get resource value without dereferencing wrappers.

**Parameters:**

- **did** (required, path, string) - Resource collection id. DID Doc Id. _Example : "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY"_
- **resourceId** (required, path, string) - DID Resource identifier. _Example : "60ad67be-b65b-40b8-b2f4-3923141ef382"_
- **accept** (optional, header, string) - accept type
  - `application/did+json`
  - `application/did+ld+json`
  - `application/ld+json`
  - `*/*`

**Output:**

```json
{
  "attrNames":[
    "name",
    "age",
    "sex"
  ]
}
```

### Get resource metadata by Id

**URL:** `/1.0/identifiers/{did}/resources/{resourceId}/metadata`

Get resource metadata without value by DID Doc.

**Parameters:**

- **did** (required, path, string) - Resource collection id. DID Doc Id. _Example : "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY"_
- **resourceId** (required, path, string) - DID Resource identifier. _Example : "60ad67be-b65b-40b8-b2f4-3923141ef382"_
- **accept** (optional, header, string) - accept type
  - `application/did+json`
  - `application/did+ld+json`
  - `application/ld+json`
  - `*/*`

**Output:**

```json
{
  "@context": "https://w3id.org/did-resolution/v1",
  "dereferencingMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2022-10-19T10:14:12Z",
    "did": {
      "didString": "did:cheqd:testnet:MjYxNzYKMjYxNzYK",
      "methodSpecificId": "MjYxNzYKMjYxNzYK",
      "method": "cheqd"
    }
  },
  "contentStream": {
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:MjYxNzYKMjYxNzYK/resources/60ad67be-b65b-40b8-b2f4-3923141ef382",
        "resourceCollectionId": "MjYxNzYKMjYxNzYK",
        "resourceId": "60ad67be-b65b-40b8-b2f4-3923141ef382",
        "resourceName": "test_cl_schema_resource",
        "resourceType": "JSON-Schema",
        "mediaType": "application/json",
        "created": "2022-08-17T06:16:58Z",
        "checksum": "a5abed82596e023395d0bb0c30516e78bd5eee7131245d94e54b38ab67c924e5",
        "previousVersionId": null,
        "nextVersionId": null
      }
    ]
  },
  "contentMetadata": {}
}
```

### Get collection resources

**URL:** `/1.0/identifiers/{did}/resources/all`

Get a list of all collection resources metadata.

**Parameters:**

- **did** (required, path, string) - DID Doc Id. _Example : "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY"_

- **accept** (optional, header, string) - accept type
  - `application/did+json`
  - `application/did+ld+json`
  - `application/ld+json`
  - `*/*`

#### Output

```json
{
  "@context": "https://w3id.org/did-resolution/v1",
  "dereferencingMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2022-10-19T10:14:12Z",
    "did": {
      "didString": "did:cheqd:testnet:MjYxNzYKMjYxNzYK",
      "methodSpecificId": "MjYxNzYKMjYxNzYK",
      "method": "cheqd"
    }
  },
  "contentStream": {
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:MjYxNzYKMjYxNzYK/resources/60ad67be-b65b-40b8-b2f4-3923141ef382",
        "resourceCollectionId": "MjYxNzYKMjYxNzYK",
        "resourceId": "60ad67be-b65b-40b8-b2f4-3923141ef382",
        "resourceName": "test_cl_schema_resource",
        "resourceType": "JSON-Schema",
        "mediaType": "application/json",
        "created": "2022-08-17T06:16:58Z",
        "checksum": "a5abed82596e023395d0bb0c30516e78bd5eee7131245d94e54b38ab67c924e5",
        "previousVersionId": null,
        "nextVersionId": null
      }
    ]
  },
  "contentMetadata": {}
}
```
