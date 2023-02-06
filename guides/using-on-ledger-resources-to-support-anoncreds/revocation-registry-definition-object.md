---
description: cheqd support for Ledger-Agnostic AnonCreds Revocation Registry Definitions
---

# Revocation Registry Definition Object

## Overview

In the ledger-agnostic [AnonCreds](https://hyperledger.github.io/anoncreds-spec/)[ specification](https://hyperledger.github.io/anoncreds-spec/), a Revocation Registry Definition Object acts as an on-ledger hub for revocation, providing a central point information about the:

1. `type` of Revocation Registry (In Indy this is always "`CL_ACCUM`").
2. `cred_def_id`: Each Revocation Registry must be linked to one specific Credential Definition.
3. `tag`: An issuer-specified name for the Revocation Registry, to ensure consistency when referencing the registry.
4. `maxCredNum`: The maximum amount of Credentials that can be revoked in the Revocation Registry before a new one needs to be started.
5. `tailsLocation`: A [URL](https://www.rfc-editor.org/rfc/rfc1738) resolving to the location of the Tails File.

{% hint style="info" %}
A Tails File is a large file containing a cryptographic accumulator value of prime numbers multiplied together. When a Credential is revoked, the value of the accumulator changes, removing the cryptographic value of the Credential as a factor of the accumulator value.
{% endhint %}

Each credential issued using the Revocation Registry Definition is given its own index (1 to the `maxCredNum`).

While not required, the Indy community has created a component, the “[Indy Tails Server](https://github.com/bcgov/indy-tails-server),” which is basically a web server for storing Tails Files.

This documentation will guide an implementor of AnonCreds on cheqd on how the cheqd AnonCreds Object Method defines and structures Revocation Registry Definition IDs and associated content.

### AnonCreds Revocation Registry Definition Object ID

Each specific AnonCreds identifier must be defined within an AnonCreds Object Method in the [AnonCreds Object Method Registry](https://hyperledger.github.io/anoncreds-spec/).

This means that an AnonCreds Revocation Registry Object ID does not need to be formatted in any particular syntax, in the latest version of the AnonCreds Specification.

{% hint style="info" %}
See the collapsible tile below to learn about how the Ledger-Agnostic AnonCreds specification handles these objects.
{% endhint %}

<details>

<summary>Ledger-agnostic AnonCreds Revocation Registry Definition Object Content</summary>

The required content and data model for the AnonCreds Revocation Registry Definition Object are as follows:

* `issuerId` - the [Issuer Identifier](https://hyperledger.github.io/anoncreds-spec/#term:issuer-identifier) of the revocation registry. MUST adhere to [Issuer Identifiers](https://hyperledger.github.io/anoncreds-spec/#issuer-identifiers) rules and MUST be the same `issuerId` as the [Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:credential-definition) on which the [Revocation Registry](https://hyperledger.github.io/anoncreds-spec/#term:revocation-registry) is based.
* `revocDefType`: the input parameter `type` (This is currently always `CL_ACCUM`)
* `credDefId`: the input parameter `cred_def_id`, [further explained here](creddef-object.md).
* `tag` - an arbitrary string defined by the \[ref: issuer], enabling an \[ref: issuer] to create multiple [Revocation Registry Definition](https://hyperledger.github.io/anoncreds-spec/#term:revocation-registry-definition)s for the same [Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:credential-definition).
* `value` - The value of the revocation registry definition
  * `publicKeys` - Public keys data for signing the accumulator; the public key of a private/public key pair
    * `accumKey` - Accumulator key for signing the accumulator
      * `z` - a public key used to sign the accumulator (described further below)
  * `maxCredNum` - The maximum amount of Credentials that can be revoked in the Revocation Registry before a new one needs to be started
  * `tailsLocation` - The URL pointing to the related tails file
  * `tailsHash` - The hash of the tails file [TAILS\_FILE](https://hyperledger.github.io/anoncreds-spec/#term:tails\_file) (see also: [next section](https://hyperledger.github.io/anoncreds-spec/#tails-file-and-tails-file-generation)) resulting from hashing the tails file version prepended to the tails file as SHA256 and then encoded to base58.

For example, the on-ledger Revocation Registry Definition Object Content is as fol

```json
{
  "issuerId": "did:web:example.org",
  "revocDefType": "CL_ACCUM",
  "credDefId": "Gs6cQcvrtWoZKsbBhD3dQJ:3:CL:140384:mctc",
  "tag": "MyCustomCredentialDefinition",
  "value": {
    "publicKeys": {
      "accumKey": {
        "z": "1 0BB...386"
      }
    },
    "maxCredNum": 666,
    "tailsLocation": "https://my.revocations.tails/tailsfile.txt",
    "tailsHash": "91zvq2cFmBZmHCcLqFyzv7bfehHH5rMhdAG5wTjqy2PE"
  }
}
```

</details>

## cheqd AnonCreds Object Method for Revocation Registry Definition Objects

### cheqd Revocation Registry Definition Object ID

cheqd [on-ledger resources](broken-reference) identify individual resources using DID URLs.

cheqd resources module uses the following format:

`did:cheqd:mainnet:<issuerDid>/resources/<revRegDefResourceId>`

Rather than using a composite string for the Revocation Registry Definition Resource ID. The cheqd AnonCreds object method uses a UUID to identify the Revocation Registry Definition Object Content which includes additional Revocation Registry Definition Object Content Metadata, providing the required fields for equivalence with Hyperledger Indy implementations.

For example, the following DID URL is cheqd's representation of a `revocRegDefId`:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df`

### cheqd Revocation Registry Definition Object Content

Before creating any on-ledger resource transaction, it is important to assemble to required Revocation Registry Definition Object Content and save it as a file locally.

cheqd's approach to AnonCreds Revocation Registry Definition Objects implements the following logic:

1. The required (ledger-agnostic) content of the Revocation Registry Definition Object Content should be included in the body of the content.
2. Anything that is network-specific should be included within AnonCreds Object Metadata.

In the example below, the content should be saved as a file, for example: `degreeCredRevocRegDef.json` with the following content:

```json
{
"AnonCredsObject: {
  "issuerId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J",
  "revocDefType": "CL_ACCUM",
  "credDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0",
  "tag": "degreeRevocRegDef",
  "value": {
    "publicKeys": {
      "accumKey": {
        "z": "1 0BB...386"
      }
    },
    "maxCredNum": 666,
    "tailsLocation": "https://my.revocations.tails/tailsfile.txt",
    "tailsHash": "91zvq2cFmBZmHCcLqFyzv7bfehHH5rMhdAG5wTjqy2PE"
  },
"AnonCredsObjectMetadata" {
  "objectFamily": "anoncreds",
  "objectFamilyVersion": "v1",
  "objectType": "4",
  "typeName": "REVOC_REG_DEF"
  "objectUri": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df"
}
```

{% hint style="info" %}
Note: The Credential Definition ID specified must have enabled revocation support for the Revocation Registry Definition to be able to created. This implementation uses \`AnonCredsObjectMetadata to provide equivalency between cheqd's AnonCreds Object Method and other AnonCreds Object Methods, including the fields, where:
{% endhint %}

| Object Metadata field | Response                                                                    | Method Specification / Equivalency     |
| --------------------- | --------------------------------------------------------------------------- | -------------------------------------- |
| objectFamily          | anoncreds                                                                   | did:indy Objects Method                |
| objectFamilyVersion   | v1                                                                          | did:indy Objects Method                |
| objectType            | 4                                                                           | Legacy Hyperledger Indy Objects Method |
| typeName              | `REVOC_REG_DEF`                                                             | Legacy Hyperledger Indy Objects Method |
| objectUri             | Fully qualified DID URL to easily access the Revocation Registry Definition | cheqd Objects Method                   |

<details>

<summary>Legacy AnonCreds Revocation Registry Definition Object ID</summary>

Like both the Legacy [Schema Object ID](schema-object.md#anoncreds-schema-id) and the Legacy [CredDef Object ID](creddef-object.md#creddef-object-id), the Legacy Revocation Registry Definition Object ID was defined as a composite string of the following information:

1. `Publisher DID`: The DID of the creator of the revocation registry. Generally this will be the same publisher as the creator of the [CredDef Object](creddef-object.md).
2. `Object Type`: An integer denoting the type of object. `4` is used for Revocation Registry Objects.
3. `CredDef Object ID`: This is the [AnonCreds CredDef Object ID](creddef-object.md#anoncreds-creddef-object-id).
4. `Revocation Registry Type`: The type of Revocation Registry used, this is by default always "`CL_ACCUM`".
5. `tag`: A unique name or tag given to the Revocation Registry Object.

The `revocRegDefId` therefore was formatted in the following way:

```bash
<publisherDid>:<objectType>:<credDefId>:<revRegType>:<tag>
```

For example a Legacy AnonCreds `revocRegDefId` could be:

```bash
zF7rhDBfUt9d1gJPjx7s1J:4:zF7rhDBfUt9d1gJPjx7s1J:3:CL:7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7:credDefDegree:CL_ACCUM:degreeCredRevRegDef
```

Through combining each of the components into one string, it provides client applications all of the information they need to know about the Revocation Registry Definition Object in a simple and easily consumable format.

This is important to mention, since many client applications may still expect RevRegDef IDs or RevRegDef Content to contain the information or specific syntax of this Legacy `revocRegDefId.`

This legacy format is now attributed to the [Hyperledger Indy Legacy AnonCreds Objects Method](https://hyperledger-indy.readthedocs.io/projects/node/en/latest/requests.html#requests)

</details>

### create Revocation Registry Definition transaction

To create a Revocation Registry Definition on cheqd, you should follow the [tutorials for creating a DID-Linked Resource here](../../tutorials/on-ledger-resources/), and pass the relevant JSON file for the object in the transaction.&#x20;

{% hint style="info" %}
Note: This transaction includes the file `degreeRevocRegDef.json` that was formatted prior to creating the transaction.
{% endhint %}

### cheqd resource Metadata

Once you have created your Revocation Registry as a resource on cheqd, the following metadata will be generated in the DID Document Metadata associated with `did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J`

```json
"linkedResourceMetadata": [
  {
  "resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
  "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
  "resourceId": "af20b1f0-5c4d-4037-9669-eaedddb9c2df",
  "resourceName": "degreeRevocRegDef",
  "resourceType": "revocRegDef",
  "resourceVersion": "0.0.1",
  "mediaType": "application/json",
  "created": "2022-08-21T08:40:00Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": null,
  "nextVersionId": null
  }
]
```

### CredDef and RevRegDef Objects in the same Collection

Across the [cheqd CredDef Object Method](creddef-object.md#cheqd-anoncreds-object-method-for-creddefs) and the [Revocation Registry Definition Object Method](revocation-registry-definition-object.md#cheqd-anoncreds-object-method-for-revocation-registry-definition-objects), each resource is associated with the same issuer DID and Collection ID.

Importantly, this allows each new resource to be indexed and versioned by their:

1. `resourceName`
2. `resourceType`

New resources can be created to update the existing CredDef or RevRegDef, whilst maintaining the historical state of the previous versions. See the documentation on [Publishing a New Version of a Resource](../../advanced-features-and-alternatives/cheqd-cosmos-cli-for-identity/add-resource-to-existing-collection.md) to understand this further.

### Fetching a cheqd Revocation Registry Definition Object

Rather than requiring a specific _GET\_REV\_REG_ function and interface to fetch the Revocation Registry Definition Object Content (such as that required on Indy), existing DID Resolvers will be able to query for the Revocation Registry Definition Object Content using the following parameters:

Common and standardized `resource` parameters:

| Parameter                 | Type                                                          | Example                                                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------------- | ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"resourceId"`            | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                                                                           |
| `"resourceName"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeRevocRegDef                                                                                                                                                                                                                                                                                                                            |
| `"resourceType"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeRevocRegDef\&resourceType=revocRegDef                                                                                                                                                                                                                                                                                                  |
| `"resourceVersionId"`     | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeRevocRegDef\&resourceVersionId=1.3.1                                                                                                                                                                                                                                                                                                   |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeRevocRegDef\&resourceType=revocRegDef\&versionTime=2015-03-11T05:30:02Z                                                                                                                                                                                                                                                                |
| `"versionId"`             | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?versionId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                                                                            |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeRevocRegDef\&resourceType=revocRegDef\&versionTime=2018-07-19T08:40:00Z                                                                                                                                                                                                                                                                |
| `"linkedResource"`        | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?linkedResource=true // _note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document._                                                                                                                                                                                                               |
| `"resourceMetadata"`      | [Boolean](https://infra.spec.whatwg.org/#booleans)            | <p>did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeRevocRegDef&#x26;resourceType=revocRegDef&#x26;versionTime=2018-07-19T08:40:00Z&#x26;resourceMetadata=true<br><br>or,<br><br>did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceMetadata=true <br><br>// note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document.</p> |
| "`latestResourceVersion`" | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeRevocRegDef\&resourceType=revocRegDef\&latestResourceVersion=true                                                                                                                                                                                                                                                                      |
| "`allResourceVersions`"   | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeRevocRegDef\&resourceType=revocRegDef\&allResourceVersions=true                                                                                                                                                                                                                                                                        |

#### Query by name and version

Like via the Legacy AnonCreds `revocRegId,` it is possible to obtain the CredDef Object Content by querying the CredDef Publisher DID and CredDef tag. The following query will dereference to the Schema Object Content itself:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=degreeRevocRegDef&resourceType=revocRegDef`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=degreeRevocRegDef&resourceType=revocRegDef`

This would return the AnonCredsCredDef data and the AnonCredsObjectMetadata.

#### Query by resource ID

For applications which are cheqd-aware, it would be possible to find the Schema Object Content via the `resourceId` using a fully qualified DID URL path or query, for example:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df`

or,

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceId=af20b1f0-5c4d-4037-9669-eaedddb9c2df`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceId=af20b1f0-5c4d-4037-9669-eaedddb9c2df`

This would return the AnonCredsCredDef data and the AnonCredsObjectMetadata.
