# Revocation Registry Entry Object

## Overview

In the [AnonCreds Specification](https://anoncreds-wg.github.io/anoncreds-spec), Revocation Registry Entry Objects contain the state of the Revocation Registry at a given point in time. This enables:

1. Holders of Verifiable Credentials to generate a proof of non-revocation (or not) about their specific credential; and
2. Verifiers to be able to verify that proof.

An initial Revocation Registry Entry is generated and published immediately on creation of the [Revocation Registry Definition Object](revocation-registry-definition-object.md) so that it can be used immediately by holders. Over time, additional Revocation Registry Entry Objects are generated and published as the revocation status of one or more credentials within the Revocation Registry change.

This documentation will guide an implementor of AnonCreds on cheqd on how the cheqd AnonCreds Object Method defines and structures Revocation Registry Entry IDs and associated content.

### AnonCreds Revocation Registry Entry ID

Each specific AnonCreds identifier must be defined within an AnonCreds Object Method in the [AnonCreds Object Method Registry](https://anoncreds-wg.github.io/anoncreds-spec/#anoncreds-objects-methods-registry)).

This means that an AnonCreds Revocation Registry Entry Object ID does not need to be formatted in any particular syntax, in the latest version of the AnonCreds Specification.

### Legacy AnonCreds Revocation Registry Entry ID

The Legacy AnonCreds Revocation Registry Entry ID was very similar in composition to the [Revocation Registry Definition Object](https://anoncreds-wg.github.io/anoncreds-spec/#anoncreds-objects-methods-registry).

The only difference is that the Revocation Registry Entry ID includes the Revocation Registry Entry ID `objectType`, which is "`5`".

The structure of the Legacy AnonCreds Revocation Registry Entry ID is as follows:

```
<objectType><RevocRegDefId>
```

For example:

```
5:zF7rhDBfUt9d1gJPjx7s1J:4:zF7rhDBfUt9d1gJPjx7s1J:3:CL:7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7:credDefDegree:CL_ACCUM:degreeCredRevRegDef
```

{% hint style="warning" %}
Important: Each AnonCreds Revocation Registry Entry has the **same ID** for a given Revocation Registry Definition Object.
{% endhint %}

> This is important to mention, since many client applications may still expect RevRegEntry IDs or RevRegEntry Content to contain the information or specific syntax of this Legacy `revocRegEntryId.`
This legacy format is now attributed to the Hyperledger Indy Legacy AnonCreds Objects Method

### AnonCreds Revocation Registry Entry Content

The required content and data model for the AnonCreds Revocation Registry Entry Object are as follows:

1. `revocDefType`
2. `revocRegDefId`
3. `accum`: the calculated cryptographic accumulator reflecting the initial state of the Revocation Registry Definition Object.

For example:

```json
{
  "data": {
    "revocDefType": "CL_ACCUM",
    "revocRegDefId": "Gs6cQcvrtWoZKsbBhD3dQJ:4:Gs6cQcvrtWoZKsbBhD3dQJ:3:CL:140389:mctc:CL_ACCUM:1-1024",
    "value": {
      "accum": "21 10B...33D"
    }
  }
}
```

Therefore, for each new Revocation Registry Entry, the 'ID' remains the same, as does the `revocRegType` and `revocRegDefId` - the only value that changes is the `accum,` reflecting the delta between the previous and most recent Revocation Registry Entries. \`\`

## cheqd AnonCreds Object Method for Revocation Registry Definition Objects

### cheqd Revocation Registry Entry Object ID

cheqd [on-ledger resources](../../resources/) identify individual resources using DID URLs.

cheqd resources module uses the following format:

`did:cheqd:mainnet:<issuerDid>/resources/<revRegRevEntryId>`

Rather than using a composite string for the Revocation Registry Entry Resource ID. The cheqd AnonCreds object method uses a UUID to identify the Revocation Registry Entry Object Content which includes additional Revocation Registry Entry Object Content Metadata, providing the required fields for equivalence with Hyperledger Indy implementations.

For example, the following DID URL is cheqd's representation of a `revocRegRevEntryId`:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/9d26b902-555d-43bd-bac3-0bedeb462887`

> Unlike in Hyperledger Indy implementations, cheqd uses a **unique identifier for each specific Revocation Registry Entry ID**. This enables better indexing and searchability.


>For conformance with the cheqd AnonCreds Object Method, each Revocation Registry Entry **MUST** be created in the **same Collection** as the Revocation Registry Definition Object.

### cheqd Revocation Registry Entry Object Content

Before creating any on-ledger resource transaction, it is important to assemble to required Revocation Registry Entry Object Content and save it as a file locally.

cheqd's approach to AnonCreds Revocation Entry Objects implements the following logic:

1. The required (ledger-agnostic) content of the Revocation Registry Entry Object Content should be included in the body of the content.
2. Anything that is network-specific should be included within AnonCreds Object Metadata.

In the example below, the content should be saved as a file, for example: `degreeCredRevocRegEntry1.json` with the following content:

```json
{
  "AnonCredsObject": {
    "data": {
      "revocDefType": "CL_ACCUM",
      "revocRegDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
      "value": {
        "accum": "21 10B...33D"
      },
    }
    "AnonCredsObjectMetadata": {
      "objectFamily": "anoncreds",
      "objectFamilyVersion": "v1",
      "objectType": "5",
      "publisherId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J",
      "objectUri": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/9d26b902-555d-43bd-bac3-0bedeb462887",
      "legacyObjectId": "5,did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J,4,did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J,3,CL,did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K,2,degreeSchema,1.5.7,credDefDegree,CL_ACCUM,degreeCredRevRegDef"
    }
  }
}
```

This implementation uses AnonCredsObjectMetadata to provide equivalency between cheqd's AnonCreds Object Method and other AnonCreds Object Methods, including the fields, where:

| Object Metadata field | Response                                                                        | Method Specification / Equivalency      |
| --------------------- | ------------------------------------------------------------------------------- | --------------------------------------- |
| objectFamily          | anoncreds                                                                       | did:indy Objects Method                 |
| objectFamilyVersion   | v1                                                                              | did:indy Objects Method                 |
| objectType            | 5                                                                               | Legacy Hyperledger Indy Objects Method  |
| typeName              | `CL_ACCUM`                                                                      | Legacy Hyperledeger Indy Objects Method |
| publisherId           | Fully qualified DID to easily identify the publisher of the Revocation Registry | cheqd Objects Method                    |
| objectUri             | Fully qualified DID URL to easily access the Revocation Registry Entry          | cheqd Objects Method                    |
| legacyObjectId        | The Legacy AnonCreds ID which may be expected by client applications            | Legacy Hyperledger Indy Objects Method  |

### cheqd Revocation Registry Entry transaction

To create a Revocation Registry Entry on cheqd, you need to carry out a resource transaction, specifying the following information (if using the [cheqd Cosmos CLI](../../resources/tutorials/)).

```bash
cheqd-noded tx resource create-resource \
    --collection-id zF7rhDBfUt9d1gJPjx7s1J \
    --resource-id 9d26b902-555d-43bd-bac3-0bedeb462887 \
    --resource-name degreeCredRevocRegEntry \
    --resource-type CL_ACCUM \
    --resource-file degreeCredRevocRegEntry1.json \
    did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J#key1 \
    l6KUjm...jz8Un7QCbrW1KPE6gSyg== \
     --from <your-account> \
     --node https://rpc.cheqd.network:443 \
     --chain-id cheqd-mainnet-1 \
     --gas auto \
     --gas-adjustment 1.3 \
     --gas-prices 25ncheq
```

### cheqd resource Metadata

Once you have created your Revocation Registry Entry as a resource on cheqd, the following metadata will be generated in the DID Document Metadata associated with `did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J`

```json
{
  "resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/9d26b902-555d-43bd-bac3-0bedeb462887",
  "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
  "resourceId": "9d26b902-555d-43bd-bac3-0bedeb462887",
  "resourceName": "degreeCredRevocRegEntry",
  "resourceType": "CL_ACCUM",
  "mediaType": "application/json",
  "created": "2022-08-21T08:40:00Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": null,
  "nextVersionId": null
}
```

{% hint style="info" %}
Next and Previous Revocation Registry Entries will appear in the resource Metadata when a new Revocation Registry Entry is made with the same resourceName and resourceType
{% endhint %}

### Linking Revocation Registry Entries

To Create a new Revocation Registry Entry as a **new version** of a previous Revocation Registry Entry, you need to create a new resource.

You must:

1. Generate a new UUID for the `resourceId`
2. Specify the same `collectionId`
3. Specify the same `resourceName`
4. Specify the same `resourceType`
5. Attach to the transaction the new `resourceFile` with the latest `accum` value.

For example, using the [cheqd Cosmos CLI](../../resources/tutorials/) to demonstrate this, a transaction may look like:

```bash
cheqd-noded tx resource create-resource \
    --collection-id zF7rhDBfUt9d1gJPjx7s1J \
    --resource-id c154bc07-43f7-4b69-ac0c-5514001f2ca3 \
    --resource-name degreeCredRevocRegEntry \
    --resource-type CL_ACCUM \
    --resource-file degreeCredRevocRegEntry2.json \
    did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J#key1 \
    l6KUjm...jz8Un7QCbrW1KPE6gSyg== \
     --from <your-account> \
     --node https://rpc.cheqd.network:443 \
     --chain-id cheqd-mainnet-1 \
     --gas auto \
     --gas-adjustment 1.3 \
     --gas-prices 25ncheq
```

Where, `degreeCredRevocRegEntry2.json` contains an updated `accum` value and `objectUri`, such as:

```json
{
  "AnonCredsRevRegEntry": {
    "data": {
      "revocDefType": "CL_ACCUM",
      "revocRegDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
      "value": {
        "accum": "52 87D...95B"
      },
    }
    "AnonCredsObjectMetadata": {
      "objectFamily": "anoncreds",
      "objectFamilyVersion": "v2",
      "objectType": "5",
      "publisherId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J",
      "objectUri": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/c154bc07-43f7-4b69-ac0c-5514001f2ca3",
      "legacyObjectId": "5,did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J,4,did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J,3,CL,did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K,2,degreeSchema,1.5.7,credDefDegree,CL_ACCUM,degreeCredRevRegDef"
    }
  }
}
```

Once the transaction has been created, the `resourceMetadata` will look like the following:

```
{
  "resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/c154bc07-43f7-4b69-ac0c-5514001f2ca3",
  "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
  "resourceId": "c154bc07-43f7-4b69-ac0c-5514001f2ca3",
  "resourceName": "degreeCredRevocRegEntry",
  "resourceType": "CL_ACCUM",
  "mediaType": "application/json",
  "created": "2022-09-01T04:30:01Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": "c154bc07-43f7-4b69-ac0c-5514001f2ca3",
  // points to previous Revocation Registry Entry
  "nextVersionId": null
}
```

{% hint style="info" %}
Note: The previousVersionId will now link to the previous Revocation Registry Entry ID
{% endhint %}

### Traversing Revocation Registry Entries using a DID Resolver

Using existing DID Resolvers, it is possible to traverse the history of Revocation Registry Entries to query deltas in the cryptographic accumulator in order to produce proofs of non-revocation required in the [AnonCreds Specification](https://anoncreds-wg.github.io/anoncreds-spec).

Common and standardized `resource` parameters:

| Parameter                 | Type                                                          | Example                                                                                                                                                                                                                                                                                                                                                                                            |
| ------------------------- | ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"resourceId"`            | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                                                                     |
| `"resourceName"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw                                                                                                                                                                                                                                                                                                                              |
| `"resourceType"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020                                                                                                                                                                                                                                                                                                 |
| `"resourceVersionId"`     | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceVersionId=1.3.1                                                                                                                                                                                                                                                                                                     |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&versionTime=2015-03-11T05:30:02Z                                                                                                                                                                                                                                                               |
| `"versionId"`             | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?versionId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                                                                      |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&versionTime=2018-07-19T08:40:00Z                                                                                                                                                                                                                                                               |
| `"linkedResource"`        | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?linkedResource=true // _note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document._                                                                                                                                                                                                         |
| `"resourceMetadata"`      | [Boolean](https://infra.spec.whatwg.org/#booleans)            | <p>did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw&#x26;resourceType=JSONSchema2020&#x26;versionTime=2018-07-19T08:40:00Z&#x26;resourceMetadata=true</p><p>or,</p><p>did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceMetadata=true // <em>note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document.</em></p> |
| "`latestResourceVersion`" | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&latestResourceVersion=true                                                                                                                                                                                                                                                                     |
| "`allResourceVersions`"   | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeLaw\&resourceType=JSONSchema2020\&allResourceVersions=true                                                                                                                                                                                                                                                                       |

#### Obtain all Revocation Registry Entry Content

A DID URL such as the following will display all of the accumulators associated with a particular Revocation Registry:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&allResourceVersions=true`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&allResourceVersions=true`

{% hint style="info" %}
It is recommended that applications using this revocation method implement a way of caching a historical list of accumulators and deltas to prevent the application from having to fetch the entire list of Revocation Registry Entries every time a proof of non-revocation is required.
{% endhint %}

#### Obtain Revocation Registry Entry Content at a point in time

Furthermore, it will be possible to query Revocation Entries at certain times. This may be very useful if you want to prove whether a Verifiable Credential was valid in the past.

For example:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&versionTime=2022-08-21T08:40:00Z`

#### Obtain latest Revocation Registry Entry

It will be very common for a proof of non-revocation to require the latest Revocation Registry Entry and work its way back from there.

The following DID URL will be able to call the latest Revocation Registry Entry

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&latestResourceVersion=true`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&latestResourceVersion=true`
