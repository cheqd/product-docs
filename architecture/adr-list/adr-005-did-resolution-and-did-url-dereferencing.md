---
description: >-
  Rules and architecture of DID resolver to handle DID URL queries, paths and
  fragments
---

# ADR 005: DID Resolution & DID URL Dereferencing

## Status

| Category                  | Status                                          |
| ------------------------- | ----------------------------------------------- |
| **Authors**               | Alex Tweeddale, Abdulla Ashurov, Andrew Nikitin |
| **ADR Stage**             | PROPOSED                                        |
| **Implementation Status** | DRAFT                                           |
| **Start Date**            | 2023-06-05                                      |
| **Last Updated**          | 2023-06-06                                      |

## Summary

The [`did:cheqd` method ADR](adr-001-cheqd-did-method.md) defines how DIDs are created and read from ledger. According to the [W3C DID Core specification](https://w3c.github.io/did-core/), DID methods are expected to provide [standards-compliant methods of DID and DID Document ("DIDDoc") production](https://w3c.github.io/did-core/#production-and-consumption).

The [cheqd DID Resolver](https://github.com/cheqd/did-resolver) is designed to implement the [W3C DID _Resolution_ specification](https://w3c-ccg.github.io/did-resolution/) for [`did:cheqd`](adr-001-cheqd-did-method.md) method.

Part of the [W3C DID _Resolution_ specification](https://w3c-ccg.github.io/did-resolution/) is the notion of DID URL Dereferencing whereby a DID URL identifies either a primary or secondary resource which is returned directly.

## Understanding DID resolution vs DID URL dereferencing

When you **resolve** a DID you get a DID Document.

When you **dereference** a DID, you may get the DID Document, a portion of a DID document, or the resource at the end of a service endpoint (if the DID contains a service component).

You can only **resolve** a DID to return a DID document, and you can only **dereference** a DID reference to return whatever resource is referenced.

## Process for dereferencing a DID URL

Normal dereferencing can be conceived in two steps:

1. A [DID](https://www.w3.org/TR/did-core/#dfn-decentralized-identifiers) is resolved to a [DID Document](https://www.w3.org/TR/did-core/#dfn-did-documents);
2. A resource within / associated with the [DID Document](https://www.w3.org/TR/did-core/#dfn-did-documents) is identified, based on the portion of the [DID URL](https://www.w3.org/TR/did-core/#dfn-did-urls) that follows the [DID](https://www.w3.org/TR/did-core/#dfn-decentralized-identifiers) (path, query, fragment as defined by the ABNF in [section 3.2 of the DID 1.0 specification](https://www.w3.org/TR/did-core/#did-url-syntax).).

<figure><img src="../../.gitbook/assets/DID URL dereference diagram.png" alt=""><figcaption><p>DID URL dereferencing diagram</p></figcaption></figure>

## DID Document Dereferencing Operations

### Simple DID Resolution

**Endpoint**: `/1.0/identifiers/<did>`

Current implementation returns here the full DIDDoc as in [example](adr-005-did-resolution-and-did-url-dereferencing.md#resolve-representation-function). By default, in `didDocumentMetadata` section will be the metadata for the latest version of DIDDoc and the list of all resources.

### DID URL with fragment

**Endpoint**: `/1.0/identifiers/<did>#example`

If a DID URL contains a fragment `#`. the logic is similar to the general web browser logic where `#` symbols can be used for linking to the particular document section.&#x20;

For the DIDDoc, you can equate a specific section within a webpage to a specific section inside the DIDDoc, for example `verificationMethod`. To resolve to a particular section, we need to use a request which specifies the section itself.&#x20;

Take the example excerpt from a DIDDoc below:

```json
"verificationMethod": [
      {
        "id": "did:cheqd:testnet:97e351e6-2d9d-4314-82ec-e0d12bc5de43#key-1",
        "type": "JsonWebKey2020",
        "controller": "did:cheqd:testnet:97e351e6-2d9d-4314-82ec-e0d12bc5de43",
        "publicKeyJwk": {
          "crv": "Ed25519",
          "kty": "OKP",
          "x": "q8-CHj4_nIYo8tK5RdjYbXlsTUnwW_i4gIEclps2i2o"
        }
      }
    ],
```

Here, to dereference to this verificationMethod, we need to pass `key-1` as a fragment parameter:

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:97e351e6-2d9d-4314-82ec-e0d12bc5de43%23key-1
```

</details>

<details>

<summary>Response format</summary>

```json
{
  "@context": "https://w3id.org/did-resolution/v1",
  "dereferencingMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2023-04-27T14:12:44Z",
    "did": {
      "didString": "did:cheqd:testnet:97e351e6-2d9d-4314-82ec-e0d12bc5de43",
      "methodSpecificId": "97e351e6-2d9d-4314-82ec-e0d12bc5de43",
      "method": "cheqd"
    }
  },
  "contentStream": {
    "@context": [
      "https://www.w3.org/ns/did/v1"
    ],
    "id": "did:cheqd:testnet:97e351e6-2d9d-4314-82ec-e0d12bc5de43#key-1",
    "type": "JsonWebKey2020",
    "controller": "did:cheqd:testnet:97e351e6-2d9d-4314-82ec-e0d12bc5de43",
    "publicKeyJwk": {
      "crv": "Ed25519",
      "kty": "OKP",
      "x": "q8-CHj4_nIYo8tK5RdjYbXlsTUnwW_i4gIEclps2i2o"
    }
  },
  "contentMetadata": {
    "created": "2023-03-01T08:47:07.919899771Z",
    "updated": "2023-03-01T08:52:27.785774183Z",
    "versionId": "cfe2f51f-8ec5-4fd8-8ab9-61859de879f4"
  }
}
```

</details>

### DID URL with query

All the queries can be divided into 2 main groups:

* Queries for fetching a resource referenced within the DIDDoc body.
* Queries for fetching an external resource associated with the DIDDoc, referenced within the DIDDoc metadata.

{% hint style="info" %}
Note: in instances where there is no `versionId` or `versionTime` parameters specfied, we have set the default dereferencing rules to fetch the latest version of a resource, **if it is uniquely identified by the parameters in the query**.&#x20;
{% endhint %}

**Common parameter validation**

* parameters `metadata` and `resourceMetadata` are designed to be a bool variables, like `true` or `false`.
* parameters `versionTime` and `resourceVersionTime` are designed as string representation of time and can be in 2 formats, RFC3339 and RFC3339Nano. Examples are ("2006-01-02T15:04:05Z07:00" and "2006-01-02T15:04:05.999999999Z07:00")

### **Query parameters**

Here we have an ability to specify different parameters to filter to particular parts of a DIDDoc.&#x20;

| Parameter      | Type                                                                                                                                                                                                                  | Description                                                                                                            |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `versionId`    | A [string](https://infra.spec.whatwg.org/#string) that conforms to a method specific unique identifier format.                                                                                                        | Used to filter to a specific version id of the DIDDoc                                                                  |
| `versionTime`  | A [JSON String](https://www.rfc-editor.org/rfc/rfc8259#section-7) serialized as an [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) normalized to UTC 00:00:00 and without sub-second decimal precision. | Used to filter to a specific version of the DIDDoc at a point in time                                                  |
| `service`      | Using a [string](https://infra.spec.whatwg.org/#string) from the service`id` property within the DID document, this will dereference to the associated `serviceEndpoint`                                              | Used to navigate to a serviceEndpoint of a DIDDoc, using the serviceId as a query parameter                            |
| `transformKeys` | A [string](https://infra.spec.whatwg.org/#string) that identifies the intended key type                                                                                                                               | Used to transform the verificationMethod key material into a different key type, based on the same cryptographic curve |
| `relativeRef`  | A [string](https://infra.spec.whatwg.org/#string) that identifies a particular "secondary" resource appended to the `serviceEndpoint`                                                                                 | Used to fetch a specific part of a serviceEndpoint, such as a particular heading on a website                          |
| `metadata`     | [Boolean](https://infra.spec.whatwg.org/#booleans)                                                                                                                                                                    | Used to fetch the metadata associated with a particular DIDDoc                                                         |

All queries can be chained together to make very specific requests using `&` within the request format. This makes query parameters particularly powerful because they can be used to fetch specific parts of a DID Document or specific resources associated with a DID Document.

For example, the example below queries a particular service endpoint associated with a particular DIDDoc version.&#x20;

```
1.0/identifiers/did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c?resourceId=e577237b-b887-43e8-9540-9097bb00efbb&service=foo
```

**VersionId**

If the user knows the particular `versionId` he could ask about it by adding the query `versionId=<uuid of DIDDoc version>` It can be combined with any other queries for building more complex requests. `linkedResourceMetadata` will contain only resources which were active for this version. In other words, resource which were created before the next version in terms of timeline.

**Example**:

<details>

<summary>Request format</summary>

```
/1.0/identifiers/did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c?versionId=ce298b6f-594b-426e-b431-370d6bc5d3ad
```

</details>

<details>

<summary>Response format</summary>

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "@context": "https://w3id.org/did-resolution/v1",
  "didResolutionMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2023-04-27T10:52:00Z",
    "did": {
      "didString": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
      "methodSpecificId": "b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
      "method": "cheqd"
    }
  },
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/suites/ed25519-2018/v1"
    ],
    "id": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c#key-1",
        "type": "Ed25519VerificationKey2018",
        "controller": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
        "publicKeyBase58": "BpVGbTeT26LipAdk26DBZrmJx2939i9gZS5VxGt1zZQ6"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c#key-1"
    ],
    "service": [
      {
        "id": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c#bar",
        "type": "LinkedDomains",
        "serviceEndpoint": [
          "https://bar.example.com"
        ]
      }
    ]
  },
  "didDocumentMetadata": {
    "created": "2023-03-06T09:36:55.56204903Z",
    "updated": "2023-03-06T09:39:48.496306968Z",
    "deactivated": true,
    "versionId": "ce298b6f-594b-426e-b431-370d6bc5d3ad",
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c/resources/5e16a3f9-7c6e-4b6b-8e28-20f56780ee25",
        "resourceCollectionId": "b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
        "resourceId": "5e16a3f9-7c6e-4b6b-8e28-20f56780ee25",
        "resourceName": "TestResource",
        "resourceType": "TestType",
        "mediaType": "text/plain; charset=utf-8",
        "resourceVersion": "1.0",
        "created": "2023-03-06T09:53:44.467029472Z",
        "checksum": "64ec88ca00b268e5ba1a35678a1b5316d212f4f366b2477232534a8aeca37f3c",
        "previousVersionId": null,
        "nextVersionId": null
      }
    ]
  }
}
</code></pre>

</details>

**VersionTime**

The user could ask resolver for the nearest DIDDoc which was created/updated before `versionTime` value. The logic about `linkedResourceMetadata` is the same. Here, resolver figure out what the `versionId` is the nearest one for `versionTime` and shows all the active resources. If you want to file resources also, you can use [`resourceVersionTime`](adr-005-did-resolution-and-did-url-dereferencing.md#resourceversiontime) parameter and combine the query with `versionTime`.

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c?versionTime=2023-03-06T09:53:44.46Z
```

</details>

<details>

<summary>Response format</summary>

```json
{
  "@context": "https://w3id.org/did-resolution/v1",
  "didResolutionMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2023-04-27T10:52:58Z",
    "did": {
      "didString": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
      "methodSpecificId": "b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
      "method": "cheqd"
    }
  },
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/suites/ed25519-2018/v1"
    ],
    "id": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c#key-1",
        "type": "Ed25519VerificationKey2018",
        "controller": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
        "publicKeyBase58": "BpVGbTeT26LipAdk26DBZrmJx2939i9gZS5VxGt1zZQ6"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c#key-1"
    ],
    "service": [
      {
        "id": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c#bar",
        "type": "LinkedDomains",
        "serviceEndpoint": [
          "https://bar.example.com"
        ]
      }
    ]
  },
  "didDocumentMetadata": {
    "created": "2023-03-06T09:36:55.56204903Z",
    "updated": "2023-03-06T09:39:48.496306968Z",
    "deactivated": true,
    "versionId": "ce298b6f-594b-426e-b431-370d6bc5d3ad",
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c/resources/5e16a3f9-7c6e-4b6b-8e28-20f56780ee25",
        "resourceCollectionId": "b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
        "resourceId": "5e16a3f9-7c6e-4b6b-8e28-20f56780ee25",
        "resourceName": "TestResource",
        "resourceType": "TestType",
        "mediaType": "text/plain; charset=utf-8",
        "resourceVersion": "1.0",
        "created": "2023-03-06T09:53:44.467029472Z",
        "checksum": "64ec88ca00b268e5ba1a35678a1b5316d212f4f366b2477232534a8aeca37f3c",
        "previousVersionId": null,
        "nextVersionId": null
      }
    ]
  }
}
```

</details>

**TransformKeys**

The transformKeys parameter changes the representation of `verificationMethod` section in DIDDoc. It allows you to change `publicKey` representation into the one of next variants. To do so, just pass the parameter `transformKeys` with one of the following values:

* Ed25519VerificationKey2020
* Ed25519VerificationKey2018
* JsonWebKey2020

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c?transformKeys=Ed25519VerificationKey2020
```

</details>

<details>

<summary>Response format</summary>

```json
{
  "@context": "https://w3id.org/did-resolution/v1",
  "didResolutionMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2023-04-27T10:53:41Z",
    "did": {
      "didString": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
      "methodSpecificId": "b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
      "method": "cheqd"
    }
  },
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/suites/ed25519-2018/v1"
    ],
    "id": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
        "publicKeyMultibase": "z6MkqGkKBhttMdqBvfUShfB2QxKJmbQtZbQ3FSzRnYr2unBU"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c#key-1"
    ],
    "service": [
      {
        "id": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c#bar",
        "type": "LinkedDomains",
        "serviceEndpoint": [
          "https://bar.example.com"
        ]
      }
    ]
  },
  "didDocumentMetadata": {
    "created": "2023-03-06T09:36:55.56204903Z",
    "updated": "2023-03-06T09:59:22.04507182Z",
    "deactivated": true,
    "versionId": "f790c9b9-4817-4b31-be43-b198e6e18071",
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c/resources/5e16a3f9-7c6e-4b6b-8e28-20f56780ee25",
        "resourceCollectionId": "b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
        "resourceId": "5e16a3f9-7c6e-4b6b-8e28-20f56780ee25",
        "resourceName": "TestResource",
        "resourceType": "TestType",
        "mediaType": "text/plain; charset=utf-8",
        "resourceVersion": "1.0",
        "created": "2023-03-06T09:53:44.467029472Z",
        "checksum": "64ec88ca00b268e5ba1a35678a1b5316d212f4f366b2477232534a8aeca37f3c",
        "previousVersionId": null,
        "nextVersionId": null
      }
    ]
  }
}
```

</details>

**Service**

Service - is a DIDDoc section which includes `serviceEndpoint` field with URL inside. As a result, pair `service=<service-id>` will  redirect to the `serviceEndpoint` value.

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c?service=bar
```

