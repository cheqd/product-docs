# ❌ StatusList2021 support

The [Status List 2021 Specification](https://w3c-ccg.github.io/vc-status-list-2021/#conceptual-framework) is a working document from the W3C to support a privacy-preserving, space-efficient, and high-performance mechanism for publishing status information such as suspension or revocation of JSON and JSON-LD Verifiable Credentials.

## Understanding Status List 2021

The [Status List 2021 Specification](https://w3c-ccg.github.io/vc-status-list-2021/) utilises [bitstrings](https://w3c-ccg.github.io/vc-status-list-2021/#conceptual-framework) to represent whether a Verifiable Credential has been suspended/revoked or not. A bitstring can be thought of as a long list of 1s and 0s, where, if the binary value of the position in the list is 1 (one), the [verifiable credential](https://w3c-ccg.github.io/vc-status-list-2021/#dfn-verifiable-credentials) is revoked, if it is 0 (zero) it is not revoked.

<figure><img src="../../../.gitbook/assets/StatusList21 Bitstring.png" alt="Graphic showing the StatusList2021 bitstring"><figcaption><p>Graphic showing the StatusList 2021 Bitstring</p></figcaption></figure>

Each issued Credential correlates with a position and index on the bitstring, so that a verifier will be able to correlate the value within the Credential against the public bitstring to ascertain whether the Credential has been revoked or not, using a [validate algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#validate-algorithm) as well as a [bitstring expansion algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#bitstring-expansion-algorithm).

### Where is the StatusList published?

The issuer keeps a bitstring list of all Verifiable Credentials it has issued. The StatusList is published by the issuer **in the format of its own Verifiable Credential.** This Verifiable Credential is generally hosted publicly on a centralised server or domain to enable third-party read-access.

The following JSON syntax shows the common format for creating a StatusList2021 definition, using a Verifiable Credential.

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/vc/status-list/2021/v1"
  ],
  "id": "https://example.com/credentials/status/3",
  "type": ["VerifiableCredential", "StatusList2021Credential"],
  "issuer": "did:example:12345",
  "issued": "2021-04-05T14:27:40Z",
  "credentialSubject": {
    "id": "https://example.com/status/3#list",
    "type": "StatusList2021",
    "statusPurpose": "revocation",
    "encodedList": "H4sIAAAAAAAAA-3BMQEAAADTCoPVPbQwfoAAAAAAAAAAAAAAAAAAAAIC3AYbSVKsAQAAA"
  },
  "proof": { ... }
```

The bitstring here is stored in the `encodedList` property. This value _MUST_ be a GZIP-compressed base-64 encoded bitstring value for the associated range of Verifiable Credential status values.

{% hint style="info" %}
Note: The uncompressed bitstring _MUST_ be at least 16KB in size to maintain herd privacy for the holder.
{% endhint %}

More specifically, each value has the following definition and use:

| Property                          | Description                                                                                                                                                                                                                                                                                        |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                              | The verifiable credential that contains the status list _MAY_ express an `id` property that matches the value specified in `statusListCredential` for the corresponding `StatusList2021Entry` (see [2.1 StatusList2021Entry](https://w3c-ccg.github.io/vc-status-list-2021/#statuslist2021entry)). |
| `type`                            | The verifiable credential that contains the status list _MUST_ express a `type` property that includes the `StatusList2021Credential` value.                                                                                                                                                       |
| `credentialSubject.type`          | The `type` of the credential subject, which is the status list, _MUST_ be `StatusList2021`.                                                                                                                                                                                                        |
| `credentialSubject.statusPurpose` | The purpose of the status entry _MUST_ be a string. While the value of the string is arbitrary, the following values _MUST_ be used for their intended purpose: **revocation** or **suspension.**                                                                                                  |
| `credentialSubject.encodedList`   | The `encodedList` property of the credential _MUST_ be the GZIP-compressed, base-64 encoded bitstring values for the associated range of verifiable credential status values. The uncompressed bitstring _MUST_ be at least 16KB in size.                                                          |

When an issuer wants to issue a Verifiable Credential relying on StatusList2021, they must include the following `credentialStatus` properties within the Verifiable Credential itself.

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/vc/status-list/2021/v1"
  ],
  "id": "https://example.com/credentials/23894672394",
  "type": ["VerifiableCredential"],
  "issuer": "did:example:12345",
  "issued": "2021-04-05T14:27:42Z",
  "credentialStatus": {
    "id": "https://example.com/credentials/status/3#94567"
    "type": "StatusList2021Entry",
    "statusPurpose": "revocation",
    "statusListIndex": "94567",
    "statusListCredential": "https://example.com/credentials/status/3"
  },
  "credentialSubject": {
    "id": "did:example:6789",
    "type": "Person"
  },
  "proof": { ... }
}
```

Where each property has the following meaning:

| Property               | Description                                                                                                                                                                                                                                                                                                                                                      |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                   | The constraints on the `id` property are listed in the Verifiable Credentials Data Model specification \[[_VC-DATA-MODEL_](https://w3c-ccg.github.io/vc-status-list-2021/#bib-vc-data-model)]. The value is expected to be a URL that identifies the status information associated with the verifiable credential. It _MUST NOT_ be the URL for the status list. |
| type                   | The `type` property _MUST_ be `StatusList2021Entry`                                                                                                                                                                                                                                                                                                              |
| `statusPurpose`        | The purpose of the status entry _MUST_ be a string. While the value of the string is arbitrary, the following values _MUST_ be used for their intended purpose: **revocation** or **suspension**                                                                                                                                                                 |
| `statusListIndex`      | The `statusListIndex` property _MUST_ be an arbitrary size integer greater than or equal to 0, expressed as a string. The value identifies the bit position of the status of the verifiable credential.                                                                                                                                                          |
| `statusListCredential` | The `statusListCredential` property _MUST_ be a URL to a verifiable credential. When the URL is dereferenced, the resulting verifiable credential _MUST_ have `type` property that includes the `StatusList2021Credential` value.                                                                                                                                |

As such, the issued Verifiable Credential references the centralised parent StatusList Credential controlled and stored by the issuer.

## StatusList2021 using cheqd Resource Module

The [Status List 2021 Specification](https://w3c-ccg.github.io/vc-status-list-2021/) indicates that it may be desirable to store the actual StatusList using something like a [Content Distribution Network](https://w3c-ccg.github.io/vc-status-list-2021/#content-distribution-networks) to lessen the load on the server maintained by the issuer to return a result in real-time.

Using cheqd's Resource Module, the same benefits may be achieved. In fact, storing a StatusList as an on-ledger Resource is a much better application of technology than using a Verifiable Credential for the same purpose.

By storing a StatusList on the cheqd Network as a Resource, it creates a much more resilient and decentralised mechanism for storing and maintaining the revocation/suspension status of Verifiable Credentials. The [benefits of using the cheqd Resource module over traditional centralised architecture are detailed here](../../resources/).

Moreover, cheqd's Resource Module enables individual Resources to be referenced and retrieved using a DID URL in conformance with DID Core. This is being standardized at [the Trust over IP Foundation](https://trustoverip.org/) within a specification called [DID URLs for Digital Resources](https://wiki.trustoverip.org/display/HOME/DID-Linked+Resources+Specification).

### Creating a StatusList Resource using Veramo SDK for cheqd

Using the cheqd Resource module, the same content and semantics of StatusList2021 can be replicated, with additional benefits of enabling DID Resolvers to fetch the contents of the StatusList.

#### Create a DID and DID Document with keys to manage the StatusList

You can follow the [tutorial to create a DID and DID Document here](../../../tutorials/did-operations/create-a-did.md).

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

On Unix systems, you can use the following command:

```bash
$ base64 -w 0 <path-to-the-resource-file>
```

Example:

```bash
$ base64 -w 0 credentialsubjectencondedlist.txt
SGVsbG8sIHdvcmxk
```

#### Create a unique ID for the StatusList Resource

[UUIDs are used to identify Resources](../../resources/technical-composition-of-did-linked-resources/creating-a-resource.md). On Unix systems, the `uuidgen` tool can be used to generate a new UUID:

```bash
$ uuidgen
4a71319b-00b1-4db9-bc05-56dc426f7062
```

#### Create a new Resource for the StatusList

```json
{
  "kms": "local",
  "payload": {
    "collectionId": "zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
    "id": "4a71319b-00b1-4db9-bc05-56dc426f7062",
    "name": "ExampleStatusList2021",
    "resourceType": "StatusList2021Revocation",
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

|                  |                                                                                                                                                    |
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
    "versionId": "4E106C7485BA847BBB4032A7FD019432EE048F0531887BF6B3DE97739CC39D62",
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
        "previousVersionId": null,
        "nextVersionId": null
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
    "versionId": "4E106C7485BA847BBB4032A7FD019432EE048F0531887BF6B3DE97739CC39D62",
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
        "previousVersionId": null,
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
        "nextVersionId": null
      }
    ]
  }
}
```

### Issuing a Verifiable Credential referencing cheqd StatusList

Follow the [instructions for issuing a Verifiable Credential here](../../../tutorials/verifiable-credentials-and-presentations/json-jwt/verifiable-credentials.md), including the credentialStatus information, as shown in the example below:

```json
{
  "credentialSubject": {
    "name": "Alice",
    "id": "did:key:z6Mkktr27VZ7TTFoTsD9p79JwtGnQDgJWKGrxJ79quE7M5Yx"
  },
  "issuer": {
    "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY"
  },
  "type": [
    "VerifiableCredential",
    "Profile"
  ],
  "credentialStatus": {
    "id": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY/resources/4a71319b-00b1-4db9-bc05-56dc426f7062#94567"
    "type": "StatusList2021Entry",
    "statusPurpose": "StatusList2021Revocation",
    "statusListIndex": "94567",
    "statusListResource": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY/resources/4a71319b-00b1-4db9-bc05-56dc426f7062"
  },
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://veramo.io/contexts/profile/v1"
  ],
  "issuanceDate": "2022-07-28T15:25:10.000Z",
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdmVyYW1vLmlvL2NvbnRleHRzL3Byb2ZpbGUvdjByb2ZpbGUvdjEiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlByb2ZpbGUiXSwiY3JlZGVudGlhbFN1YmplY3QiOnsibmFtZSI6IkFsaWNlIn19LCJzdWIiOiJkaWQ6a2V5Ono2TWtrdHIyN1ZaN1RURm9Uc25RRGdKV0tHc0Q5cDc5Snd0R25RRGdKV0tHcnhKNzlxdUU3TTVZeCIsIm5iZiI6MTY1OTAyMTkxMCwiaXNzIjoiZGlkOmNoZXFkOm1haW5uZXQ6ekFYd3dxWnpoQ1pBMUw3N1pCYThmaFZOakw5TVFDSFgifQ.MRqlKuFQzpjLvsW3C2ZSBEf5jfvJCPQBwl-gP1P8bRfNSvjxj9H3eDgDmEf5jfvJCPQBwUDltBr-ZQ3Q7SKVSvCaJHV8TnUzBA"
  }
}
```

Where the values within the credentialSubject field are as follows:

| Property               | Description                                                                                                                                                                                                            |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                   | A specific DID URL dereferencing to the actual status of the Credential                                                                                                                                                |
| type                   | The `type` property _MUST_ be `StatusList2021Entry`                                                                                                                                                                    |
| `statusPurpose`        | The purpose of the status entry _MUST_ be a string. While the value of the string is arbitrary, the following values _MUST_ be used for their intended purpose: **revocation** or **suspension**                       |
| `statusListIndex`      | The `statusListIndex` property _MUST_ be an arbitrary size integer greater than or equal to 0, expressed as a string. The value identifies the bit position of the status of the verifiable credential.                |
| `statusListCredential` | The `statusListCredential` property _MUST_ is a DID URL to a statusList Resource. When the URL is dereferenced, the resulting Resource _MUST_ have `type` property that includes the `StatusList2021Credential` value. |

#### Validate Algorithm for cheqd StatusList

The following process, or one generating the exact output, _MUST_ be followed when validating a verifiable credential that is contained in a cheqd StatusList Resource.

1. Let **credentialToValidate** be a verifiable credentials containing a `credentialStatus` entry that is a StatusList2021Entry, associated with an entry in a bitstring.
2. Let **status purpose** be the value of `statusPurpose` in the `credentialStatus` entry in the **credentialToValidate**.
3. Verify all proofs associated with the **credentialToValidate**. If a proof fails, return a validation error.
4. Verify that the **status purpose** matches the `resourceType` value in the **StatusList2021 Resource**.
5. Let **compressed bitstring** be the value of the `encodedList` property of the **StatusList2021 Resource**.
6. Let **credentialIndex** be the value of the `statusListIndex` property of the bitstring in the **StatusList2021 Resource**.
7. Generate a **revocation bitstring** by passing **compressed bitstring** to the [Bitstring Expansion Algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#bitstring-expansion-algorithm).
8. Let **status** be the value of the bit at position **credentialIndex** in the **revocation bitstring**.
9. Return `true` if **status** is 1, `false` otherwise.
