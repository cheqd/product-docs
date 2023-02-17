---
description: cheqd support for Ledger-Agnostic AnonCreds Revocation Registry Definitions
---

# Revocation Registry Definition Object

## cheqd AnonCreds Object Method for Revocation Registry Definition Objects

In the ledger-agnostic [AnonCreds](https://hyperledger.github.io/anoncreds-spec/) [specification](https://hyperledger.github.io/anoncreds-spec/), a Revocation Registry Definition Object acts as an on-ledger hub for revocation, providing a central point information about the:

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

If you are not familiar with the latest Ledger-Agnostic AnonCreds Revocation Registry Definition structure, click the collapsible tile below to learn about the new format.

<details>

<summary>Ledger-agnostic AnonCreds Revocation Registry Definition Object Content</summary>

Each specific AnonCreds identifier must be defined within an AnonCreds Object Method in the [AnonCreds Object Method Registry](https://hyperledger.github.io/anoncreds-spec/).

This means that an AnonCreds Revocation Registry Object ID does not need to be formatted in any particular syntax, in the latest version of the AnonCreds Specification.

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

### cheqd Revocation Registry Definition Object ID

cheqd uses [DID-Linked Resources](../did-linked-resources/) to identify individual resources, associated with a DID, using fully resolvable DID URLs.

cheqd resources module uses the following format:

`did:cheqd:mainnet:<issuerDid>/resources/<revRegDefResourceId>`

Rather than using a composite string for the Revocation Registry Definition Resource ID. The cheqd AnonCreds object method uses a UUID to identify the Revocation Registry Definition Object Content.

For example, the following DID URL is cheqd's representation of a `revocRegDefId`:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df`

### Understanding Request vs Response formats

It is important to differentiate between the **Request format** for creating an AnonCreds object on cheqd, and the **Response format**, for how an AnonCreds objectshould be compiled by SDKs and the [cheqd DID Resolver](../../architecture/adr-list/adr-003-did-resolver.md).&#x20;

The request format _**may**_ be specific to each AnonCreds Object Method. However, the response format _**should**_ be standardised to enable any AnonCreds supported application to understand the object, without needing custom or method-specific logic.

### cheqd Revocation Registry Definition Request format

The cheqd revocation registry definition request format comprises of:

1. A revocation registry definition object file (e.g. `degreeRevocRegDef.json`);
2. A Payload file (including the signing keys)
3. Additional input parameters.

Both of these inputs are required to provide the ledger enough information to:

1. Populate a [cheqd DID-Linked Resource](../did-linked-resources/); and
2. Compile a standardised AnonCreds revocation registry definition object in the [Response format](revocation-registry-definition-object.md#cheqd-revocation-registry-definition-response-format).&#x20;

#### Revocation Registry Definition Object file

Before creating any on-ledger resource transaction, it is important to assemble the required Revocation Registry Definition Object Content and save it as a file locally.

In the example below, the content should be saved as a file, for example: `degreeRevocRegDef.json` with the following content:

```json
{
  "revocDefType": "CL_ACCUM",
  "credDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0",
  "tag": "2.0",
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
```

{% hint style="info" %}
Note: The Credential Definition ID specified must have enabled revocation support for the Revocation Registry Definition to be able to be used properly in an SDK.
{% endhint %}

This Revocation Registry Definition Object file maps the fields of the Revocation Registry Definition Object to populate a [DID-Linked resource](../did-linked-resources/) stored on cheqd, with the following mapping:

| Object Metadata field | Object Metadata input | Mapped cheqd field | Mapped field output    |
| --------------------- | --------------------- | ------------------ | ---------------------- |
| "type"                | "CL\_ACCUM"           | "resourceType"     | "anonCredsRevocRegDef" |
| "tag"                 | ""                    | "resourceVersion"  | ""                     |

What this means is that if the resource data file has an object of "type" = "CL\_ACCUM" then it will automatically populate the "resourceType" as "anonCredsRevocRegDef" when creating the "payload file".

#### Revocation Registry Definition Payload File

The Payload file extracts the information from the Revocation Registry Definition Object file to populate the following:

```json
{
  "Payload": {
    "collectionId": "zF7rhDBfUt9d1gJPjx7s1J",
    "id": "af20b1f0-5c4d-4037-9669-eaedddb9c2df",
    "resourceType": "anonCredsRevocRegDef",
    "version": "2.0",
    "alsoKnownAs": []
  },
  "SignInputs": [
    {
      "verificationMethodID": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J#key1",
      "privKey": "y4B5qis9BXUq/mODsrWtS3q5ejOk/okSIXlX1/a9HvuG3PgYmekfQmq3QhJ4JSzN/rkiGCQDNKoTXMmxuXDHbg=="
    }
  ]
}
```

#### **Request format additional inputs**

When passing the payload file to the ledger, additional information MUST also be provided as `flags` to fully populate the [DID-Linked resource](../did-linked-resources/).&#x20;

| Additional parameter | Expected input     | Rationale                                                                                                                                                                                        |
| -------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| "name"               | "universityDegree" | The payload file on its own does not provide the ledger the requisite amount of information to create a full DID-Linked Resource. resourceName must be provided as an additional input parameter |

For example:

```bash
cheqd-noded tx anonCredsRevocRegDef create \
  --chain-id cheqd \
  --keyring-backend test \
  --output json \
  --fees 10000000000ncheq \
  --gas auto \
  --gas-adjustment 1.8 \
  --from base_account \
  --name universityDegree \
  "payload.json" degreeRevocRegDef.json
```

### cheqd resource Metadata

Once you have created your Revocation Registry as a resource on cheqd, the following metadata will be generated in the DID Document Metadata associated with `did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J`

```json
"linkedResourceMetadata": [
  {
  "resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
  "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
  "resourceId": "af20b1f0-5c4d-4037-9669-eaedddb9c2df",
  "resourceName": "universityDegree",
  "resourceType": "anonCredsRevocRegDef",
  "resourceVersion": "2.0",
  "mediaType": "application/json",
  "created": "2022-08-21T08:40:00Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": null,
  "nextVersionId": null
  }
]
```

### cheqd Revocation Registry Definition Response format

Using the cheqd [Revocation Registry Definition Request format](revocation-registry-definition-object.md#cheqd-revocation-registry-definition-request-format) and [associated resource metadata](revocation-registry-definition-object.md#cheqd-resource-metadata), the ledger has enough information to compile the following data structure as a response format.

This can either be compiled by the associated SDK handling cheqd AnonCreds, or it can be assembled by the cheqd DID resolver.&#x20;

```json
{
  "issuerId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J"
  "revocDefType": "CL_ACCUM",
  "credDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0",
  "tag": "2.0",
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
```

#### Compiling Response format in cheqd DID Resolver

The cheqd DID resolver will use the following logic to compile the standardised response format:

{% hint style="info" %}
_If "**resourceType=anonCredsRevocRegDef**" then **append "issuerId"** to the beginning of the Response Format for the resource presented_
{% endhint %}

### create Revocation Registry Definition transaction

To create a Revocation Registry Definition on cheqd, you should follow the [tutorials for creating a DID-Linked Resource here](../../tutorials/on-ledger-resources/), and pass the relevant JSON file for the object in the transaction.

## Tying CredDef, RevRegDef and StatusListEntry Objects together

Across the [cheqd CredDef Object Method](creddef-object.md#cheqd-anoncreds-object-method-for-creddefs), the [Revocation Registry Definition Object Method](revocation-registry-definition-object.md#cheqd-anoncreds-object-method-for-revocation-registry-definition-objects) and the [StatusListEntry Object Method](revocation-registry-entry-object.md) - each resource is associated with the same issuer DID and Collection ID.

Importantly, this allows each new resource to be indexed and versioned by their:

1. `resourceName`
2. `resourceType`

New resources can be created to update the existing CredDef or RevRegDef, whilst maintaining the historical state of the previous versions. See the documentation on [Publishing a New Version of a Resource](../../advanced-features-and-alternatives/cheqd-cosmos-cli-for-identity/add-resource-to-existing-collection.md) to understand this further.

## Fetching a cheqd Revocation Registry Definition Object

Existing DID Resolvers will be able to query for the Revocation Registry Definition Object Content using the [same patterns and parameters as the Schema Object found here](schema-object.md#fetching-a-cheqd-resource).&#x20;

The cheqd AnonCreds method also enables applications to derive the [CredDef](creddef-object.md), [Revocation Registry Definition Object](revocation-registry-definition-object.md) and [Status List Entries](revocation-registry-entry-object.md) from the same root:

### Same Resource Name, different Resource type

We propose that the `resourceName` for CredDefs, Revocation Registry Definitions and Status List Entries **should remain the same** when each of these resources is part of the same AnonCred. This will make it easier for resources to query by `resourceName` and `resourceType` to delineate between the three resources using a common root. &#x20;

Using this logic, the following queries can be used to dereference to [CredDefs](creddef-object.md), [Revocation Registry Definitions](revocation-registry-definition-object.md) and [Status List Entries](revocation-registry-entry-object.md), in a way which can derive all three resources from the same root:

#### Dereference to CredDef

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsCredDef`

#### Dereference to Revocation Registry Definition

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsRevocRegDef`

#### Dereference to Revocation Status List Entry

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsStatusListEntry`

{% hint style="info" %}
**Note**: across all three of these queries, the resolver would fetch the latest version of the resource by default
{% endhint %}

## Constructing an AnonCred with this logic

The AnonCreds construction below uses this logic to demonstrate how an application could derive the latest [Status List Entry](revocation-registry-entry-object.md) using the "`rev_reg_id`" since it shares the same root and would only require replacing "anonCredsRevocRegDef" with "anonCredsStatusListEntry".

This is similar to how Hyperledger Indy uses composite strings to derive assoicated AnonCreds Objects from others. For example:

```json
{
    "schema_id": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
    "cred_def_id": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsCredDef",
    "rev_reg_id": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsRevocRegDef",
    "values": {...}
}
```

### Legacy AnonCreds Revocation Registry Definition Structure

<details>

<summary>Legacy AnonCreds Revocation Registry Definition Object</summary>

Like both the Legacy Schema Object ID and the Legacy CredDef Object ID, the Legacy Revocation Registry Definition Object ID was defined as a composite string of the following information:

1. `Publisher DID`: The DID of the creator of the revocation registry. Generally this will be the same publisher as the creator of the [CredDef Object](creddef-object.md).
2. `Object Type`: An integer denoting the type of object. `4` is used for Revocation Registry Objects.
3. `CredDef Object ID`: This is the [AnonCreds CredDef Object ID](creddef-object.md).
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
