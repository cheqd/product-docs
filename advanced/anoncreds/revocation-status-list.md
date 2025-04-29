---
description: cheqd support for Ledger-Agnostic AnonCreds Revocation Status List Objects
---

# Revocation Status Lists

## cheqd AnonCreds Object Method for Revocation Status List Objects

In the ledger-agnostic [AnonCreds](https://hyperledger.github.io/anoncreds-spec/) [specification](https://hyperledger.github.io/anoncreds-spec/), Status List Objects contain the state of the cryptographic accumulator and revocation indices at a given point in time. This enables:

1. Holders of Verifiable Credentials to generate a proof of non-revocation (or not) about their specific credential; and
2. Verifiers to be able to verify that proof.

A Status List is generated and published immediately on creation of the [Revocation Registry Definition Object](revocation-registry-definition.md) so that it can be used immediately by holders. Over time, additional Status Lists may be generated and published as the revocation status of one or more credentials within the Revocation Registry change.

In each of these subsequent Revocation Status List Objects, there is an updated **cryptographic accumulator** value AND an **updated list of revoked indices**, pointing to the Revocation Registry Definition Object and a location within a Tails File, associated with an index value.

This documentation will guide an implementor of AnonCreds on cheqd on how the cheqd AnonCreds Object Method defines and structures Status List IDs, Request Formats and Response Formats.

### AnonCreds Status List Objects

If you are not familiar with the latest Ledger-Agnostic AnonCreds Revocation Registry Definition structure, click the collapsible tile below to learn about the new format.

<details>

<summary>Ledger-Agnostic AnonCreds Revocation Status List Object Content</summary>

Each specific AnonCreds identifier must be defined within an AnonCreds Object Method in the [AnonCreds Object Method Registry](https://anoncreds-wg.github.io/anoncreds-spec/#anoncreds-objects-methods-registry)).

This means that an AnonCreds Revocation Status List Object ID does not need to be formatted in any particular syntax, in the latest version of the AnonCreds Specification.

The required content and data model for any AnonCreds Revocation Status List Object are as follows:

* `revRegDefId`: the identifier of the associated [Revocation Registry Definition](https://hyperledger.github.io/anoncreds-spec/#term:revocation-registry-definition). The format of the identifier is dependent on the [AnonCreds Objects Method](https://hyperledger.github.io/anoncreds-spec/#term:anoncreds-objects-method) used by the issuer.
* `revocationList`: Bit array defining the status of the credential in the \[ref: Revocation Registry]. A value of `1` means the credential is revoked, a value of `0` means the credential is not revoked.
* `currentAccumulator`: the calculated cryptographic accumulator reflecting the initial state of the [Revocation Registry](https://hyperledger.github.io/anoncreds-spec/#term:revocation-registry)
* `timestamp`: the timestamp at which the accumulator value is valid

For example:

```json
{
  "revRegDefId": "4xE68b6S5VRFrKMMG1U95M:4:4xE68b6S5VRFrKMMG1U95M:3:CL:59232:default:CL_ACCUM:4ae1cc6c-f6bd-486c-8057-88f2ce74e960",
  "revocationList": [0, 1, 1, 0],
  "currentAccumulator": "21 124C594B6B20E41B681E92B2C43FD165EA9E68BC3C9D63A82C8893124983CAE94 21 124C5341937827427B0A3A32113BD5E64FB7AB39BD3E5ABDD7970874501CA4897 6 5438CB6F442E2F807812FD9DC0C39AFF4A86B1E6766DBB5359E86A4D70401B0F 4 39D1CA5C4716FFC4FE0853C4FF7F081DFD8DF8D2C2CA79705211680AC77BF3A1 6 70504A5493F89C97C225B68310811A41AD9CD889301F238E93C95AD085E84191 4 39582252194D756D5D86D0EED02BF1B95CE12AED2FA5CD3C53260747D891993C",
  "timestamp": 1669640864487
}
```

Therefore, for each new Revocation Status List, the the `revocRegDefId` remains the same - the only value that changes is the `currentAccumulator,` the `revocationList` and the `timestamp` reflecting the delta between the previous and most recent Status Lists.

</details>

### cheqd Revocation Status List Object ID

cheqd uses [DID-Linked Resources](../../studio/did-linked-resources/understanding-dlrs/) to identify individual resources, associated with a DID, using fully resolvable DID URLs.

cheqd resources module uses the following format:

`did:cheqd:mainnet:<issuerDid>/resources/<statusListId>`

Rather than using a composite string for the Status List ID. The cheqd AnonCreds object method uses a UUID to identify the Revocation Status List Object.

For example, the following DID URL is cheqd's representation of a `statusListId`:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/9d26b902-555d-43bd-bac3-0bedeb462887`

Another supported format for a `statusListId` may be used in applications where it is important to derive the `credDefId`, `revocRegDefId` and `statusListId` from the same root.

This format uses query-based syntax, for example:

`did:cheqd:mainnet:<IssuerDid>?resourceName=<resourceName>&resourceType=<resourceType>`

For example:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsStatusList`

### Understanding Request vs Response formats

It is important to differentiate between the **Request format** for creating an AnonCreds object on cheqd, and the **Response format**, for how an AnonCreds objectshould be compiled by SDKs and the [cheqd DID Resolver](../../architecture/adr-list/adr-003-did-resolver.md).

The request format _**may**_ be specific to each AnonCreds Object Method. However, the response format _**should**_ be standardised to enable any AnonCreds supported application to understand the object, without needing custom or method-specific logic.

### cheqd Status List Request format

The cheqd AnonCreds Status List request format comprises of:

1. A Resource file for the Status List object content (e.g. `degreeStatusList.json`); and
2. A Payload file (including the signing keys and additional inputs to create a DID-Linked Resource).

Both of these inputs are required to provide the ledger enough information to:

1. Populate a [cheqd DID-Linked Resource](../../studio/did-linked-resources/understanding-dlrs/); and
2. Compile a standardised AnonCreds Revocation Registry Definition object in the [Response format](revocation-status-list.md#cheqd-status-list-response-format).

#### Status List Resource file

Before creating any on-ledger resource transaction, it is important to assemble the required Status List Content and save it as a file locally.

In the example below, the content should be saved as a file, for example: `degreeStatusList.json` with the following content:

```json
{
    "revRegDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
    "revocationList": [0, 1, 1, 0],
    "currentAccumulator": "21 124C594B6B20E41B681E92B2C43FD165EA9E68BC3C9D63A82C8893124983CAE94 21 124C5341937827427B0A3A32113BD5E64FB7AB39BD3E5ABDD7970874501CA4897 6 5438CB6F442E2F807812FD9DC0C39AFF4A86B1E6766DBB5359E86A4D70401B0F 4 39D1CA5C4716FFC4FE0853C4FF7F081DFD8DF8D2C2CA79705211680AC77BF3A1 6 70504A5493F89C97C225B68310811A41AD9CD889301F238E93C95AD085E84191 4 39582252194D756D5D86D0EED02BF1B95CE12AED2FA5CD3C53260747D891993C"
}
```

This Status List Resource file inputs should be replicated where possible within the Payload file, to populate a [DID-Linked resource](../../studio/did-linked-resources/understanding-dlrs/) stored on cheqd, with the following mapping:

| Resource file field | Resource file input  | Payload file field | Payload file input    |
| ------------------- | -------------------- | ------------------ | --------------------- |
| "type"              | "currentAccumulator" | "resourceType"     | "anonCredsStatusList" |

What this means is that if the **Resource file** has an object of **"type" = "currentAccumulator"** then this should be written as **"resourceType" = "anonCredsStatusList"** when creating the **Payload file**.

#### Status List Payload File

The **Payload file** utilises the inputs from the **Resource file** where possible, mapping common fields across. The **Payload file** may also require additional inputs to be provided by the creator to create a DID-Linked Resource for inputs not provided in the **Resource file**.

Below is an example of a Payload file:

```json
{
  "payload": {
    "collectionId": "zF7rhDBfUt9d1gJPjx7s1J",
    "id": "af20b1f0-5c4d-4037-9669-eaedddb9c2df",
    "name": "universityDegree-1.0",
    "version": "",
    "resourceType": "anonCredsStatusList",
    "alsoKnownAs": []
  },
  "signInputs": [
    {
      "verificationMethodID": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J#key1",
      "privKey": "y4B5qis9BXUq/mODsrWtS3q5ejOk/okSIXlX1/a9HvuG3PgYmekfQmq3QhJ4JSzN/rkiGCQDNKoTXMmxuXDHbg=="
    }
  ]
}
```

When passing the Payload file to the ledger, additional inputs may be required within the Payload file to populate the [DID-Linked Resource](../../studio/did-linked-resources/understanding-dlrs/). In this instance, the only additional information required is:

| Additional parameter | Expected input                                     | Rationale                                                                                                                                                                                                                                                                                                 |
| -------------------- | -------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "name"               | "\<insert same name as CredDef>-\<Revocation Tag>" | The Payload file drawing inputs from the Resource file on its own does not provide the ledger the requisite amount of information to create a full DID-Linked Resource. resourceName must be provided as an additional input parameter, as well as a revocation tag. There must be a hyphen between them. |

#### Publishing resource using CLI

For example, the full request format using a CLI should be structured as follows:

```bash
cheqd-noded tx resource create [payload.json] [degreeStatusList.json] \
  
  --chain-id cheqd \
  --keyring-backend test \
  --output json \
  --fees 10000000000ncheq \
  --gas auto \
  --gas-adjustment 1.8 \
  --from base_account \
```

### cheqd resource Metadata

Once you have created your Status List Object as a resource on cheqd, the following metadata will be generated in the DID Document Metadata associated with `did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J`

```json
"linkedResourceMetadata": [
  {
  "resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/9d26b902-555d-43bd-bac3-0bedeb462887",
  "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
  "resourceId": "9d26b902-555d-43bd-bac3-0bedeb462887",
  "resourceName": "universityDegree-1.0",
  "resourceType": "anonCredsStatusList",
  "mediaType": "application/json",
  "created": "2022-08-21T08:40:00Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": null,
  "nextVersionId": null
  }
]
```

{% hint style="info" %}
Next and Previous Status Lists will appear in the resource Metadata when a new Status List is made with the same `resourceName` and `resourceType`
{% endhint %}

### cheqd Status List Response format

Using the cheqd [Status List Request format](revocation-status-list.md#cheqd-status-list-request-format) and [associated resource metadata](revocation-status-list.md#cheqd-resource-metadata), the ledger has enough information to compile the following data structure as a response format.

This can either be compiled by the relevant SDK handling cheqd AnonCreds, or it can be assembled by the cheqd DID resolver.

```json
{
    "revRegDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
    "revocationList": [0, 1, 1, 0],
    "currentAccumulator": "21 124C594B6B20E41B681E92B2C43FD165EA9E68BC3C9D63A82C8893124983CAE94 21 124C5341937827427B0A3A32113BD5E64FB7AB39BD3E5ABDD7970874501CA4897 6 5438CB6F442E2F807812FD9DC0C39AFF4A86B1E6766DBB5359E86A4D70401B0F 4 39D1CA5C4716FFC4FE0853C4FF7F081DFD8DF8D2C2CA79705211680AC77BF3A1 6 70504A5493F89C97C225B68310811A41AD9CD889301F238E93C95AD085E84191 4 39582252194D756D5D86D0EED02BF1B95CE12AED2FA5CD3C53260747D891993C",
    "timestamp": "2022-08-21T08:40:00Z"
}
```

#### Compiling Response format in cheqd DID Resolver

The cheqd DID resolver will use the following logic to compile the standardised response format:

{% hint style="info" %}
_If "**resourceType=anonCredsStatusList**" then **append "created" into a field called "timestamp"** to the end of the Response Format for the resource presented_
{% endhint %}

### Linking Status Lists

To Create a Status List as a **new version** of a previous Status List, you need to create a new resource.

You must:

1. Generate a new UUID for the `resourceId`
2. Specify the same `collectionId`
3. Specify the same `resourceName`
4. Specify the same `resourceType`
5. Attach to the transaction the new `resourceFile` with the latest `accum` value and `index` values.

Once the transaction has been created, the `resourceMetadata` will look like the following:

```json
"linkedResourceMetadata": [
  {
  "resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/c154bc07-43f7-4b69-ac0c-5514001f2ca3",
  "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
  "resourceId": "513d8a44-6188-41c2-8de8-eda562f82947",
  "resourceName": "universityDegree-1.0",
  "resourceType": "anonCredsStatusList",
  "mediaType": "application/json",
  "created": "2022-09-01T04:30:01Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": "c154bc07-43f7-4b69-ac0c-5514001f2ca3",
  // points to previous Status List 
  "nextVersionId": null
  }
]
```

{% hint style="info" %}
Note: The previousVersionId will now link to the previous Revocation Status List ID
{% endhint %}

## Tying CredDef, RevRegDef and StatusList Objects together

Across the [cheqd CredDef Object Method](credential-definition.md#cheqd-anoncreds-object-method-for-creddefs), the [Revocation Registry Definition Object Method](revocation-registry-definition.md) and the [StatusList Object Method](revocation-status-list.md) - each resource is associated with the same issuer DID and Collection ID.

Importantly, this allows each new resource to be indexed and versioned by their:

1. `resourceName`
2. `resourceType`

New resources can be created to update the existing CredDef or RevRegDef, whilst maintaining the historical state of the previous versions. See the documentation on [Publishing a New Version of a Resource](../tooling/cheqd-node-cli/add-more-resources.md) to understand this further.

### Fetching a cheqd Status List Object

Existing DID Resolvers will be able to query for the Status List Object Content using the [same patterns and parameters as the Schema Object found here](schema.md#fetching-a-cheqd-resource).

The cheqd AnonCreds method also enables applications to derive the [CredDef](credential-definition.md), [Revocation Registry Definition Object](revocation-registry-definition.md) and [Status Lists](revocation-status-list.md) from the same root:

#### Same Resource Name, different Resource type

We propose that the `resourceName` for CredDefs, Revocation Registry Definitions and Revocation Status Lists **should remain the same** when each of these resources is part of the same AnonCred. This will make it easier for resources to query by `resourceName` and `resourceType` to delineate between the three resources using a common root.

Using this logic, the following queries can be used to dereference to [CredDefs](credential-definition.md), [Revocation Registry Definitions](revocation-registry-definition.md) and [Status Lists](revocation-status-list.md), in a way which can derive all three resources from the same root:

#### Dereference to CredDef

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsCredDef`

#### Dereference to Revocation Registry Definition

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsRevocRegDef`

#### Dereference to Revocation Status List

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsStatusList`

{% hint style="info" %}
**Note**: across all three of these queries, the resolver would fetch the latest version of the resource by default
{% endhint %}

### Traversing Status Lists Versions using a DID Resolver

Using existing DID Resolvers, it is possible to traverse the history of Status List versions in order to produce proofs of non-revocation required in the [AnonCreds Specification](https://anoncreds-wg.github.io/anoncreds-spec).

#### Obtain all Status List Versions

A DID URL such as the following will display all of the accumulators associated with a particular Status List:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsStatusList&allResourceVersions=true`

#### Obtain Status List Content at a point in time

Furthermore, it will be possible to query Status Lists at certain times. This may be very useful if you want to prove whether a Verifiable Credential was valid in the past:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?universityDegree&resourceType=anonCredsStatusList&versionTime=2022-08-21T08:40:00Z`

#### Obtain latest Status List Version

It will be very common for a proof of non-revocation to require the latest Status List and work its way back from there.

The following DID URL will be able to call the latest Status List:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?universityDegree&resourceType=anonCredsStatusList`

### Constructing an AnonCred with this logic

The AnonCreds construction below uses this logic to demonstrate how an application could derive the latest [Status List](revocation-status-list.md) using the "`rev_reg_id`" since it shares the same root and would only require replacing "anonCredsRevocRegDef" with "anonCredsStatusList".

This is similar to how Hyperledger Indy uses composite strings to derive assoicated AnonCreds Objects from others. For example:

```json
{
    "schema_id": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
    "cred_def_id": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsCredDef",
    "rev_reg_id": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsRevocRegDef",
    "values": {...}
}
```

### Legacy AnonCreds Revocation Registry Structure

<details>

<summary>Legacy AnonCreds Revocation Registry Entry Object</summary>

The Legacy AnonCreds Revocation Registry Entry ID was very similar in composition to the [Revocation Registry Definition Object](https://anoncreds-wg.github.io/anoncreds-spec/#anoncreds-objects-methods-registry).

The only difference is that the Revocation Registry Entry ID includes the Revocation Registry ID `objectType`, which is "`5`".

The structure of the Legacy AnonCreds Revocation Registry Entry ID is as follows:

```bash
<objectType><RevocRegDefId>
```

For example:

```bash
5:zF7rhDBfUt9d1gJPjx7s1J:4:zF7rhDBfUt9d1gJPjx7s1J:3:CL:7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7:credDefDegree:CL_ACCUM:degreeCredRevRegDef
```

Important: Each AnonCreds Revocation Registry Entry has the **same ID** for a given Revocation Registry Definition Object.

This is important to mention, since many client applications may still expect RevReg IDs or RevReg Content to contain the information or specific syntax of this Legacy `revocRegId.` This legacy format is now attributed to the [Hyperledger Indy Legacy AnonCreds Objects Method](https://hyperledger.github.io/anoncreds-methods-registry/#hyperledger-indy-legacy-anoncreds-method).

</details>
