---
description: cheqd support for Ledger-Agnostic AnonCreds schemas
---

# Schema Object

## Overview

Schemas are used to list a set of attributes. Issuers of Verifiable Credentials may reference schemas within Credentials they issue in order to provide a layer of semantic interoperability with other issuers utilising the same schema.

In the [AnonCreds Specification](https://hyperledger.github.io/anoncreds-spec/), schemas are written directly to a [Verifiable Data Registry](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-decentralised-identifier-did/what-is-a-verifiable-data-registry), rather than using a centralized service such as [schema.org](https://schema.org/). Schemas are also associated with Credential Definitions, which are used to link the schema, issuer and holder together ([detailed further here](creddef-object.md)).

This documentation will guide an implementor of AnonCreds on cheqd on how the cheqd AnonCreds Object Method defines and structures Schema IDs and associated content.

### Hyperledger AnonCreds Schema ID

Each specific AnonCreds identifier must be defined within an AnonCreds Object Method in the [AnonCreds Object Method Registry](https://hyperledger.github.io/anoncreds-spec/).

This means that an AnonCreds Schema Object ID does not need to be formatted in any particular syntax, in the latest version of the [AnonCreds Specification](https://hyperledger.github.io/anoncreds-spec/).

### Ledger-Agnostic AnonCreds Schema Object Content

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

## cheqd AnonCreds Object method for Schemas

### cheqd Schema ID

cheqd [DID-Linked Resources](../did-linked-resources/) identify individual resources, associated with a DID, using DID URLs.

cheqd resources implementation uses the following path-based syntax:

`did:cheqd:mainnet:<SchemaIssuerId>/resources/<SchemaId>`

The cheqd AnonCreds object method uses a UUID to identify the Schema Object Content which includes additional Schema Object Content Metadata, providing the required fields for equivalence with Hyperledger Indy implementations of AnonCreds.

For example, the following DID URL is cheqd's representation of a `schema_id`:

`did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497`

### cheqd Schema Object Content

Before creating any on-ledger transaction, it is important to assemble to required Schema Object Content and save it as a file locally.

In the example below, the content should be saved as a file, for example: `degreeSchema.json` with the following content:

```json
{
"AnonCredsObject": {
  "issuerId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K",
  "name": "degreeSchema",
  "version": "1.5.7",
  "attrNames": ["name", "age", "vmax"]
  }
"AnonCredsObjectMetadata": {
    "objectFamily": "anoncreds",
    "objectFamilyVersion": "v1",
    "objectType": "2",
    "typeName": "SCHEMA",
    "objectUri": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497"
    }
  }
}
```

This implementation uses AnonCredsObjectMetadata to provide equivalency between cheqd's AnonCreds Object Method and other AnonCreds Object Methods, including the fields, where:

| Object Metadata field | Response                                                   | Method Equivalence                     |
| --------------------- | ---------------------------------------------------------- | -------------------------------------- |
| objectFamily          | anoncreds                                                  | did:Indy Objects Method                |
| objectFamilyVersion   | v1                                                         | did:Indy Objects Method                |
| objectType            | 2                                                          | Legacy Hyperledger Indy Objects Method |
| typeName              | `SCHEMA`                                                   | Legacy Hyperledger Indy Objects Method |
| objectUri             | Fully qualified DID URL to easily access the Schema Object | cheqd Objects Method                   |

{% hint style="info" %}
Note: The cheqd ledger will not provide any checks on the Schema Object Content or Metadata. Therefore, it is the responsibility of the Schema creator to make sure that the `name,` `version` and AnonCredsObjectMetadata are correct and aligned with resourceName and resourceVersion.
{% endhint %}

### Legacy AnonCreds Schema ID

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

{% hint style="info" %}
This is important to mention, since many client applications may still expect Schema IDs or Schema Content to contain the information or syntax within this Legacy `schema_id.`
{% endhint %}

{% hint style="info" %}
This legacy format is now attributed to the [Hyperledger Indy Legacy AnonCreds Objects Method](https://hyperledger-indy.readthedocs.io/projects/node/en/latest/requests.html#requests)
{% endhint %}

### cheqd create Schema transaction

To create a Schema on cheqd, you need to carry out a resource transaction, specifying the following information.

```
cheqd-noded tx resource create-resource \
    --collection-id 7BPMqYgYLQni258J8JPS8K \
    --resource-id 6259d357-eeb1-4b98-8bee-12a8390d3497 \
    --resource-name degreeSchema \
    --resource-type schema \
    --resource-version 1.5.7 \
    --resource-file degreeSchema.json \
    did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K#key1 \
    l6KUjm...jz8Un7QCbrW1KPE6gSyg== \
     --from <your-account> \
     --node https://rpc.cheqd.network:443 \
     --chain-id cheqd-mainnet-1 \
     --gas auto \
     --gas-adjustment 1.3 \
     --gas-prices 25ncheq
```

Note that this transaction includes the file `degreeSchema.json` that was formatted prior to creating the transaction.

### cheqd resource Metadata

Once you have created your resource on cheqd, the following metadata will be generated in the DID Document Metadata associated with `did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K`

```json
"resourceURI": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
    "resourceCollectionId": "7BPMqYgYLQni258J8JPS8K",
    "resourceId": "6259d357-eeb1-4b98-8bee-12a8390d3497",
    "resourceName": "degreeSchema",
    "resourceType": "schema",
    "resourceVersion": "1.5.7",
    "mediaType": "application/json",
    "created": "2022-07-19T08:40:00Z",
    "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
    "previousVersionId": null,
    "nextVersionId": null
```

### Fetching a cheqd Schema Object

Rather than requiring a specific GET\_SCHEMA function and interface to fetch the Schema Object Content (such as that required on Indy for the `schema_id` (`7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7`), existing DID Resolvers will be able to query for the Schema Object Content using the following parameters:

Common and standardized `resource` parameters:

| Parameter                 | Type                                                          | Example                                                                                                                                                                                                                                                                                                                                                    |
| ------------------------- | ------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"resourceId"`            | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                             |
| `"resourceName"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw                                                                                                                                                                                                                                                                                      |
| `"resourceType"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020                                                                                                                                                                                                                                                         |
| `"resourceVersionId"`     | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceVersionId=1.3.1                                                                                                                                                                                                                                                             |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&versionTime=2015-03-11T05:30:02Z                                                                                                                                                                                                                       |
| `"versionId"`             | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?versionId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                              |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&versionTime=2018-07-19T08:40:00Z                                                                                                                                                                                                                       |
| `"linkedResource"`        | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?linkedResource=true // _note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document._                                                                                                                                                                 |
| `"resourceMetadata"`      | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&versionTime=2018-07-19T08:40:00Z\&resourceMetadata=true or, did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceMetadata=true // note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document. |
| "`latestResourceVersion`" | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&latestResourceVersion=true                                                                                                                                                                                                                             |
| "`allResourceVersions`"   | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&allResourceVersions=true                                                                                                                                                                                                                               |

#### Query by name and version

Like via the Legacy AnonCreds `schema_id,` it is possible to obtain the Schema Object Content by querying the Schema Publisher DID, Schema name and Schema Version. The following query will dereference to the Schema Object Content itself:

`did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K?resouceName=degreeSchema&resourceType=schema&resouceVersion=1.5.7`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K?resouceName=degreeSchema&resourceType=schema&resouceVersion=1.5.7`

#### Query by resource ID

For applications which are cheqd-aware, it would be possible to find the Schema Object Content via the `resourceId` using a fully qualified DID URL path or query, for example:

`did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497`

or,

`did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K?resourceId=6259d357-eeb1-4b98-8bee-12a8390d3497`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K?resourceId=6259d357-eeb1-4b98-8bee-12a8390d3497`
