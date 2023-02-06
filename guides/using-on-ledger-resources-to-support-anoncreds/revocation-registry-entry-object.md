---
description: cheqd support for Ledger-Agnostic AnonCreds Revocation Status List Objects
---

# Revocation Status List Object

## Overview

In the ledger-agnostic [AnonCreds](https://hyperledger.github.io/anoncreds-spec/)[ specification](https://hyperledger.github.io/anoncreds-spec/), Revocation Status List Objects contain the state of the cryptographic accumulator and revocation indices at a given point in time. This enables:

1. Holders of Verifiable Credentials to generate a proof of non-revocation (or not) about their specific credential; and
2. Verifiers to be able to verify that proof.

An initial Revocation Status List Entry is generated and published immediately on creation of the [Revocation Registry Definition Object](revocation-registry-definition-object.md) so that it can be used immediately by holders. Over time, additional Revocation Status List Entries may be generated and published as the revocation status of one or more credentials within the Revocation Registry change.

In each of these subsequent Revocation Status List Objects, there is an updated **cryptographic accumulator** value AND an **updated list of revoked indices**, pointing to the Revocation Registry Definition Object and a location within a Tails File, associated with an index value.

This documentation will guide an implementor of AnonCreds on cheqd on how the cheqd AnonCreds Object Method defines and structures Revocation Registry Entry IDs and associated content.

### AnonCreds Revocation Registry Entry ID

Each specific AnonCreds identifier must be defined within an AnonCreds Object Method in the [AnonCreds Object Method Registry](https://anoncreds-wg.github.io/anoncreds-spec/#anoncreds-objects-methods-registry)).

This means that an AnonCreds Revocation Status List Object ID does not need to be formatted in any particular syntax, in the latest version of the AnonCreds Specification.

{% hint style="info" %}
See the collapsible tile below to learn about how the Ledger-Agnostic AnonCreds specification handles these objects.
{% endhint %}

<details>

<summary>Ledger-Agnostic AnonCreds Revocation Status List Object Content</summary>

The required content and data model for any AnonCreds Revocation Status List Entry Object are as follows:

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

Therefore, for each new Revocation Status List entry, the the `revocRegDefId` remains the same - the only value that changes is the `currentAccumulator,` the `revocationList` and the `timestamp` reflecting the delta between the previous and most recent Revocation Registry Entries.

</details>

## cheqd AnonCreds Object Method for Revocation Status List Objects and Entries

### cheqd Revocation Status List Object ID

cheqd [DID-Linked Resources](https://docs.cheqd.io/identity/guides/did-linked-resources) identify individual resources using DID URLs.

cheqd resources module uses the following format:

`did:cheqd:mainnet:<issuerDid>/resources/<revRegStatusId>`

Rather than using a composite string for the Revocation Status List Object ID. The cheqd AnonCreds object method uses a UUID to identify the Revocation Status List Object Content which includes additional Revocation Status List Object Content Metadata, providing the required fields for equivalence with Hyperledger Indy implementations.

For example, the following DID URL is cheqd's representation of a `revocRegStatusList`:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/9d26b902-555d-43bd-bac3-0bedeb462887`

> Unlike in Hyperledger Indy implementations, cheqd uses a **unique identifier for each specific Revocation Registry Entry ID**. This enables better indexing and searchability. For conformance with the cheqd AnonCreds Object Method, each Revocation Registry Status List Object **MUST** be created in the **same Collection** as the Revocation Registry Definition Object.

### cheqd Revocation Status List Entry Object Content

Before creating any on-ledger resource transaction, it is important to assemble to required Revocation Status List Object Content and save it as a file locally.

cheqd's approach to AnonCreds Revocation Entry Objects implements the following logic:

1. The required (ledger-agnostic) content of the Revocation Status List Object Content should be included in the body of the content.
2. Anything that is network-specific should be included within AnonCreds Object Metadata.

In the example below, the **first entry** in a Revocation Registry should be saved as a file, for example: `degreeCredRevocStatusList.json` with the following content:

```json
{
  "AnonCredsObject": {
    "revRegDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
    "revocationList": [0, 1, 1, 0],
    "currentAccumulator": "21 124C594B6B20E41B681E92B2C43FD165EA9E68BC3C9D63A82C8893124983CAE94 21 124C5341937827427B0A3A32113BD5E64FB7AB39BD3E5ABDD7970874501CA4897 6 5438CB6F442E2F807812FD9DC0C39AFF4A86B1E6766DBB5359E86A4D70401B0F 4 39D1CA5C4716FFC4FE0853C4FF7F081DFD8DF8D2C2CA79705211680AC77BF3A1 6 70504A5493F89C97C225B68310811A41AD9CD889301F238E93C95AD085E84191 4 39582252194D756D5D86D0EED02BF1B95CE12AED2FA5CD3C53260747D891993C",
    "timestamp": 1669640864487
  },
  "AnonCredsObjectMetadata": {
    "objectFamily": "anoncreds",
    "objectFamilyVersion": "v1",
    "objectType": "5",
    "issuerId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J",
    "objectUri": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/9d26b902-555d-43bd-bac3-0bedeb462887"
  }
}
```

The **subsequent entries** in the Revocation Status List should be formatted in the same way, containing updated indices of the revoked and un-revoked credentials in the Tails File, as well as the latest accumulator. For example:

```json
{
  "AnonCredsObject": {
    "revRegDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
    "revocationList": [0, 1, 1, 0, 1, 1, 1],
    "currentAccumulator": "21 116...567",
    "timestamp": 1769640863481
  },
  "AnonCredsObjectMetadata": {
    "objectFamily": "anoncreds",
    "objectFamilyVersion": "v1",
    "objectType": "5",
    "issuerId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J",
    "objectUri": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/c154bc07-43f7-4b69-ac0c-5514001f2ca3"
  }
}
```

{% hint style="info" %}
Note: For implementating AnonCreds Revocation Status List Entries on cheqd you can include **all revocation indices within each latest entry.** This is different to Hyperledger Indy, however, it makes the non-revocation proof far more simple, since only the latest entry is needed, rather than all the historic entries.
{% endhint %}

This implementation also uses AnonCredsObjectMetadata to provide equivalency between cheqd's AnonCreds Object Method and other AnonCreds Object Methods, including the fields, where:

| Object Metadata field | Response                                                                        | Method Specification / Equivalency      |
| --------------------- | ------------------------------------------------------------------------------- | --------------------------------------- |
| objectFamily          | anoncreds                                                                       | did:indy Objects Method                 |
| objectFamilyVersion   | v1                                                                              | did:indy Objects Method                 |
| objectType            | 5                                                                               | Legacy Hyperledger Indy Objects Method  |
| typeName              | `CL_ACCUM`                                                                      | Legacy Hyperledeger Indy Objects Method |
| issuerId              | Fully qualified DID to easily identify the publisher of the Revocation Registry | cheqd Objects Method                    |
| objectUri             | Fully qualified DID URL to easily access the Revocation Registry Entry          | cheqd Objects Method                    |

<details>

<summary>Legacy AnonCreds Revocation Registry Entry ID</summary>

The Legacy AnonCreds Revocation Registry Entry ID was very similar in composition to the [Revocation Registry Definition Object](https://anoncreds-wg.github.io/anoncreds-spec/#anoncreds-objects-methods-registry).

The only difference is that the Revocation Registry Entry ID includes the Revocation Registry Entry ID `objectType`, which is "`5`".

The structure of the Legacy AnonCreds Revocation Registry Entry ID is as follows:

```bash
<objectType><RevocRegDefId>
```

For example:

```bash
5:zF7rhDBfUt9d1gJPjx7s1J:4:zF7rhDBfUt9d1gJPjx7s1J:3:CL:7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7:credDefDegree:CL_ACCUM:degreeCredRevRegDef
```

Important: Each AnonCreds Revocation Registry Entry has the **same ID** for a given Revocation Registry Definition Object.

This is important to mention, since many client applications may still expect RevRegEntry IDs or RevRegEntry Content to contain the information or specific syntax of this Legacy `revocRegEntryId.` This legacy format is now attributed to the Hyperledger Indy Legacy AnonCreds Objects Method

</details>

### cheqd resource Metadata

Once you have created your Revocation Registry Entry as a resource on cheqd, the following metadata will be generated in the DID Document Metadata associated with `did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J`

```json
"linkedResourceMetadata": [
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
]
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
5. Attach to the transaction the new `resourceFile` with the latest `accum` value and `index` values.

For example:

```json
{
    "kms": "local",
    "payload": {
        "collectionId": "zF7rhDBfUt9d1gJPjx7s1J",
        "id": "513d8a44-6188-41c2-8de8-eda562f82947",
        "name": "degreeCredRevocRegEntry",
        "resourceType": "CL_ACCUM",
        "data": "SGVsbG8sIHdvcmxk"
    },
    "signInputs": [{
        "verificationMethodId": "did:cheqd:testnet:zF7rhDBfUt9d1gJPjx7s1J#key-1",
        "keyType": "Ed25519",
        "privateKeyHex": "0f ... 38"
    }]
}
```

where the Base 64 encoded data decodes to:

```json
{
  "AnonCredsObject": {
    "revRegDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
    "revocationList": [0, 1, 1, 0, 1, 1, 1, 0],
    "currentAccumulator": "21 916...727",
    "timestamp": 1989540864487
  },
  "AnonCredsObjectMetadata": {
    "objectFamily": "anoncreds",
    "objectFamilyVersion": "v1",
    "objectType": "5",
    "issuerId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J",
    "objectUri": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/513d8a44-6188-41c2-8de8-eda562f82947"
  }
}
```

Once the transaction has been created, the `resourceMetadata` will look like the following:

```json
"linkedResourceMetadata": [
  {
  "resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/c154bc07-43f7-4b69-ac0c-5514001f2ca3",
  "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
  "resourceId": "513d8a44-6188-41c2-8de8-eda562f82947",
  "resourceName": "degreeCredRevocRegEntry",
  "resourceType": "CL_ACCUM",
  "mediaType": "application/json",
  "created": "2022-09-01T04:30:01Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": "c154bc07-43f7-4b69-ac0c-5514001f2ca3",
  // points to previous Revocation Registry Entry
  "nextVersionId": null
  }
]
```

{% hint style="info" %}
Note: The previousVersionId will now link to the previous Revocation Status List Entry ID
{% endhint %}

### create Revocation Status List Objects

To create a Revocation Status List Objects on cheqd, you should follow the [tutorials for creating a DID-Linked Resource here](../../tutorials/on-ledger-resources/), and pass the relevant JSON file for the object in the transaction.&#x20;

### Traversing Revocation Registry Entries using a DID Resolver

Using existing DID Resolvers, it is possible to traverse the history of Revocation Registry Entries to query deltas in the cryptographic accumulator in order to produce proofs of non-revocation required in the [AnonCreds Specification](https://anoncreds-wg.github.io/anoncreds-spec).

Common and standardized `resource` parameters:

| Parameter                 | Type                                                          | Example                                                                                                                                                                                                                                                                                                                                                             |
| ------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"resourceId"`            | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                                      |
| `"resourceName"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeCredRevocRegEntry                                                                                                                                                                                                                                                                                 |
| `"resourceType"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeCredRevocRegEntry\&resourceType=CL\_ACCUM                                                                                                                                                                                                                                                         |
| `"resourceVersionId"`     | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeCredRevocRegEntry\&resourceVersionId=0.0.2                                                                                                                                                                                                                                                        |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeCredRevocRegEntry\&resourceType=CL\_ACCUM\&versionTime=2015-03-11T05:30:02Z                                                                                                                                                                                                                       |
| `"versionId"`             | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?versionId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                                       |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeCredRevocRegEntry\&resourceType=CL\_ACCUM\&versionTime=2018-07-19T08:40:00Z                                                                                                                                                                                                                       |
| `"linkedResource"`        | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?linkedResource=true // _note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document._                                                                                                                                                                          |
| `"resourceMetadata"`      | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeCredRevocRegEntry\&resourceType=CL\_ACCUM\&versionTime=2018-07-19T08:40:00Z\&resourceMetadata=true or, did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceMetadata=true // note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document. |
| "`latestResourceVersion`" | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeCredRevocRegEntry\&resourceType=CL\_ACCUM\&latestResourceVersion=true                                                                                                                                                                                                                             |
| "`allResourceVersions`"   | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=degreeCredRevocRegEntry\&resourceType=CL\_ACCUM\&allResourceVersions=true                                                                                                                                                                                                                               |

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
