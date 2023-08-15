---
description: Create Verifiable Credential Status List v2021 on cheqd
---

# Create Status List

The [Status List 2021 Specification](https://w3c-ccg.github.io/vc-status-list-2021/) indicates that it may be desirable to store the actual StatusList using something like a [Content Distribution Network](https://w3c-ccg.github.io/vc-status-list-2021/#content-distribution-networks) to lessen the load on the server maintained by the issuer to return a result in real-time.

Using cheqd's Resource Module, the same benefits may be achieved. In fact, storing a StatusList as an on-ledger Resource is a much better application of technology than using a Verifiable Credential for the same purpose.

By storing a StatusList on the cheqd Network as a Resource, it creates a much more resilient and decentralised mechanism for storing and maintaining the revocation/suspension status of Verifiable Credentials. The [benefits of using the cheqd Resource module over traditional centralised architecture are detailed here](../../../credential-service/did-linked-resources/understanding-dlrs/context.md).

Moreover, cheqd's Resource Module enables individual Resources to be referenced and retrieved using a DID URL in conformance with DID Core. This is being standardized at [the Trust over IP Foundation](https://trustoverip.org/) within a specification called [DID URLs for Digital Resources](https://wiki.trustoverip.org/display/HOME/DID-Linked+Resources+Specification).

### Creating a StatusList Resource using Veramo SDK for cheqd

Using the cheqd Resource module, the same content and semantics of StatusList2021 can be replicated, with additional benefits of enabling DID Resolvers to fetch the contents of the StatusList.

#### Create a DID and DID Document with keys to manage the StatusList

You can follow the [tutorial to create a DID and DID Document here](../did-operations/create-did.md).

Let's assume that the following DID is created.

```json
"didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1"
    ],
    "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
    "controller": [
      "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY"
    ],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY#verkey-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
        "publicKeyMultibase": "zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpYAZPD1M9tvwyzE"
      },
    ],
    "authentication": [
      "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY#verkey-1"
    ]
  }
```

#### Prepare encodedList content

Prepare a file with the StatusList2021 bitstring `encodedList` and encode it into `base64`, following the [same generate algorithm as in the Status List2021 Specification](https://w3c-ccg.github.io/vc-status-list-2021/#generate-algorithm).

{% hint style="info" %}
Note: The uncompressed bitstring _MUST_ be at least 16KB in size to maintain herd privacy for the holder.
{% endhint %}

#### Create a unique ID for the StatusList Resource

[UUIDs are used to identify Resources](https://en.wikipedia.org/wiki/Universally\_unique\_identifier). On Unix systems, the `uuidgen` tool can be used to generate a new UUID.

#### Create a new Resource for an unencrypted StatusList

```json
{
  "kms": "local",
  "payload": {
    "collectionId": "zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
    "id": "4a71319b-00b1-4db9-bc05-56dc426f7062",
    "name": "ExampleStatusList2021",
    "resourceType": "StatusList2021Revocation",
    "encrypted": false
    "data": "SGVsbG8sIHdvcmxk"
  },
  "signInputs": [
    {
      "verificationMethodId": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY#verkey-1",
      "keyType": "Ed25519",
      "privateKeyHex": "0f5c124886178037952e87e0cdc55d185732577fca19ae877e64ac9ab24a0cc534e5326e70f1a42d785d93048aee806c359ec75a7b06f39253befd1746708438"
    }
  ]
}
```

Where the fields within the payload have the following meaning:

| Parameter        | Description                                                                                                                                        |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **collectionId** | The unique identifier of the parent DID, to link the Resource to a particular DID                                                                  |
| **id**           | A UUID for the resource, to enable it to be specifically referenced and fetched                                                                    |
| **name**         | This must be a unique name indicating the `type` of Status List`,` but also a qualifying name for the List. For example: **ExampleStatusList2021** |
| **resourceType** | This must indicate the `statusPurpose.` This value should be either: **StatusList2021Revocation** or **StatusList2021Suspension**                  |
| **data**         | Base 64 encoded file containing the full bitstring for the StatusList                                                                              |

{% hint style="info" %}
Note: If an issuer wants to create multiple StatusLists within the same Collection, they must have **unique** and **distinct names**.
{% endhint %}

#### Note association between DID and Resource

Once created, the StatusList2021 Resource will be associated with the parent DID, and referenced in the DID Document Metadata as follows:

```json
{
  "didDocument": {
    "@context": ["https://www.w3.org/ns/did/v1"],
    "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
    "controller": ["did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY"],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY#verkey-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
        "publicKeyMultibase": "zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpYAZPD1M9tvwyzE"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY#verkey-1"
    ]
  },
  "didDocumentMetadata": {
    "created": "2022-09-16T11:15:20Z",
    "versionId": "204e296f-d55b-4552-9fec-0b242a689f96",
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY/resources/4a71319b-00b1-4db9-bc05-56dc426f7062",
        "resourceCollectionId": "zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
        "resourceId": "4a71319b-00b1-4db9-bc05-56dc426f7062",
        "resourceName": "ExampleStatusList2021",
        "resourceType": "StatusList2021Revocation",
        "mediaType": "application/json",
        "created": "2022-09-16T11:15:52Z",
        "checksum": "0f503dfbff29de9ac74fd07f1720ba560eb388e28062367884890c311736ec9d",
        "previousVersionId": "",
        "nextVersionId": ""
      }
    ]
  }
}
```

### Creating new StatusList2021 Resource Version

An issuer may want to regularly update the bitstring whenever there is a certain amount of revocation status updates. The issuer will therefore need to **create a new version for the initial StatusList2021 Resource**.

Resources with the same Collection ID and name are grouped into version sets. Each resource in such a set has a link to the previous version (except the first version) and the next version (if it's not the most recent version).

To create a resource and mark it as a new version within a particular group, it is necessary to use the same `collection-id, name` and `type` as in the previous version. Links between versions will be created automatically.

New versions have dedicated unique IDs and can be referenced and retrieved as any other resources.

For example:

```json
{
  "kms": "local",
  "payload": {
    "collectionId": "zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY", // Same Collection ID
    "id": "6922ae19-777e-4e05-8b10-8a2f0a2d418d", // New unique ID
    "name": "ExampleStatusList2021", // Same name
    "resourceType": "StatusList2021Revocation", // Same Resource Type
    "data": "hwbWB8FnRwXxmxk" // New base 64 encoded value with updated bitstring
  },
  "signInputs": [
    {
      "verificationMethodId": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY#verkey-1", // Same Verification key
      "keyType": "Ed25519",
      "privateKeyHex": "0f5c124886178037952e87e0cdc55d185732577fca19ae877e64ac9ab24a0cc534e5326e70f1a42d785d93048aee806c359ec75a7b06f39253befd1746708438"
    }
  ]
}
```

Resulting in the following metadata syntax:

```json
{
  "didDocument": {
    "@context": ["https://www.w3.org/ns/did/v1"],
    "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
    "controller": ["did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY"],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY#verkey-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
        "publicKeyMultibase": "zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpYAZPD1M9tvwyzE"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY#verkey-1"
    ]
  },
  "didDocumentMetadata": {
    "created": "2022-09-16T11:15:20Z",
    "versionId": "204e296f-d55b-4552-9fec-0b242a689f96",
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY/resources/4a71319b-00b1-4db9-bc05-56dc426f7062",
        "resourceCollectionId": "zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
        "resourceId": "4a71319b-00b1-4db9-bc05-56dc426f7062",
        "resourceName": "ExampleStatusList2021",
        "resourceType": "StatusList2021Revocation",
        "mediaType": "application/json",
        "created": "2022-09-16T11:15:52Z",
        "checksum": "0f503dfbff29de9ac74fd07f1720ba560eb388e28062367884890c311736ec9d",
        "previousVersionId": "",
        "nextVersionId": "6922ae19-777e-4e05-8b10-8a2f0a2d418d"
      },
      {
        "resourceURI": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY/resources/6922ae19-777e-4e05-8b10-8a2f0a2d418d",
        "resourceCollectionId": "zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
        "resourceId": "6922ae19-777e-4e05-8b10-8a2f0a2d418d",
        "resourceName": "ExampleStatusList2021",
        "resourceType": "StatusList2021Revocation",
        "mediaType": "application/json",
        "created": "2022-10-16T09:04:17Z",
        "checksum": "0f503dfbff29de9ac74fd07f1720ba560eb388e28062367884890c311736ec9d",
        "previousVersionId": "4a71319b-00b1-4db9-bc05-56dc426f7062",
        "nextVersionId": ""
      }
    ]
  }
}
```
