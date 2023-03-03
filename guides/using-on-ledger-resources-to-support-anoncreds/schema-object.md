---
description: cheqd support for Ledger-Agnostic AnonCreds schemas
---

# Schema Object

## cheqd AnonCreds Object method for Schemas

Schemas are used to list a set of attributes. Issuers of Verifiable Credentials may reference schemas within Credentials they issue in order to provide a layer of semantic interoperability with other issuers utilising the same schema.

In the [AnonCreds Specification](https://hyperledger.github.io/anoncreds-spec/), schemas are written directly to a [Verifiable Data Registry](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-decentralised-identifier-did/what-is-a-verifiable-data-registry), rather than using a centralized service such as [schema.org](https://schema.org/). Schemas are also referenced within Credential Definitions, which are used to link the schema, issuer and holder together ([detailed further here](creddef-object.md)).

This documentation will guide an implementor of AnonCreds on cheqd on how the cheqd AnonCreds Object Method defines the structure of [**cheqd schema IDs**](schema-object.md#cheqd-schema-id), the [**schema request format**](schema-object.md#cheqd-schema-request-format) and the [**schema response format**](schema-object.md#cheqd-schema-response-format).

### Hyperledger AnonCreds Schema Object

If you are not familiar with the latest Ledger-Agnostic AnonCreds Schema structure, click the collapsible tile below to learn about the new format.

<details>

<summary>Ledger-Agnostic AnonCreds Schema Object Content</summary>

Each specific AnonCreds identifier must be defined within an AnonCreds Object Method in the [AnonCreds Object Method Registry](https://hyperledger.github.io/anoncreds-spec/).

This means that an AnonCreds Schema Object ID does not need to be formatted in any particular syntax in the latest version of the [AnonCreds Specification](https://hyperledger.github.io/anoncreds-spec/).

In the [Hyperledger AnonCreds specification](https://hyperledger.github.io/anoncreds-spec/), the Schema Object Content which is required to be written to the Verifiable Data Registry, contains the following information:

* `issuerId` - the [Issuer Identifier](https://hyperledger.github.io/anoncreds-spec/#term:issuer-identifier) of the schema. MUST adhere to [Issuer Identifiers](https://hyperledger.github.io/anoncreds-spec/#issuer-identifiers) rules.
* `name` (string) - the name of the schema
* `version` (string) - the schema version
* `attrNames` (str\[]) - an array of strings with each string being the name of an attribute of the schema

For example:

```json
{
  "issuerId": "https://example.org/issuers/74acabe2-0edc-415e-ad3d-c259bac04c15",
  "name": "Example schema",
  "version": "0.0.1",
  "attrNames": ["name", "age", "vmax"]
}
```

</details>

### cheqd Schema ID

cheqd uses [DID-Linked Resources](../did-linked-resources/) to identify individual resources, associated with a DID, using fully resolvable DID URLs.

cheqd resources implementation uses the following path-based syntax:

`did:cheqd:mainnet:<SchemaIssuerId>/resources/<SchemaId>`

The cheqd AnonCreds object method uses a UUID to identify the Schema Object Content.

For example, the following DID URL is cheqd's representation of a `schemaId`:

`did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497`

### Understanding Request vs Response formats

It is important to differentiate between the **Request format** for creating an AnonCreds object on cheqd, and the **Response format**, for how an AnonCreds objectshould be compiled by SDKs and the [cheqd DID Resolver](../../architecture/adr-list/adr-003-did-resolver.md).

The request format _**may**_ be specific to each AnonCreds Object Method. However, the response format _**should**_ be standardised to enable any AnonCreds supported application to understand the object, without needing custom or method-specific logic.

### cheqd Schema Request format

The cheqd schema request format comprises of:

1. A Resource file for the Schema object content (e.g. `degreeSchema.json`); and
2. A Payload file (including the signing keys and additional inputs to create a DID-Linked Resource).

Both of these inputs are required to provide the ledger enough information to:

1. Populate a [cheqd DID-Linked Resource](../did-linked-resources/); and
2. Compile a standardised AnonCreds schema object in the [Response format](schema-object.md#cheqd-schema-response-format).

#### cheqd Schema Resource file

Before creating any on-ledger transaction, it is important to assemble the required Schema Object Content and save it as a file locally.

In the example below, the content should be saved as a JSON file, for example: `degreeSchema.json` with the following content:

```json
{
  "name": "degreeSchema",
  "version": "1.5.7",
  "attrNames": ["name", "age", "degree", "grade"]
 }
```

This Schema Resource file inputs should be replicated where possible within the Payload file, to populate a [DID-Linked resource](../did-linked-resources/) stored on cheqd, with the following mapping:

| Resource file field | Payload file field |
| ------------------- | ------------------ |
| "name"              | "name"             |
| "version"           | "version"          |

#### Schema Payload File

The **Payload file** utilises the inputs from the **Resource file** where possible, mapping common fields across. The **Payload file** may also require additional inputs to be provided by the creator to create a DID-Linked Resource for inputs not provided in the **Resource file**.&#x20;

Below is an example of a Payload file:

```json
{
  "Payload": {
    "collectionId": "7BPMqYgYLQni258J8JPS8K",
    "id": "6259d357-eeb1-4b98-8bee-12a8390d3497",
    "name": "degreeSchema",
    "version": "1.5.7",
    "resourceType": "anonCredsSchema", // this is added as an additional input
    "alsoKnownAs": []
  },
  "SignInputs": [
    {
      "verificationMethodID": "did:cheqd:testnet:7BPMqYgYLQni258J8JPS8K#key1",
      "privKey": "y4B5qis9BXUq/mODsrWtS3q5ejOk/okSIXlX1/a9HvuG3PgYmekfQmq3QhJ4JSzN/rkiGCQDNKoTXMmxuXDHbg=="
    }
  ]
}
```

When passing the Payload file to the ledger, additional inputs may be required within the Payload file to populate the [DID-Linked Resource](../did-linked-resources/). In this instance, the only additional information required is:

| Additional parameter | Expected input    | Rationale                                                                                                                                                                                                                   |
| -------------------- | ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "resourceType"       | "anonCredsSchema" | The Payload file drawing inputs from the Resource file does not provide the ledger the requisite amount of information to create a full DID-Linked Resource. resourceType must be provided as an additional input parameter |

#### Publishing resource using CLI

For example, the full request format using a CLI should be structured as follows:

```bash
cheqd-noded tx resource create [payload.json] [degreeSchema.json] \

  --chain-id cheqd \
  --keyring-backend test \
  --output json \
  --fees 10000000000ncheq \
  --gas auto \
  --gas-adjustment 1.8 \
  --from base_account \
```

### cheqd resource Metadata

Once you have created your resource on cheqd, the following metadata will be generated in the DID Document Metadata associated with `did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K`

```json
"linkedResourceMetadata": [
  {    
    "resourceURI": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
    "resourceCollectionId": "7BPMqYgYLQni258J8JPS8K",
    "resourceId": "6259d357-eeb1-4b98-8bee-12a8390d3497",
    "resourceName": "degreeSchema",
    "resourceType": "anonCredsSchema",
    "resourceVersion": "1.5.7",
    "mediaType": "application/json",
    "created": "2022-07-19T08:40:00Z",
    "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
    "previousVersionId": null,
    "nextVersionId": null
    }
]
```

### cheqd Schema Response format

Using the [cheqd Schema Request Format](schema-object.md#cheqd-schema-request-format) and associated [resource metadata](schema-object.md#cheqd-resource-metadata), the ledger has enough information to compile the following data structure as a response format.

This can either be compiled by the associated SDK handling cheqd AnonCreds, or it can be assembled by the cheqd DID resolver.

```json
{
  "issuerId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K",
  "name": "degreeSchema",
  "version": "1.5.7",
  "attrNames": ["name", "age", "degree", "grade"]
}
```

#### Compiling Response format in cheqd DID Resolver

The cheqd DID resolver will use the following logic to compile the standardised response format:

{% hint style="info" %}
_If "**resourceType=anonCredsSchema**" then **append "issuerId"** to the beginning of the Response Format for the resource presented_
{% endhint %}

### Create schema transaction

To create a schema on cheqd, you should follow the [tutorials for creating a DID-Linked Resource here](../../tutorials/on-ledger-resources/), and pass the relevant JSON file for the object in the transaction.

### Fetching a cheqd Resource

Existing DID Resolvers will be able to query for any AnonCreds Object Content using the following parameters:

| Parameter                 | Type                                                          | Example                                                                                                                                                                                                                                                                                                                                                            |
| ------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `"resourceId"`            | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                             |
| `"resourceName"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw                                                                                                                                                                                                                                                                                      |
| `"resourceType"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020                                                                                                                                                                                                                                                         |
| `"resourceVersionId"`     | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceVersionId=1.3.1                                                                                                                                                                                                                                                             |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&versionTime=2015-03-11T05:30:02Z                                                                                                                                                                                                                       |
| `"versionId"`             | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?versionId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                              |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&versionTime=2018-07-19T08:40:00Z                                                                                                                                                                                                                       |
| `"linkedResource"`        | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?linkedResource=true // _note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document._                                                                                                                                                                 |
| `"resourceMetadata"`      | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&versionTime=2018-07-19T08:40:00Z\&resourceMetadata=true or, did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceMetadata=true // note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document. |
| "`latestResourceVersion`" | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&latestResourceVersion=true                                                                                                                                                                                                                             |
| "`allResourceVersions`"   | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&allResourceVersions=true                                                                                                                                                                                                                               |

### Legacy AnonCreds Schema Object

<details>

<summary>Legacy AnonCreds Schema Object</summary>

Prior to the AnonCreds specification being updated, the `schema_id` was defined as a **composite** of the following set of elements:

* `publisher DID`: is a [string](https://infra.spec.whatwg.org/#string). The DID of the Schema Publisher.
* `object type`: An integer denoting the type of object. `2` is used for Schemas.
* `name`: is a [string](https://infra.spec.whatwg.org/#string), the name of the schema
* `version`: is a [string](https://infra.spec.whatwg.org/#string), the version of the schema in [semver](https://semver.org/) format. The three part, period (“.”) separated format MAY be enforced.

The `schema_id` therefore was formatted in the following way:

`<publisherDid>:<objectType>:<name>:<version>`

For example a Legacy AnonCreds `schema_id` could be:

```bash
7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7
```

Through combining each of the components into one string, it provides client applications all of the information they need to know about the schema in a simple and easily consumable format.

This is important to mention, since many client applications may still expect Schema IDs or Schema Content to contain the information or syntax within this Legacy `schema_id.`

This legacy format is now attributed to the [Hyperledger Indy Legacy AnonCreds Objects Method](https://hyperledger-indy.readthedocs.io/projects/node/en/latest/requests.html#requests)

</details>