</details>

<details>

<summary>Response format</summary>

```
Will be redirected to https://bar.example.com
```

</details>

**RelativeRef**

`relativeRef` is an additional part for `serviceEndpoint` and can be used only with [service](adr-005-did-resolution-and-did-url-dereferencing.md#service) parameter. It combines `serviceEndpoint` URL with `relativeRef` value and does the redirect to the URL.

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c?service=bar&relativeRef=%2Ffoo
```

</details>

<details>

<summary>Response format</summary>

```
Will be redirect to https://bar.example.com/foo
```

</details>

**Metadata**

By default, without `metadata=true` pair `DID-resolver` returns only particular DIDDoc. If you want to specifically fetch **only the metadata for a DIDDoc**, you can pass an additional query. It can be helpful in the instance, for example, where you want to get all the resources which were created for the exact `versionId` or before `versionTime`.

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c?versionId=ce298b6f-594b-426e-b431-370d6bc5d3ad&metadata=true
```

</details>

<details>

<summary>Response format</summary>

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "@context": "https://w3id.org/did-resolution/v1",
  "dereferencingMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2023-04-27T10:58:56Z",
    "did": {
      "didString": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
      "methodSpecificId": "b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
      "method": "cheqd"
    }
  },
  "contentStream": {
    "created": "2023-03-06T09:36:55.56204903Z",
    "updated": "2023-03-06T09:39:48.496306968Z",
    "deactivated": true,
    "versionId": "ce298b6f-594b-426e-b431-370d6bc5d3ad",
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c/resources/5e16a3f9-7c6e-4b6b-8e28-20f56780ee25",
        "resourceCollectionId": "b5d70adf-31ca-4662-aa10-d3a54cd8f06c",
        "resourceId": "5e16a3f9-7c6e-4b6b-8e28-20f56780ee25",
        "resourceName": "TestResource",
        "resourceType": "TestType",
        "mediaType": "text/plain; charset=utf-8",
        "resourceVersion": "1.0",
        "created": "2023-03-06T09:53:44.467029472Z",
        "checksum": "64ec88ca00b268e5ba1a35678a1b5316d212f4f366b2477232534a8aeca37f3c",
        "previousVersionId": null,
        "nextVersionId": null
      }
    ]
  },
  "contentMetadata": {}
}
</code></pre>

