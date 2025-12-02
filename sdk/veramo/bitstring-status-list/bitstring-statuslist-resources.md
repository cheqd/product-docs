---
description: >-
  Create a Bitstring Status List on cheqd, stored as a DID-Linked Resource
  (DLR), using Veramo.
---

# Create Status List

The Bitstring Status List Specification indicates that it may be desirable to store the actual StatusList using something like a [Content Distribution Network](https://w3c-ccg.github.io/vc-status-list-2021/#content-distribution-networks) to lessen the load on the server maintained by the issuer to return a result in real-time.

Using cheqd's Resource Module, the same benefits may be achieved. In fact, storing a StatusList as an on-ledger Resource is a much better application of technology than using a Verifiable Credential for the same purpose.

By storing a StatusList on the cheqd Network as a Resource, it creates a much more resilient and decentralised mechanism for storing and maintaining the revocation/suspension status of Verifiable Credentials. The [benefits of using the cheqd Resource module over traditional centralised architecture are detailed here](../../../studio/did-linked-resources/understanding-dlrs/context.md).

Moreover, cheqd's Resource Module enables individual Resources to be referenced and retrieved using a DID URL in conformance with DID Core. This is being standardized at [the Trust over IP Foundation](https://trustoverip.org/) within a specification called [DID URLs for Digital Resources](https://wiki.trustoverip.org/display/HOME/DID-Linked+Resources+Specification).

### Creating a StatusList Resource using Veramo SDK for cheqd

Using the cheqd Resource module, the same content and semantics of Bistring Status List can be replicated, with additional benefits of enabling DID Resolvers to fetch the contents of the StatusList.

#### Step 1: Create a DID and DID Document with keys to manage the StatusList

You can follow the [tutorial to create a DID and DID Document here](../dids/create-did.md).

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

#### Step 2: Compiling a Bitstring Status List Payload file

Below are is an example of unencrypted Status List Payload file:&#x20;

<details>

<summary>BitstringStatusList</summary>

```json
{
    "kms": "local",
    "issuerDid": "did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY",
    "statusListName": "status-list-unencrypted",
    "statusPurpose": ["revocation", "suspension", "message"],
    "statusSize": 2,
    "statusMessages": [
		    { "status": "0x0", "message": "valid" },
		    { "status": "0x1", "message": "revoked" },
		    { "status": "0x2", "message": "suspended" },
		    { "status": "0x3", "message": "unknown" }
	    ],
    "encrypted": false,
    "statusListEncoding": "base64url"
}

```

</details>

The code snippet above shows a Bitstring Status List Payload file which supports both Revocation and Suspension. With Bitstring Status List with `statusSize` as 2, the same list can be used for revocation, suspension or any other message as determined by the `statusMessages` . The payload indicates to the ledger that the Resource should is unencrypted.

For encrypted status list example, check [charge.md](../payments/charge.md "mention")

Where the fields within the payload have the following meaning:

<table><thead><tr><th width="248.33333333333331">Parameter</th><th>Value</th><th>Description</th></tr></thead><tbody><tr><td><code>"kms"</code></td><td><code>"local"</code></td><td>This indicates that a local Key Management System (KMS) is being used for cryptographic operations, ensuring secure key handling.</td></tr><tr><td><code>"issuerDid"</code></td><td><code>did:cheqd:testnet:zGgLTsq96mTsFcFBUCxX6k4kc5i5RNpY</code></td><td>Specifies the issuer's Decentralized Identifier (DID), uniquely identifying the entity responsible for managing and issuing verifiable credentials.</td></tr><tr><td><code>"statusListName"</code></td><td><code>"status-list-unencrypted"</code></td><td>Names the Bitstring Status List, providing context for its purpose.</td></tr><tr><td><code>"statusPurpose"</code></td><td><code>["revocation", "suspension", "message"]</code> </td><td>Defines the purposes of the status list as Bitstring status list supports multiple purposes.</td></tr><tr><td><code>"statusSize"</code></td><td><code>2</code></td><td>Defines the size of each bit in the Bitstring Status List.</td></tr><tr><td><code>"statusMessages"</code></td><td><p></p><pre class="language-json5"><code class="lang-json5">[{ "status": "0x0", "message": "valid" },
{ "status": "0x1", "message": "revoked" },
{ "status": "0x2", "message": "suspended" },
{ "status": "0x3", "message": "unknown" }]
</code></pre></td><td>Array of "status" and "message" which defines the message for each bit value. Must be 2^StatusSize </td></tr><tr><td><code>"encrypted"</code></td><td><code>false</code></td><td>Indicates that the Verifiable Credential Status List, crucially, <strong>is unencrypted.</strong> This can be either <strong>true (encrypted)</strong> or <strong>false (unencrypted).</strong></td></tr><tr><td><code>"statusListEncoding"</code></td><td><code>base64url</code></td><td>The default encoding for BitStringStatusList</td></tr></tbody></table>

{% hint style="info" %}
Note: If an issuer wants to create multiple StatusLists under the same DID, they must have **unique** and **distinct names**.
{% endhint %}

#### Step 3: Submit a create Status List transaction to the ledger

Once an Issuer has compiled the Payload file, they can submit this to the ledger using the following transaction:

```bash
veramo execute -m cheqdCreateStatusList --argsFile path/to/payload.json
```

The ledger will acknowledge that this Bitstring status list is created and published. &#x20;

#### Note association between DID and Resource

Once created, the BitstringStatusList Resource will be associated with the parent DID, and referenced in the DID Document Metadata as follows:

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
        "resourceName": "status-list-unencrypted",
        "resourceType": "BitstringStatusListCredential",
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

### Creating new BitstringStatusList Resource Version

An issuer may want to regularly update the bitstring whenever there is a certain amount of revocation/suspension status updates. The issuer will therefore need to **create a new version for the initial BitstringStatusList Resource**.

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
    "name": "status-list-unencrypted", // Same name
    "resourceType": "BitstringStatusListCredential", // Same Resource Type
    "data": "hwbWB8FnRwXxmxk...." // New base 64 encoded value with updated bitstring
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
        "resourceName": "status-list-unencrypted",
        "resourceType": "BitstringStatusListCredential",
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
        "resourceName": "status-list-unencrypted",
        "resourceType": "BitstringStatusListCredential",
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
