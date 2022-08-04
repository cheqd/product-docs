# ADR 001: DID Resolver

## Status

| Category | Status |
| :--- | :--- |
| **Authors** | Alex Tweeddale, Renata Toktar, Ankur Banerjee |
| **ADR Stage** | DRAFT |
| **Implementation Status** | In Progress |
| **Start Date** | 2022-02-22 |
| **Last Updated** | 2022-08-04 |

## Summary

The [`did:cheqd` method ADR](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method) defines how DIDs are created and read from ledger. According to the [W3C DID Core specification](https://w3c.github.io/did-core/), DID methods are expected to provide [standards-compliant methods of DID and DID Document ("DIDDoc") production](https://w3c.github.io/did-core/#production-and-consumption).

The [cheqd DID Resolver](https://github.com/cheqd/did-resolver) is designed to implement the [W3C DID *Resolution* specification](https://w3c-ccg.github.io/did-resolution/) for [`did:cheqd`](https://docs.cheqd.io/node/architecture/adr-list/adr-002-cheqd-did-method) method.

## Context

The DID Resolution specification prescribes [a defined algorithm with standardised behaviour for expected and unexpected inputs](https://w3c-ccg.github.io/did-resolution/#resolving-algorithm) that a conforming DID method must be able to produce.

All conforming DID resolvers implement `resolve` and `resolveRepresentation` abstract functions, as defined in the [DID Resolution specification](https://w3c-ccg.github.io/did-resolution/#resolving).

### Resolve function

The `resolve` function is intended to fetch the *abstract* form of the DID Document, as stored on the ledger. This abstract/raw form [may *not* necessarily be in JSON/JSON-LD format](https://www.w3.org/TR/did-core/#did-resolution) as the underlying data persistence layer where the DIDDoc is stored for any particular method might use different serialisation/storage formats.

```js
resolve(did, resolutionOptions) → 
« didResolutionMetadata, didDocument, didDocumentMetadata »
```

Since [cheqd uses the Cosmos SDK blockchain framework](https://blog.cheqd.io/why-cheqd-has-joined-the-cosmos-4db8845722c5), the underlying data storage and retrieval ("resolve") mechanisms used rely on those offered by the [Cosmos SDK framework](https://docs.cosmos.network/master/intro/overview.html). Cosmos SDK [uses Protobuf (Protocol Buffers) encoding for its wire protocol](https://docs.cosmos.network/master/core/encoding.html).

Cosmos SDK framework typically provides [gRPC/gRPC-Web, JSON-RPC, and REST API endpoints for on-ledger modules](https://docs.cosmos.network/master/core/grpc_rest.html) and functionality.

For example, `did:cheqd:testnet:DAzMQo4MDMxCjgwM` can be fetched using the native Cosmos SDK REST API endpoint (or equivalent endpoints). This provides responses that would meet the abstract definition of a `resolve` function as defined in the DID Core specification.

In case of the cheqd network *testnet*, an instance of this `resolve` endpoint through the Cosmos SDK REST API would be [api.cheqd.network/cheqd/v1/did/did:cheqd:testnet:DAzMQo4MDMxCjgwM](https://api.cheqd.network/cheqd/v1/did/did:cheqd:testnet:DAzMQo4MDMxCjgwM) which returns the following response:

```jsonc
{
  "did": {
    "context": [
    ],
    "id": "did:cheqd:testnet:DAzMQo4MDMxCjgwM",
    "controller": [
    ],
    "verification_method": [
      {
        "id": "did:cheqd:testnet:DAzMQo4MDMxCjgwM#key1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:DAzMQo4MDMxCjgwM",
        "public_key_jwk": [
        ],
        "public_key_multibase": "z6jVkB274neVf7iJETpMECwznBF8wDe8tpvF4BZLRZgMU"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:DAzMQo4MDMxCjgwM#key1"
    ],
    "assertion_method": [
    ],
    "capability_invocation": [
    ],
    "capability_delegation": [
    ],
    "key_agreement": [
    ],
    "service": [
    ],
    "also_known_as": [
    ]
  },
  "metadata": {
    "created": "2022-07-19T08:29:07Z",
    "updated": "",
    "deactivated": false,
    "version_id": "57543FA1D9C56033BABBFA3A438E0A149E01BBB89E6D666ACE1243455AA6F2BC",
    "resources": [
      "44547089-170b-4f5a-bcbc-06e46e0089e4"
    ]
  }
}
```

As you can see in the response body above, this is the raw Protobuf fetched from the cheqd testnet ledger, marshalled into a JSON form. Crucially, this form has certain deviations from the JSON/JSON-LD production expected in DID Core specification:

1. JSON key names that correlate to DID Core properties are listed in [`snake_case`](https://en.wikipedia.org/wiki/Snake_case), rather than [`camelCase`](https://en.wikipedia.org/wiki/Camel_case) as required. This is because Protobuf standard linting rules require these properties to be defined in `snake_case`.
2. DID Core properties with empty values are still shown in this JSON, whereas the requirement is to drop them from standards-compliant DIDDoc representations.

### Resolve Representation function

The `resolveRepresentation` abstract function, as defined in DID Core specification, is intended to address concerns similar to the ones highlighted above to product a [standards-compliant JSON/JSON-LD representation](https://www.w3.org/TR/did-core/#did-resolution) of a DIDDoc.

```js
resolveRepresentation(did, resolutionOptions) → 
« didResolutionMetadata, didDocumentStream, didDocumentMetadata »
```

For example, a `resolveRepresentation` function *could* derive a valid standards-compliant representation of `did:cheqd:testnet:DAzMQo4MDMxCjgwM` from the above `resolve` function. The response would be similar to the one below containing Resolution Metadata, DIDDoc, and DIDDoc Metadata:

```jsonc
{
  "didResolutionMetadata": {
    "contentType": "application/did+ld+json",
    "retrieved": "2022-08-03T09:52:49Z",
    "did": {
      "didString": "did:cheqd:testnet:DAzMQo4MDMxCjgwM",
      "methodSpecificId": "DAzMQo4MDMxCjgwM",
      "method": "cheqd"
    }
  },
  "didDocument": {
    "id": "did:cheqd:testnet:DAzMQo4MDMxCjgwM",
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:DAzMQo4MDMxCjgwM#key1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:DAzMQo4MDMxCjgwM",
        "publicKeyMultibase": "z6jVkB274neVf7iJETpMECwznBF8wDe8tpvF4BZLRZgMU"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:DAzMQo4MDMxCjgwM#key1"
    ]
  },
  "didDocumentMetadata": {
    "created": "2022-07-19T08:29:07Z",
    "versionId": "57543FA1D9C56033BABBFA3A438E0A149E01BBB89E6D666ACE1243455AA6F2BC",
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:DAzMQo4MDMxCjgwM/resources/44547089-170b-4f5a-bcbc-06e46e0089e4",
        "resourceCollectionId": "DAzMQo4MDMxCjgwM",
        "resourceId": "44547089-170b-4f5a-bcbc-06e46e0089e4",
        "resourceName": "DemoResource",
        "resourceType": "CL-Schema",
        "mediaType": "application/json",
        "created": "2022-07-19T08:40:00Z",
        "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
        "previousVersionId": null, // null if no previous version, otherwise, resourceId of previous version
        "nextVersionId": null, // null if no new version, otherwise, resourceId of new version
      }
    ]
  }
}
```

## Architecture of DID Resolver for cheqd

As described above, the abstract `resolve` function is already available for the cheqd ledger via the default Cosmos SDK gRPC/REST API endpoints. Our primary objective with building a DID Resolver for cheqd was to design this `resolveRepresentation` piece as a standalone component that was *not* packaged within the [cheqd-node ledger code](https://github.com/cheqd/cheqd-node).

This objective has certain advantages:

- Updates to DID Resolver code can be carried out and released independently of cheqd-node releases. As a consequence, there's no need to go through an on-ledger governance vote, and voting period to make a change to `resolveRepresentation`.
- A separate web service module would allow for flexibility in how to handle complex scenarios on DID URL Dereferencing, error code handling for DID URL requests, and safely handling content transport for various media types.
- Making the DID Resolver a standalone, non-ledger module allows for an operator of this web service to *independently* scale their service horizontally and vertically.

We explored two architectural patterns for how a DID Resolver could be implemented for the cheqd ledger. The objective here was to explore and provide DID resolution operators multiple approaches for running resolution service, each with their own pros and cons (which are discussed below).

1. **"Full" cheqd DID Resolver**
   1. Since the [cheqd-node ledger](https://github.com/cheqd/cheqd-node) / Cosmos SDK is written in Golang, this resolver would consist of Golang libraries imported from the existing ledger code. This promotes code reuse.
   2. Data would be fetched from the ledger using the gRPC endpoint on a node, which allows it (by default) to take place over an encrypted channel [since gRPC uses HTTP/2](https://grpc.io/).
   3. Data retrieved would be in the native Protobuf representation as stored on ledger, thus allowing data integrity computations to be made.
2. **"Light" cheqd DID Resolver**
   1. Universal Resolver drivers are designed to be run as Docker containers. A limitation of this approach is that the computation footprint of a compute resource can be quite high, e.g., a Docker container may be 100 MB+ in size and [suffer from slow startup times in a "cold-start" scenario](https://mikhail.io/serverless/coldstarts/aws/).
   2. Thus, our "Light" DID Resolver idea was to explore using [Cloudflare Workers](https://workers.cloudflare.com/), a lightweight serverless compute platform. As a comparison, [Cloudflare Workers are limited to 1 MB in size](https://developers.cloudflare.com/workers/platform/limits/) and [have extremely low cold-start times](https://blog.cloudflare.com/eliminating-cold-starts-with-cloudflare-workers/). (We use Cloudflare Workers in [our Cosmos SDK Custom Data API](https://github.com/cheqd/data-api), for example.)
   3. Cloudflare Workers can also be deployed outside the Cloudflare service in a Docker container using [Miniflare](https://miniflare.dev/). This could be used to provide a Docker container deployment option for the Universal Resolver `did:cheqd` driver.
   4. However, [a limitation of Cloudflare Workers is they do not allow a gRPC *request* to be made](https://community.cloudflare.com/t/can-i-make-a-grpc-request-from-a-worker/157450/4) to an external endpoint. This would force the "Light" cheqd Resolver to use the gRPC-Web / REST endpoint `resolve` implementation to fetch data from the ledger. This could be considered a higher risk profile in terms of data integrity by resolver operators / client applications.

Both of the architectural patterns above are designed so that a **[Universal Resolver driver for `did:cheqd`](https://github.com/decentralized-identity/universal-resolver)** could be created. The Universal Resolver project aims to provide a common REST API definition for DID Resolution where each DID method can provide a Docker container that with an easy-to-deploy mechanism for the specific DID method.

### *Full* cheqd DID Resolver

The *Full cheqd DID Resolver* is able to use `github.com/cheqd/cheqd-node` as a Golang module for send `resolve` requests to a cheqd node instance to fetch DIDDoc / Resources from the ledger.

Since the *Full cheqd DID Resolver* is wrapped for usage as a Docker container image using the Universal Resolver specification, the end-to-end sequence diagram for our DID Resolver would look like below:

![*Full cheqd DID Resolver* sequence diagram](../../.gitbook/assets/cheqd-full-did-resolver-sequence-diagram.png)
*Figure 1: "Full" cheqd DID Resolver sequence diagram ([editable version](https://swimlanes.io/u/CE_Rjphs9?rev=7))*

The *Full cheqd DID Resolver* is designed to handle requests concurrently, while reducing the risk of large quantities of threads and requests blocking the efficiency of the on-ledger services.

#### Fetching Protobuf from ledger and converting it to JSON

Since Cosmos SDK SDK encodes data in Protobuf, the DID Resolver "[marshalls](https://en.wikipedia.org/wiki/Marshalling_(computer_science))" them to JSON. The software class diagram below describes how these components/methods are tied together:

![*Full cheqd DID Resolver* class diagram](../../.gitbook/assets/cheqd-full-did-resolver-class-diagram.png)
*Figure 2: "Full" cheqd DID Resolver class diagram*

Marshalling/unmarshalling requests back-and-forth between Protobuf and JSON is carried out by services in the "Full" DID Resolver

![*Full cheqd DID Resolver* Protobuf <-> JSON marshalling](../../.gitbook/assets/cheqd-did-resolver-protobuf-json-marshalling.png)
*Figure 3: "Full" cheqd DID Resolver Protobuf <-> JSON marshalling ([editable version](https://swimlanes.io/u/2W1PQKx_s?rev=4))*

### Resolution rules

The cheqd DID Resolver complies with the rules and algorithm defined in [Decentralized Identifier Resolution (DID Resolution) v0.2](https://w3c-ccg.github.io/did-resolution). This section clarifies and expands some descriptions specific to cheqd.

The [DID Resolution specification HTTP(S) bindings](https://w3c-ccg.github.io/did-resolution/#bindings-https) section states that:

> If the output of the DID URL dereferencing function contains the `didDocumentStream`:
>
> - If the value of the `Accept` HTTP header is absent or `application/did+ld+json` (or other media type of a conformant representation of a DID document):
>   - The HTTP response status code MUST be `200`.
>   - The HTTP response MUST contain a `Content-Type` header. The value of this header MUST be `application/did+ld+json` (or other media type of a conformant representation of a DID document).
> - The HTTP response body MUST contain the `didDocumentStream`, in the representation corresponding to the `Accept` HTTP header.

Since the cheqd DID Resolver APIs are REST APIs, the default `Content-Type: application/did+ld+json` encoding is used if the `Accept` header is not explicitly set since it matches the `Accept: */*` header that most client applications send.

#### `Accept` header is `application/did+ld+json` OR blank OR `*/*`

- **Response HTTP header**: `Content-Type: application/did+ld+json`
- **Response HTTP body**
  - `didDocument` / `contentStream` contains `@context` section;
  - `didResolutionMetadata` / `dereferencingMetadata` `contentType` field is `application/did+ld+json`

#### `Accept` request HTTP header contains `application/ld+json;profile="https://w3id.org/did-resolution"`

- **Response HTTP header**: `Content-Type: application/ld+json;profile="https://w3id.org/did-resolution`
- **Response HTTP body**
  - `didDocument` / `contentStream` contains `@context` section;
  - `didResolutionMetadata` / `dereferencingMetadata` `contentType` field is `application/ld+json;profile="https://w3id.org/did-resolution`

#### `Accept` request HTTP header contains `application/did+json`

- **Response HTTP header**: `Content-Type: application/did+json`
- **Response HTTP body**
  - `didDocument` / `contentStream` DOES NOT contain `@context` section;
  - `didResolutionMetadata` / `dereferencingMetadata` `ContentType` field is `application/did+json`

#### DID URL Dereferencing result is the *actual* Resource on-ledger

If the result of DID URL Dereferencing is *not* `didDocumentStream` but a *`Resource`*, then cheqd DID Resolver sets the `Content-Type` header to the `mediaType` defined in the Resource metadata.

- **Request** HTTP headers
  - `Accept` should allow `*/*` *or* match the `mediaType` of the Resource
  - [`Accept-Encoding`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Encoding) may be allowed compression methods (e.g., `gzip`, `compress`, etc.)
- **Response** HTTP headers
  - `Content-Type` should be set to `mediaType>` of the Resource
  - `Content-Encoding` should be set to a valid content compression method in `Accept-Encoding` and response compressed accordingly
  - [`Content-Length`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Length) should be set to the Resource size, in decimal bytes
- **Response HTTP body**
  - Resource, encoded correctly according to the `mediaType`

#### `Accept` request HTTP header does not contain valid `Content-Type`

- **Response HTTP header**: `Content-Type: application/json`
- **Response HTTP body**
  - `didDocument` / `contentStream` is `[]`;
  - `didResolutionMetadata` / `dereferencingMetadata` contains a property error with value `representationNotSupported`
  - `didResolutionMetadata` / `dereferencingMetadata` `ContentType` field is `application/json`

### Errors

The DID resolution output should always conform to the following format: `( didResolutionMetadata, didDocument, didDocumentMetadata )`
If the resolution is unsuccessful, the DID resolver should return the following result:

- didResolutionMetadata contains `"error" : "<Error message>"`
- didDocument: null
- didDocumentMetadata: `[]`

The DID dereferencing output should always conform to the following format: `( dereferencingMetadata, contentStream, contentMetadata )`

- dereferencingMetadata contains `"error" : "<Error message>"`
- contentStream: null
- contentMetadata: `[]`

#### Error list

- **invalidDid** - DID does not conform to the rule of the [DID Syntax](https://www.w3.org/TR/did-core/#did-syntax)
  - response status code 400
- **invalidDidUrl** - DID URL does not conform to the rule of the [DID URL Syntax](https://www.w3.org/TR/did-core/#did-url-syntax)
  - response status code 400
- **notFound** - DID Doc, Resource or DID Doc fragment does not exist in a ledger
  - response status code 404
- **representationNotSupported** - Resolution `Accept` option or dereferencing for this DID URL is not supported
  - response status code 406
- **methodNotSupported** - DID method of the input DID is not supported
  - response status code 406
- **internalError** - an unexpected error occurs during DID Resolution or DID URL dereferencing
  - response status code 500
- `didDocumentMetadata` property deactivated with value `true`
  - response status code 410

## Resources

cheqd has implemented resources on ledger within its update 0.6.x. Resources will be shown within DIDDoc metadata.

### Resource resolution

The following syntax can be used in DID Resolution to fetch resources or previews of resources:

- `/1.0/identifiers/<did>/resources/all`
  - Return everything within the Collection of Resources in a preview format, without presenting the actual data within the resources.
- `/1.0/identifiers/<did>/resources/`
  - Status code 301
  - Redirect to `/resources/all`
- `/1.0/identifiers/<did>/resources`
  - Error -> invalidDidUrl
- `/1.0/identifiers/<did>/resources/<resource_id>`
  - Return resource data for specific resource
  - ContentType = MediaType
- `/1.0/identifiers/<did>/resources/<resource_id>/metadata`
  - Return resource metadata (without data)

## 3. Universal Resolver Driver

<!-- markdown-link-check-disable-next-line -->
The Decentralised Identity Foundation (DIF) has a publicly accessible Universal Resolver, which can be found at <https://dev.uniresolver.io>

The Universal Resolver is a landing page and search engine for multiple DID Resolvers, each with their own Universal Resolver Driver. Universal Resolver Drivers are all packaged up as [Docker Containers](https://www.docker.com/resources/what-container/), which makes it much easier for clients to import and directly support multiple drivers.

The flow for resolving a DID using this implementation is as follows:

<!-- markdown-link-check-disable-next-line -->
- A client sends a request to <https://dev.uniresolver.io>.
- The Universal Resolver on DIF servers uses the cheqd Universal Resolver Driver to redirect client request to the full cheqd DID Resolver.
- cheqd full DID Resolver gets DID Doc in protobuf format from the ledger through the Cosmos SDK gRPC API.
- cheqd full DID Resolver generates a response for the client's request based on received DID Doc.
- cheqd full DID Resolver sends a response to the client through the cheqd Universal Resolver Driver and Universal Resolver itself.

### 1. Client implements the **full cheqd DID Resolver** as a Library into their own application

In this case, the Go module can be imported simply into a client's own libraries by using the following:

```ignorelang
import (
     "github.com/cheqd/cheqd-did-resolver/services"
)
```

The flow for resolving a DID using this implementation is as follows:

- A client imports the library as a go module into their own application
- Using the library, the client makes a request directly to the cheqd node, or alternatively, to their own node on the cheqd network
- Requests will query the Cosmos gRPC endpoint to return a DID Document and associated metadata in protobuf format
- The client application will unmarshal the protobuf into a JSON format which is compliant with [W3C DID Core](https://www.w3.org/TR/did-core/).

The flow for DID resolution is illustrated in the third "Client <-> Ledger" section from [figure 1](../assets/adr-001-did-resolver/universal-resolver-sequence-diagram.puml).

## References

- [W3C Decentralized Identifiers (DIDs)](https://www.w3.org/TR/did-core/) recommendation
- [W3C Decentralized Identifier Resolution (DID Resolution)](https://w3c-ccg.github.io/did-resolution/) specification
- [DID Core Specification Test Suite](https://w3c.github.io/did-test-suite/)

## Unresolved questions

- Should the web service find another node for the request if it is not possible to connect to the cheqd node? Should the web service need to have a pool of trusted nodes for routing requests?
- How should synchronous response to client requests be handled, if needed in the future?