</details>

### **Errors**

* `NotFoundError` raises if there is now resource/metadata with requested set of parameters or the result is ambiguous
* `RepresentationNotSupported` raises if there are:
  * unsupported query parameters
  * value for parameters are empty
  * unsupported `transformKeys` value
  * `metadata` or `resourceMetadata` parameter has value not only `true` or `false`
  * `relativeRef` parameter is used without `service` one. They must be used only together
* `InvalidDidUrl` raises if there are:
  * `versionId` or `resourceId` are not valid UUID strings
  * `versionTime` or `resourceVersionTime` are not in RFC3339 or RFC3339Nano formats.
  * `resourceVersionTime` is used without any other resource parameters.

## Dereferencing to a DID-Linked Resource

cheqd's [ADR: 002 DID-Linked Resources](adr-002-did-linked-resources.md) introduces a new concept to the SSI ecosystem - namely, resources that can be associated with a DID and fetched using existing DID URL dereferencing patterns.

The diagram below shows how a DID-Linked Resource sits beneath a DID and can be fetched using a DID URL.&#x20;

<figure><img src="../../.gitbook/assets/DID-Linked-Resources-Relationship.png" alt=""><figcaption><p>DID URL dereferencing to DID-Linked Resource diagram</p></figcaption></figure>

### DID-Linked Resource resolution rules

