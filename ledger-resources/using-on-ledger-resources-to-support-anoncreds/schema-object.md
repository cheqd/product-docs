# Schema Object

## Overview

Schemas on AnonCreds are written directly to a [Verifiable Data Registry](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-decentralised-identifier-did/what-is-a-verifiable-data-registry), rather than using a centralized service such as [schema.org](https://schema.org/).&#x20;

This documentation will guide an implementor of AnonCreds on cheqd on how to replicate an AnonCreds Schema Object using [cheqd's resources on-ledger](../../resources/).

### AnonCreds Schema ID

Each Schema Object within the [AnonCreds specification](https://anoncreds-wg.github.io/anoncreds-spec/) has its own unique `schema_id`.

The Schema ID is a **composite** of the following set of elements:

* `publisher DID`: is a [string](https://infra.spec.whatwg.org/#string). The DID of the Schema Publisher.
* `object type`: An integer denoting the type of object. `2` is used for Schemas.
* `name`: is a [string](https://infra.spec.whatwg.org/#string), the name of the schema
* `version`: is a [string](https://infra.spec.whatwg.org/#string), the version of the schema in [semver](https://semver.org/) format. The three part, period (“.”) separated format MAY be enforced.

The `schema_id` therefore is formatted in the following way:

`<publisherDid>:<objectType>:<name>:<version>`

For example an AnonCreds `schema_id` could be:

```bash
7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7
```

### AnonCreds Schema Object Content

The actual Schema Object Content which is written to the Verifiable Data Registry, contains the following content:

* `attr_names`: is the array of attribute names (claim names) that will constitute the AnonCred credential of this type.
* `name`: is a [string](https://infra.spec.whatwg.org/#string), the name of the schema, which will be a part of the published `schema_id`.
* `version`: is a [string](https://infra.spec.whatwg.org/#string), the version of the schema in [semver](https://semver.org/) format. The three part, period (“.”) separated format MAY be enforced. The `version` will be part of the published `schema_id`.

For example:

```json
{
    "attr_names": [
        "birthlocation",
        "facephoto",
        "expiry_date",
        "citizenship",
        "name",
        "birthdate",
        "firstname",
        "uuid"
    ],
    "name": "degreeSchema",
    "version": "1.5.7"
}
```

Within the Schema Object Content, the `name` and the `version` should be the same as the `name` and `version` in the **composite** `schema_id.`

Therefore, the schema\_id `"7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7"` identifies the Schema Object content above.&#x20;

Once published on a Hyperledger Indy ledger, an additional identifier for the published schema, the `TXN_ID`, is available to those reading from the ledger

## cheqd AnonCreds Object method for Schemas

### cheqd Schema ID

cheqd [on-ledger resources](../../resources/) identify individual resources using DID URLs.&#x20;

cheqd resources module uses the following format:

`did:cheqd:mainnet:<SchemaProposerId>/resources/<SchemaResourceId>`

Rather than using a composite string for the Schema Resource ID. The cheqd AnonCreds object method uses a UUID to identify the Schema Object Content which includes additional Schema Object Content Metadata, providing the required fields for equivalence with Hyperledger Indy implementations.&#x20;

For example, the following DID URL is cheqd's representation of a `schema_id`:

`did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497`

### cheqd Schema Object Content

Before creating any on-ledger transaction, it is important to assemble to required Schema Object Content and save it as a file locally.&#x20;

&#x20;In the example below, the content should be saved as a file, for example: `degreeSchema.json` with the following content:

<pre><code>{
<strong>    "AnonCredsSchema": {
</strong>        "attr_names": [
            "birthlocation",
            "facephoto",
            "expiry_date",
            "citizenship",
            "name",
            "birthdate",
            "firstname",
            "uuid"
        ],
        "name": "degreeSchema",
        "version": "1.5.7"
    },
    "AnonCredsObjectMetadata": {
      "objectFamily": "anoncreds",
      "objectFamilyVersion": "v2",
      "objectType": "2",
      "publisherDid": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K",
      "objectURI": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497"
<strong>}</strong></code></pre>

This implementation uses AnonCredsObjectMetadata to provide equivalency between cheqd's AnonCreds Object Method and other AnonCreds Object Methods, including the fields, where:

| Object Metadata field | Response                                                   | Equivalency                            |
| --------------------- | ---------------------------------------------------------- | -------------------------------------- |
| objectFamily          | anoncreds                                                  | did:Indy Objects Method                |
| objectFamilyVersion   | v2                                                         | did:Indy Objects Method                |
| objectType            | 2                                                          | Legacy Hyperledger Indy Objects Method |
| publisherDid          | DID of the schema publisher                                | Legacy Hyperledger Indy Objects Method |
| objectUri             | Fully qualified DID URL to easily access the Schema Object |                                        |

{% hint style="info" %}
Note: The cheqd ledger will not provide any checks on the Schema Object Content or Metadata. Therefore, it is the responsibility of the Schema creator to make sure that the `name,` `version` and AnonCredsObjectMetadata are correct.&#x20;
{% endhint %}

### create Schema transaction

To create a Schema on cheqd, you need to carry out a resource transaction, specifying the following information.&#x20;

```
cheqd-noded tx resource create-resource \
    --collection-id 7BPMqYgYLQni258J8JPS8K \
    --resource-id 6259d357-eeb1-4b98-8bee-12a8390d3497 \
    --resource-name degreeSchema \
    --resource-type CL-Schema \
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

```
"resourceURI": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
    "resourceCollectionId": "7BPMqYgYLQni258J8JPS8K",
    "resourceId": "6259d357-eeb1-4b98-8bee-12a8390d3497",
    "resourceName": "degreeSchema",
    "resourceType": "CL-Schema",
    "resourceVersion": "1.5.7",
    "mediaType": "application/json",
    "created": "2022-07-19T08:40:00Z",
    "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
    "previousVersionId": null,
    "nextVersionId": null
```

### Fetching a cheqd schema object

Rather than requiring a specific GET_SCHEMA function and interface to fetch the Schema Object Content (such as that required on Indy for the_ `schema_id` __ 7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7), existing DID Resolvers will be able to query for the Schema Object Content using the following parameters:

1. Query by name and version

Like the AnonCreds `schema_id,` it is possible to obtain the Schema Object Content by querying the Schema Publisher DID, Schema name and Schema Version. The following query will dereference to the Schema Object Content itself:&#x20;

`did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K?resouceName=degreeSchema&resourceType=CL-Schema&resouceVersion=1.5.7`

2\. Query by resource ID

For applications which are cheqd-aware, it would be possible to find the Schema Object Content via the `resourceId` using a fully qualified DID URL path or query, for example:&#x20;

`did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497`

or,&#x20;

`did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K?resourceId=6259d357-eeb1-4b98-8bee-12a8390d3497`