Requests to fetch on-ledger cheqd Resources are considered as a DID URL Dereferencing scenario it uses [DID URL paths](https://w3c.github.io/did-core/#path) to lead to a Resource object, rather than a DIDDoc.

On the other hand, on-ledger cheqd Resources _metadata_ requests are handled like DID URL Resolution since the result is a subsection of `didDocumentMetadata` specific to that resource.

API endpoints related to on-ledger cheqd Resources are described below. All of these request _types_ are `GET` requests, with `POST` and `PUT` requests disallowed.

#### Get a specific Resource

Returns the Resource data/payload stored on ledger for specified resource. `HEAD` request _type_ is also allowed for this endpoint since it can be used for [HTTP content negotiation](https://developer.mozilla.org/en-US/docs/Web/HTTP/Content\_negotiation) phase by client applications. In this case, _only_ the HTTP **Response** headers are returned without the body.

**Endpoint**: `/1.0/identifiers/<did>/resources/<resource_id>`

1. **Request** HTTP headers
   1. `Accept` should allow `*/*` _or_ match the `mediaType` of the Resource
   2. [`Accept-Encoding`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Encoding) may be allowed compression methods (e.g., `gzip`, `compress`, etc.)
2. **Response** HTTP headers
   1. Status code `200 OK`
   2. `Content-Type` should be set to `mediaType>` of the Resource
   3. `Content-Encoding` should be set to a valid content compression method in `Accept-Encoding` and response compressed accordingly
   4. [`Content-Length`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Length) should be set to the Resource size, in decimal bytes
3. **Response** HTTP body
   1. Resource, encoded correctly according to the `mediaType`

#### Get _metadata_ for a specific resource

Return metadata for a specified resource. This is effectively a portion of the DIDDoc Metadata block.

**Endpoint**: `/1.0/identifiers/<did>/resources/<resource_id>/metadata`

#### Get metadata for _all_ Resources linked to a DID

Returns metadata for all Resources directly linked to/controlled by a DID. This is effectively the full `linkedResourceMetadata` section in DIDDoc Metadata block.

**Endpoint**: `/1.0/identifiers/<did>/resources/all`

**Alternative endpoints**

1. `/1.0/identifiers/<did>/resources/`
   1. Status code `301`
   2. Redirect to `/resources/all`
2. `/1.0/identifiers/<did>/resources`
   1. Throw an `invalidDidUrl` error

### Query parameters

Here we have an ability to specify different parameters to filter to particular DID-Linked Resources.

| Parameter              | Type                                                                                                                                                                                                                  | Description                                                                                     |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `resourceId`           | A [string](https://infra.spec.whatwg.org/#string) that conforms to a method specific unique identifier format.                                                                                                        | The unique identifier of a particular DID-Linked Resource                                       |
| `resourceCollectionId` | A [string](https://infra.spec.whatwg.org/#string) that conforms to a method specific unique identifier format.                                                                                                        | Can be used to query all resources associated with a DID if combined with resourceMetadata=true |
| `resourceName`         | A [string](https://infra.spec.whatwg.org/#string)                                                                                                                                                                     | The specific name of a DID-Linked Resource                                                      |
| `resourceType`         | A [string](https://infra.spec.whatwg.org/#string)                                                                                                                                                                     | The specific type of a DID-Linked Resource                                                      |
| `resourceVersionTime`  | A [JSON String](https://www.rfc-editor.org/rfc/rfc8259#section-7) serialized as an [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) normalized to UTC 00:00:00 and without sub-second decimal precision. | Used to fetch a version of a resource at a specific point in time                               |
| `checksum`             | A [string](https://infra.spec.whatwg.org/#string)                                                                                                                                                                     | Used to specify a particular resource checksum to demonstrate it is untampered                  |
| `resourceMetadata`     | [Boolean](https://infra.spec.whatwg.org/#booleans)                                                                                                                                                                    | Used to fetch metadata related to a specific resource or group of resources                     |

Like with DIDDoc query parameters, these can all be chained together to create complex requests for specific DID-Linked Resources at particular points in time or associated with particular DIDDoc versions.&#x20;

### Rules and logic for handling ambiguous queries

It is important to understand how our resolver logically handles more complex dereferencing requests. We have set some baseline defaults and rules to ensure a logical and consistent experience for clients who use our resolver.

1. **Ambiguity generally throws an error**

If the request specifies a parameter where there are multiple potential results, such as where the DID has two resources of the same `resourceType` ('String') but `resourceName` is not the same,  an error will be thrown because there is not enough information to discern which resource is being requested.

2. **Multiple versions of the same resource**

If there are multiple resources with the same `resourceType` and `resourceName` but with different `versionIds,` and there is no parameter specified to fetch a particular version, **the resolver will fetch the latest resource by default**.

This is because the query is not ambiguous in terms of discerning which set of resources to dereference to, but is only ambiguous in terms of which version of that resource to fetch.&#x20;

3. **Ambiguity + resourceMetadata=true**

If there is an ambiguous query, such as where there are two resources with the same name but different types, AND there is a resourceMetadata=true parameter, resource data pertaining to all the resources which could potentially be seen as being ambiguous will be returned.&#x20;

For example, in the below example, there are multiple resources with the `resourceType= string`, but with different `resourceName` parameters:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:c1685ca0-1f5b-439c-8eb8-5c0e85ab7cd0?resourceType=String&resourceMetadata=true
```

</details>

<details>

<summary>Response format</summary>

```json
{
    "@context": "https://w3id.org/did-resolution/v1",
    "dereferencingMetadata": {
        "contentType": "application/did+ld+json",
        "retrieved": "2023-04-26T15:38:26Z",
        "did": {
            "didString": "did:cheqd:testnet:c1685ca0-1f5b-439c-8eb8-5c0e85ab7cd0",
            "methodSpecificId": "c1685ca0-1f5b-439c-8eb8-5c0e85ab7cd0",
            "method": "cheqd"
        }
    },
    "contentStream": {
        "created": "2023-01-25T11:58:10.390039347Z",
        "versionId": "e5615fc2-6f13-42b1-989c-49576a574cef",
        "linkedResourceMetadata": [
            {
                "resourceURI": "did:cheqd:testnet:c1685ca0-1f5b-439c-8eb8-5c0e85ab7cd0/resources/9ba3922e-d5f5-4f53-b265-fc0d4e988c77",
                "resourceCollectionId": "c1685ca0-1f5b-439c-8eb8-5c0e85ab7cd0",
                "resourceId": "9ba3922e-d5f5-4f53-b265-fc0d4e988c77",
                "resourceName": "Demo Resource",
                "resourceType": "String",
                "mediaType": "application/json",
                "resourceVersion": "",
                "created": "2023-01-25T12:08:39.633307395Z",
                "checksum": "e1dbc03b50bdb995961dc8843df6539b79d03bf49787ed6462189ee97d27eaf3",
                "previousVersionId": null,
                "nextVersionId": null
            },
            {
                "resourceURI": "did:cheqd:testnet:c1685ca0-1f5b-439c-8eb8-5c0e85ab7cd0/resources/e733ebb7-c8dd-41ed-9d42-33bceea70952",
                "resourceCollectionId": "c1685ca0-1f5b-439c-8eb8-5c0e85ab7cd0",
                "resourceId": "e733ebb7-c8dd-41ed-9d42-33bceea70952",
                "resourceName": "ResourceName",
                "resourceType": "String",
                "mediaType": "text/plain; charset=utf-8",
                "resourceVersion": "",
                "created": "2023-01-25T12:04:52.261619721Z",
                "checksum": "cffd829b06797f85407be9353056db722ca3eca0c05ab0462a42d30f19cdef09",
                "previousVersionId": null,
                "nextVersionId": null
            }
        ]
    },
    "contentMetadata": {}
}
```

</details>

**ResourceId**

`ResourceId` parameter can be used for filtering a particular resource version by specifically identifying its unique ID.

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:b5d70adf-31ca-4662-aa10-d3a54cd8f06c?resourceId=5e16a3f9-7c6e-4b6b-8e28-20f56780ee25
```

</details>

<details>

<summary>Response format</summary>

```
Hello world
```

</details>

**ResourceCollectionId**

`resourceCollectionId` parameter filters all the resource by `collectionId` field. By default cause we are asking for resources for a particular DID it already includes all the resource with the same `collectionId` and this parameter can used mostly as sanity check. Without `resourceMetadata=true` parameter will return the latest created resource if there is only one resource or an unambiguous resource.

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c?resourceCollectionId=d8ac0372-0d4b-413e-8ef5-8e8f07822b2c
```

</details>

<details>

<summary>Response format</summary>

```json
{
    "type": "CL",
    "tag": "TAG",
    "value": {
        "primary": {
            "n": "92511867718854414868106363741369833735017762038454769060600859608405811709675033445666654908195955460485998711087020152978597220168927505650092431295783175164390266561239892662085428655566792056852960599485298025843840058914610127716620252006466964070280255168745873592143068949458568751438337748294055976926080232538440619420568859737673474560851456027625679328271511966332808025880807996449998057729417608399774744254122385012832309402226532031122728445959276178939234308090390331654445053482963947804769291501664200141562885660084823885847247231002821472258218384342423605116504024514572826071246440130942849549441",
            "s": "80388543865249952799447792504739237616187770512259677275061283897050980768551818104137338144380636412773836688624071360386172349725818126495487584981520630638409717065318132420766896092370913800616033623618952639023946750307405126873476182540669638841562357523429245685476919178722373320218824590869735129801004394337640642997250464303104754942997839179333543643110326022824394934965538190976474473353762308333205671176627192797138375084260446324344637548455228161138089974447059481109651156379803576163576511072261388342837813901850712083922506433336723723235701670225584863772222447543742649328218950436824219992164",
            "r": {
                "age": "676933340341980399002624386891134393471002096508227567343731826159610079436978196421307099268754545293545727546242372579987825752872485684085629459107300175443328323289748793060894500514926703654606851666031895448970879827423190730510730624784665299646624113512701254199984520803796529034094958026048762178753193812250643294518237843809104055653333871102658177900702978008644780459400512716361564897282969982554031820285585105004870317861287847206222714589633178648982299799311192432563797220854755882933052881306804544233529886513105815543097685128456041780804442879272476590077760678785460726492895806240870944398",
                "master_secret": "57770757113548032970308439965749734133430520933173186296299026579579930337912607419798836831937319372744879560676750427054135869214212225572618340088847222727882935159356459822445182287686057012197046378986248048722180093079919306125315662058290895629438767985427829790980355162853804522854494960613869765167538645624719923127052541372069255024631093663068055100579264049925388231368871107383977060590248865498902704546409806115171120555709438784189721957301548212242748685629860268468247494986146122636455769804467583612610341632602695197189514316033637331733820369170763954604394734655429769801516997967996980978751"
            },
            "rctxt": "19574881057684356733946284215946569464410211018678168661028327420122678446653210056362495902735819742274128834330867933095119512313591151219353395069123546495720010325822330866859140765940839241212947354612836044244554152389691282543839111284006009168728161183863936810142428875817934316327118674532328892591410224676539770085459540786747902789677759379901079898127879301595929571621032704093287675668250862222728331030586585586110859977896767318814398026750215625180255041545607499673023585546720788973882263863911222208020438685873501025545464213035270207099419236974668665979962146355749687924650853489277747454993",
            "z": "18569464356833363098514177097771727133940629758890641648661259687745137028161881113251218061243607037717553708179509640909238773964066423807945164288256211132195919975343578956381001087353353060599758005375631247614777454313440511375923345538396573548499287265163879524050255226779884271432737062283353279122281220812931572456820130441114446870167673796490210349453498315913599982158253821945225264065364670730546176140788405935081171854642125236557475395879246419105888077042924382595999612137336915304205628167917473420377397118829734604949103124514367857266518654728464539418834291071874052392799652266418817991437"
        }
    },
    "schemaId": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/bae5cb6c-564a-4ed4-8c0e-d5c3b0f8ae0a"
}
```

</details>



**ResourceType**

This parameter is also just a filter by `Type` field through resources. But there is a corner case if the user asks about exact resource (exact data). If after applying all the parameters in request several resources are left with the same `Name` - the latest one will be responded. Otherwise - error `NotFoundError` will be raised.

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c?resourceType=anonCredsCredDef
```

</details>

<details>

<summary>Response format</summary>

```json
{
    "type": "CL",
    "tag": "TAG",
    "value": {
        "primary": {
            "n": "92511867718854414868106363741369833735017762038454769060600859608405811709675033445666654908195955460485998711087020152978597220168927505650092431295783175164390266561239892662085428655566792056852960599485298025843840058914610127716620252006466964070280255168745873592143068949458568751438337748294055976926080232538440619420568859737673474560851456027625679328271511966332808025880807996449998057729417608399774744254122385012832309402226532031122728445959276178939234308090390331654445053482963947804769291501664200141562885660084823885847247231002821472258218384342423605116504024514572826071246440130942849549441",
            "s": "80388543865249952799447792504739237616187770512259677275061283897050980768551818104137338144380636412773836688624071360386172349725818126495487584981520630638409717065318132420766896092370913800616033623618952639023946750307405126873476182540669638841562357523429245685476919178722373320218824590869735129801004394337640642997250464303104754942997839179333543643110326022824394934965538190976474473353762308333205671176627192797138375084260446324344637548455228161138089974447059481109651156379803576163576511072261388342837813901850712083922506433336723723235701670225584863772222447543742649328218950436824219992164",
            "r": {
                "age": "676933340341980399002624386891134393471002096508227567343731826159610079436978196421307099268754545293545727546242372579987825752872485684085629459107300175443328323289748793060894500514926703654606851666031895448970879827423190730510730624784665299646624113512701254199984520803796529034094958026048762178753193812250643294518237843809104055653333871102658177900702978008644780459400512716361564897282969982554031820285585105004870317861287847206222714589633178648982299799311192432563797220854755882933052881306804544233529886513105815543097685128456041780804442879272476590077760678785460726492895806240870944398",
                "master_secret": "57770757113548032970308439965749734133430520933173186296299026579579930337912607419798836831937319372744879560676750427054135869214212225572618340088847222727882935159356459822445182287686057012197046378986248048722180093079919306125315662058290895629438767985427829790980355162853804522854494960613869765167538645624719923127052541372069255024631093663068055100579264049925388231368871107383977060590248865498902704546409806115171120555709438784189721957301548212242748685629860268468247494986146122636455769804467583612610341632602695197189514316033637331733820369170763954604394734655429769801516997967996980978751"
            },
            "rctxt": "19574881057684356733946284215946569464410211018678168661028327420122678446653210056362495902735819742274128834330867933095119512313591151219353395069123546495720010325822330866859140765940839241212947354612836044244554152389691282543839111284006009168728161183863936810142428875817934316327118674532328892591410224676539770085459540786747902789677759379901079898127879301595929571621032704093287675668250862222728331030586585586110859977896767318814398026750215625180255041545607499673023585546720788973882263863911222208020438685873501025545464213035270207099419236974668665979962146355749687924650853489277747454993",
            "z": "18569464356833363098514177097771727133940629758890641648661259687745137028161881113251218061243607037717553708179509640909238773964066423807945164288256211132195919975343578956381001087353353060599758005375631247614777454313440511375923345538396573548499287265163879524050255226779884271432737062283353279122281220812931572456820130441114446870167673796490210349453498315913599982158253821945225264065364670730546176140788405935081171854642125236557475395879246419105888077042924382595999612137336915304205628167917473420377397118829734604949103124514367857266518654728464539418834291071874052392799652266418817991437"
        }
    },
    "schemaId": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/bae5cb6c-564a-4ed4-8c0e-d5c3b0f8ae0a"
}
```

</details>



**ResourceName**

Behavior of this parameter is similar with [resourceType](adr-005-did-resolution-and-did-url-dereferencing.md#resourcetype) one. If there is no ambiguous resource, it will be fetched. Otherwise greater specifity is required.&#x20;

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c?resourceName=TAG
```

</details>

<details>

<summary>Response format</summary>

```json
{
    "type": "CL",
    "tag": "TAG",
    "value": {
        "primary": {
            "n": "92511867718854414868106363741369833735017762038454769060600859608405811709675033445666654908195955460485998711087020152978597220168927505650092431295783175164390266561239892662085428655566792056852960599485298025843840058914610127716620252006466964070280255168745873592143068949458568751438337748294055976926080232538440619420568859737673474560851456027625679328271511966332808025880807996449998057729417608399774744254122385012832309402226532031122728445959276178939234308090390331654445053482963947804769291501664200141562885660084823885847247231002821472258218384342423605116504024514572826071246440130942849549441",
            "s": "80388543865249952799447792504739237616187770512259677275061283897050980768551818104137338144380636412773836688624071360386172349725818126495487584981520630638409717065318132420766896092370913800616033623618952639023946750307405126873476182540669638841562357523429245685476919178722373320218824590869735129801004394337640642997250464303104754942997839179333543643110326022824394934965538190976474473353762308333205671176627192797138375084260446324344637548455228161138089974447059481109651156379803576163576511072261388342837813901850712083922506433336723723235701670225584863772222447543742649328218950436824219992164",
            "r": {
                "age": "676933340341980399002624386891134393471002096508227567343731826159610079436978196421307099268754545293545727546242372579987825752872485684085629459107300175443328323289748793060894500514926703654606851666031895448970879827423190730510730624784665299646624113512701254199984520803796529034094958026048762178753193812250643294518237843809104055653333871102658177900702978008644780459400512716361564897282969982554031820285585105004870317861287847206222714589633178648982299799311192432563797220854755882933052881306804544233529886513105815543097685128456041780804442879272476590077760678785460726492895806240870944398",
                "master_secret": "57770757113548032970308439965749734133430520933173186296299026579579930337912607419798836831937319372744879560676750427054135869214212225572618340088847222727882935159356459822445182287686057012197046378986248048722180093079919306125315662058290895629438767985427829790980355162853804522854494960613869765167538645624719923127052541372069255024631093663068055100579264049925388231368871107383977060590248865498902704546409806115171120555709438784189721957301548212242748685629860268468247494986146122636455769804467583612610341632602695197189514316033637331733820369170763954604394734655429769801516997967996980978751"
            },
            "rctxt": "19574881057684356733946284215946569464410211018678168661028327420122678446653210056362495902735819742274128834330867933095119512313591151219353395069123546495720010325822330866859140765940839241212947354612836044244554152389691282543839111284006009168728161183863936810142428875817934316327118674532328892591410224676539770085459540786747902789677759379901079898127879301595929571621032704093287675668250862222728331030586585586110859977896767318814398026750215625180255041545607499673023585546720788973882263863911222208020438685873501025545464213035270207099419236974668665979962146355749687924650853489277747454993",
            "z": "18569464356833363098514177097771727133940629758890641648661259687745137028161881113251218061243607037717553708179509640909238773964066423807945164288256211132195919975343578956381001087353353060599758005375631247614777454313440511375923345538396573548499287265163879524050255226779884271432737062283353279122281220812931572456820130441114446870167673796490210349453498315913599982158253821945225264065364670730546176140788405935081171854642125236557475395879246419105888077042924382595999612137336915304205628167917473420377397118829734604949103124514367857266518654728464539418834291071874052392799652266418817991437"
        }
    },
    "schemaId": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/bae5cb6c-564a-4ed4-8c0e-d5c3b0f8ae0a"
}
```

</details>

**ResourceVersion**

This parameter filters by `Version` field. We introduced it with latest network upgrade and can be optionally set to identify a version of a resource with a particular string.

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c?resourceVersion=b9029cf7-c40b-4850-b9a1-9bfad46a68d7
```

</details>

<details>

<summary>Response format</summary>

```json
{
    "type": "CL",
    "tag": "TAG",
    "value": {
        "primary": {
            "n": "92511867718854414868106363741369833735017762038454769060600859608405811709675033445666654908195955460485998711087020152978597220168927505650092431295783175164390266561239892662085428655566792056852960599485298025843840058914610127716620252006466964070280255168745873592143068949458568751438337748294055976926080232538440619420568859737673474560851456027625679328271511966332808025880807996449998057729417608399774744254122385012832309402226532031122728445959276178939234308090390331654445053482963947804769291501664200141562885660084823885847247231002821472258218384342423605116504024514572826071246440130942849549441",
            "s": "80388543865249952799447792504739237616187770512259677275061283897050980768551818104137338144380636412773836688624071360386172349725818126495487584981520630638409717065318132420766896092370913800616033623618952639023946750307405126873476182540669638841562357523429245685476919178722373320218824590869735129801004394337640642997250464303104754942997839179333543643110326022824394934965538190976474473353762308333205671176627192797138375084260446324344637548455228161138089974447059481109651156379803576163576511072261388342837813901850712083922506433336723723235701670225584863772222447543742649328218950436824219992164",
            "r": {
                "age": "676933340341980399002624386891134393471002096508227567343731826159610079436978196421307099268754545293545727546242372579987825752872485684085629459107300175443328323289748793060894500514926703654606851666031895448970879827423190730510730624784665299646624113512701254199984520803796529034094958026048762178753193812250643294518237843809104055653333871102658177900702978008644780459400512716361564897282969982554031820285585105004870317861287847206222714589633178648982299799311192432563797220854755882933052881306804544233529886513105815543097685128456041780804442879272476590077760678785460726492895806240870944398",
                "master_secret": "57770757113548032970308439965749734133430520933173186296299026579579930337912607419798836831937319372744879560676750427054135869214212225572618340088847222727882935159356459822445182287686057012197046378986248048722180093079919306125315662058290895629438767985427829790980355162853804522854494960613869765167538645624719923127052541372069255024631093663068055100579264049925388231368871107383977060590248865498902704546409806115171120555709438784189721957301548212242748685629860268468247494986146122636455769804467583612610341632602695197189514316033637331733820369170763954604394734655429769801516997967996980978751"
            },
            "rctxt": "19574881057684356733946284215946569464410211018678168661028327420122678446653210056362495902735819742274128834330867933095119512313591151219353395069123546495720010325822330866859140765940839241212947354612836044244554152389691282543839111284006009168728161183863936810142428875817934316327118674532328892591410224676539770085459540786747902789677759379901079898127879301595929571621032704093287675668250862222728331030586585586110859977896767318814398026750215625180255041545607499673023585546720788973882263863911222208020438685873501025545464213035270207099419236974668665979962146355749687924650853489277747454993",
            "z": "18569464356833363098514177097771727133940629758890641648661259687745137028161881113251218061243607037717553708179509640909238773964066423807945164288256211132195919975343578956381001087353353060599758005375631247614777454313440511375923345538396573548499287265163879524050255226779884271432737062283353279122281220812931572456820130441114446870167673796490210349453498315913599982158253821945225264065364670730546176140788405935081171854642125236557475395879246419105888077042924382595999612137336915304205628167917473420377397118829734604949103124514367857266518654728464539418834291071874052392799652266418817991437"
        }
    },
    "schemaId": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/40829caf-b415-4b1d-91a3-b56dfb6374f4"
}sons
```

</details>



**ResourceVersionTime**

**Important:** This parameter must always be accompanied by another resource query qualifier.

The main goal here is to get the nearest resource for `resourceVersionTime` value. "Nearest" means that if we are asking for time between `resource1` and `resource2` were created - `resource1` will be returned. In case if requested `resourceVersionTime` is before the first resource created - `NotFoundError` will be returned. The most useful use-case here is checking that some "Credential" (driver's license) was active at `resourceVersionTime` (was not revoked from Revocation Registry for example).

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c?resourceVersionTime=2023-02-22T06:58:18.61Z&resourceVersion=1.14.417474384596773
```

</details>

<details>

<summary>Response format</summary>

```json
{
    "name": "test - 11",
    "version": "1.14.417474384596773",
    "attrNames": [
        "name"
    ]
}
```

</details>

**Checksum**

It just checks that `checksum` is the same as resource's metadata and also can used as a sanity check. For example, if the user knows what is exact checksum then it may be checked before actual downloading.

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c?checksum=27ad51a49f079a6634b18bbc3ac08dd2d91f13fabf72ea8e5d83692fe4820058
```

</details>

<details>

<summary>Response format</summary>

```json
{
    "name": "test - 11",
    "version": "1.75.7154775070032",
    "attrNames": [
        "name"
    ]
}
```

</details>

**ResourceMetadata**

This parameter a kind of modifier which works in the same manner as [metadata](adr-005-did-resolution-and-did-url-dereferencing.md#metadata) but applies to resources. It allows to get only Metadata information about resource(s) without downloading. Also it changes the flow for `resourceType` and `resourceName` parameters and general meaning of this parameter - just filter. So, here it allows to create a chain of parameters and apply all of them to the resourceCollection and get only interested resource metadata. Possible variants:

* `resourceMetadata=true`
* `resourceMetadata=false`
* Unused

**Example**:

<details>

<summary>Request format</summary>

```
1.0/identifiers/did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c?resourceType=anonCredsSchema&resourceMetadata=true
```

</details>

<details>

<summary>Response format</summary>

```json
{
    "@context": "https://w3id.org/did-resolution/v1",
    "dereferencingMetadata": {
        "contentType": "application/did+ld+json",
        "retrieved": "2023-04-27T11:10:17Z",
        "did": {
            "didString": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
            "methodSpecificId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
            "method": "cheqd"
        }
    },
    "contentStream": {
        "created": "2023-02-21T14:28:47.406713879Z",
        "versionId": "44f49254-8106-40ee-99ad-e50ac9517346",
        "linkedResourceMetadata": [
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/bae5cb6c-564a-4ed4-8c0e-d5c3b0f8ae0a",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "bae5cb6c-564a-4ed4-8c0e-d5c3b0f8ae0a",
                "resourceName": "test11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.38.00219791272952",
                "created": "2023-02-22T08:57:23.341829704Z",
                "checksum": "93ba6f3c55ee073e6278f98e820776e73cfd9d3e32dc5882507ee8effbdbfadd",
                "previousVersionId": "40829caf-b415-4b1d-91a3-b56dfb6374f4",
                "nextVersionId": null
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/40829caf-b415-4b1d-91a3-b56dfb6374f4",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "40829caf-b415-4b1d-91a3-b56dfb6374f4",
                "resourceName": "test11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.45.245928566483904",
                "created": "2023-02-22T08:55:07.547309938Z",
                "checksum": "2a6af570635ed49a39eae9a9c60ccb40d61466839d4ab2f17432a8ac705da489",
                "previousVersionId": "547abdb3-99f8-4040-b030-3296c4668846",
                "nextVersionId": "bae5cb6c-564a-4ed4-8c0e-d5c3b0f8ae0a"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/547abdb3-99f8-4040-b030-3296c4668846",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "547abdb3-99f8-4040-b030-3296c4668846",
                "resourceName": "test11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.42.26264376650386",
                "created": "2023-02-22T08:54:14.484707292Z",
                "checksum": "4524f2193da6e5cc28d8a71f268d097891d053d4f206b045347ae117ce70d8ac",
                "previousVersionId": null,
                "nextVersionId": "40829caf-b415-4b1d-91a3-b56dfb6374f4"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/897368de-e6c5-44ac-a256-2bd02330ab5b",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "897368de-e6c5-44ac-a256-2bd02330ab5b",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.96.35140535691139",
                "created": "2023-02-22T07:35:25.81556714Z",
                "checksum": "4e64170b0b1aedd66b15c7a5644157519ed0d30dfc4df69989310dbef2f7bd60",
                "previousVersionId": "9f41aca5-bbdf-473d-88cb-4dfb78671ffe",
                "nextVersionId": null
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/9f41aca5-bbdf-473d-88cb-4dfb78671ffe",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "9f41aca5-bbdf-473d-88cb-4dfb78671ffe",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.1.6636632864708512",
                "created": "2023-02-22T07:33:32.80237235Z",
                "checksum": "8847e6b3b935d1c281fe714872edfa3755f45531217dca87f33daae53f43dc64",
                "previousVersionId": "64d5e85c-365c-4457-ab0c-c32f19449f58",
                "nextVersionId": "897368de-e6c5-44ac-a256-2bd02330ab5b"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/64d5e85c-365c-4457-ab0c-c32f19449f58",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "64d5e85c-365c-4457-ab0c-c32f19449f58",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.31.68736260709204",
                "created": "2023-02-22T07:06:23.964025795Z",
                "checksum": "c65b1d18da23ea0c918cd2a86d151946856a368adcaa04e91e7938e1f65064a6",
                "previousVersionId": "e4e32646-69a0-40ef-af9d-235cb0b6a108",
                "nextVersionId": "9f41aca5-bbdf-473d-88cb-4dfb78671ffe"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/e4e32646-69a0-40ef-af9d-235cb0b6a108",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "e4e32646-69a0-40ef-af9d-235cb0b6a108",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.56.91909743350392",
                "created": "2023-02-22T06:59:47.570665615Z",
                "checksum": "4c5e3136eb7e00f88145af3f100c252c366cbd97ca60efc99916cf4f1e66f7de",
                "previousVersionId": "31fa6841-bcda-4a3c-abd3-261e1b244d3c",
                "nextVersionId": "64d5e85c-365c-4457-ab0c-c32f19449f58"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/31fa6841-bcda-4a3c-abd3-261e1b244d3c",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "31fa6841-bcda-4a3c-abd3-261e1b244d3c",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.14.417474384596773",
                "created": "2023-02-22T06:58:06.704598725Z",
                "checksum": "4645fa956b3ec2565e323479ef9031e9778e63f4446c04a4c132c8ea866219f9",
                "previousVersionId": "02bc483a-f6e3-4a8e-918d-c903b369eb1c",
                "nextVersionId": "e4e32646-69a0-40ef-af9d-235cb0b6a108"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/02bc483a-f6e3-4a8e-918d-c903b369eb1c",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "02bc483a-f6e3-4a8e-918d-c903b369eb1c",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.75.7154775070032",
                "created": "2023-02-22T06:54:21.642136513Z",
                "checksum": "27ad51a49f079a6634b18bbc3ac08dd2d91f13fabf72ea8e5d83692fe4820058",
                "previousVersionId": "7f733048-0694-4a7c-ad67-23d691524f7b",
                "nextVersionId": "31fa6841-bcda-4a3c-abd3-261e1b244d3c"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/7f733048-0694-4a7c-ad67-23d691524f7b",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "7f733048-0694-4a7c-ad67-23d691524f7b",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.53.53714418716473",
                "created": "2023-02-22T06:52:23.286538106Z",
                "checksum": "1bfc0482df474af911ca1deb0212c7e51b327bb6da5761237e00359534a64e60",
                "previousVersionId": "53714087-e20f-456b-a68f-0a3c64909a31",
                "nextVersionId": "02bc483a-f6e3-4a8e-918d-c903b369eb1c"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/53714087-e20f-456b-a68f-0a3c64909a31",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "53714087-e20f-456b-a68f-0a3c64909a31",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.13.328827899509488",
                "created": "2023-02-22T06:47:28.120518418Z",
                "checksum": "f7769a16166c55533e3a39aa8b7a2d57c9cc91e5d10fac24a7a52f8af146f0fc",
                "previousVersionId": "9c280ef5-96d6-423c-a5eb-e397d82317ea",
                "nextVersionId": "7f733048-0694-4a7c-ad67-23d691524f7b"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/9c280ef5-96d6-423c-a5eb-e397d82317ea",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "9c280ef5-96d6-423c-a5eb-e397d82317ea",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.60.80326856085772",
                "created": "2023-02-22T06:45:29.298572213Z",
                "checksum": "7e03f7f4efc8ff82b19957e81d377e52f4c4f23e860bd90f1a51a51fd6b65e2b",
                "previousVersionId": "9b6d5a3f-2dd4-4d14-b940-96967239f933",
                "nextVersionId": "53714087-e20f-456b-a68f-0a3c64909a31"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/9b6d5a3f-2dd4-4d14-b940-96967239f933",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "9b6d5a3f-2dd4-4d14-b940-96967239f933",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.66.73560282313116",
                "created": "2023-02-22T06:42:49.403227253Z",
                "checksum": "d81a6caf78ed4e7ee983acd8b2db2ba97f84a95ec35c3a9f97a5f56d0f4e7b2e",
                "previousVersionId": "12e7385c-9815-4094-a8de-50b0f8713508",
                "nextVersionId": "9c280ef5-96d6-423c-a5eb-e397d82317ea"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/12e7385c-9815-4094-a8de-50b0f8713508",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "12e7385c-9815-4094-a8de-50b0f8713508",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.52.437822168723436",
                "created": "2023-02-22T06:31:46.632577547Z",
                "checksum": "e94bcb65a758753c1b66332c5d1878c2b87b959c3af7f94b5a5d81ed241292fa",
                "previousVersionId": "eee49898-c80d-4862-a7db-73c7aa9a6c88",
                "nextVersionId": "9b6d5a3f-2dd4-4d14-b940-96967239f933"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/eee49898-c80d-4862-a7db-73c7aa9a6c88",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "eee49898-c80d-4862-a7db-73c7aa9a6c88",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.14.189123052353757",
                "created": "2023-02-22T06:27:44.050370138Z",
                "checksum": "7b4f4a81fc39a920bc1a4e0bff0e9ff1c91057702dd920dd1936090db099f76d",
                "previousVersionId": "319f8889-8bbe-4fc6-b5a4-638ba58390a2",
                "nextVersionId": "12e7385c-9815-4094-a8de-50b0f8713508"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/319f8889-8bbe-4fc6-b5a4-638ba58390a2",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "319f8889-8bbe-4fc6-b5a4-638ba58390a2",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.60.10585488950264",
                "created": "2023-02-22T06:24:34.178043845Z",
                "checksum": "5c259086d4676d6fe06e447776c8e55f04e6e01d98489b2a85d3a35d8f50e732",
                "previousVersionId": "57a091e6-aafb-4c88-81fe-508a51c92491",
                "nextVersionId": "eee49898-c80d-4862-a7db-73c7aa9a6c88"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/57a091e6-aafb-4c88-81fe-508a51c92491",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "57a091e6-aafb-4c88-81fe-508a51c92491",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.0.8322705530387609",
                "created": "2023-02-22T06:21:06.651892018Z",
                "checksum": "9737d5e62aa4ea8903e11d9322782b5c5349a37ffecca17b8b0165941ca084aa",
                "previousVersionId": "ffd001c2-1f80-4cd8-84b2-945fba309457",
                "nextVersionId": "319f8889-8bbe-4fc6-b5a4-638ba58390a2"
            },
            {
                "resourceURI": "did:cheqd:testnet:d8ac0372-0d4b-413e-8ef5-8e8f07822b2c/resources/ffd001c2-1f80-4cd8-84b2-945fba309457",
                "resourceCollectionId": "d8ac0372-0d4b-413e-8ef5-8e8f07822b2c",
                "resourceId": "ffd001c2-1f80-4cd8-84b2-945fba309457",
                "resourceName": "test - 11",
                "resourceType": "anonCredsSchema",
                "mediaType": "application/json",
                "resourceVersion": "1.40.23710152624007",
                "created": "2023-02-21T14:29:04.895533682Z",
                "checksum": "2f95be447ce790c337767af65cc65e7312244bfd7f2a3ee0886e4c7956be3cee",
                "previousVersionId": null,
                "nextVersionId": "57a091e6-aafb-4c88-81fe-508a51c92491"
            }
        ]
    },
    "contentMetadata": {}
}
```

</details>

### HTTP headers and associated rules

The cheqd DID Resolver complies with the rules and algorithm defined in [Decentralized Identifier Resolution (DID Resolution) v0.3](https://w3c-ccg.github.io/did-resolution). This section clarifies and expands some descriptions specific to cheqd.

The [DID Resolution specification HTTP(S) bindings](https://w3c-ccg.github.io/did-resolution/#bindings-https) section states that:

> If the output of the DID URL dereferencing function contains the `didDocumentStream`:
>
> * If the value of the `Accept` HTTP header is absent or `application/did+ld+json` (or other media type of a conformant representation of a DID document):
>   * The HTTP response status code MUST be `200`.
>   * The HTTP response MUST contain a `Content-Type` header. The value of this header MUST be `application/did+ld+json` (or other media type of a conformant representation of a DID document).
> * The HTTP response body MUST contain the `didDocumentStream`, in the representation corresponding to the `Accept` HTTP header.

Since the cheqd DID Resolver APIs are REST APIs, the default `Content-Type: application/did+ld+json` encoding is used if the `Accept` header is not explicitly set since it matches the `Accept: */*` header that most client applications send.

#### `Accept` header is `application/did+ld+json` OR blank OR `*/*`

* **Response** HTTP headers
  * Status code `200 OK`
  * `Content-Type: application/did+ld+json`
* **Response** HTTP body
  * `didDocument` / `contentStream` contains `@context` section;
  * `didResolutionMetadata` / `dereferencingMetadata` `contentType` field is `application/did+ld+json`

#### `Accept` request HTTP header contains `application/ld+json;profile="https://w3id.org/did-resolution"`

* **Response** HTTP headers
  * Status code `200 OK`
  * `Content-Type: application/ld+json;profile="https://w3id.org/did-resolution`
* **Response** HTTP body
  * `didDocument` / `contentStream` contains `@context` section;
  * `didResolutionMetadata` / `dereferencingMetadata` `contentType` field is `application/ld+json;profile="https://w3id.org/did-resolution`

#### `Accept` request HTTP header contains `application/did+json`

* **Response** HTTP headers
  * Status code `200 OK`
  * `Content-Type: application/did+json`
* **Response HTTP body**
  * `didDocument` / `contentStream` DOES NOT contain `@context` section;
  * `didResolutionMetadata` / `dereferencingMetadata` `ContentType` field is `application/did+json`

### Error handling

The DID Resolution specification [defines an algorithm for how invalid DID URL Resolution/Dereferencing errors](https://w3c-ccg.github.io/did-resolution/#resolving-algorithm) should be handled. The cheqd DID Resolver aims to implement all of these scenarios, with the [correct HTTP Response status codes based on the specific error](https://w3c-ccg.github.io/did-resolution/#bindings) encountered.

### DID Resolution errors

The DID resolution output should always conform to the following format: `( didResolutionMetadata, didDocument, didDocumentMetadata )`

If the resolution is unsuccessful, the DID resolver should return the following result:

* `didResolutionMetadata` contains `"error" : "<Error message>"`
* `didDocument`: null
* `didDocumentMetadata`: `[]`

### DID URL Dereferencing errors

The DID dereferencing output should always conform to the following format: `( dereferencingMetadata, contentStream, contentMetadata )`

* `dereferencingMetadata` contains `"error" : "<Error message>"`
* `contentStream`: null
* `contentMetadata`: `[]`
