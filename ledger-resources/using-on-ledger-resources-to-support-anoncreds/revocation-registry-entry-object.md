# Revocation Registry Entry Object

## Overview

In the AnonCreds Specification, Revocation Registry Entry Objects contain the state of the Revocation Registry at a given point in time. This enables:

1. Holders of Verifiable Credentials to generate a proof of non-revocation (or not) about their specific credential; and
2. Verifiers to be able to verify that proof.&#x20;

An initial Revocation Registry Entry is generated and published immediately on creation of the [Revocation Registry Definition Object](revocation-registry-definition-object.md) so that it can be used immediately by holders. Over time, additional Revocation Registry Entry Objects are generated and published as the revocation status of one or more credentials within the Revocation Registry change.

### AnonCreds Revocation Registry Entry ID

The AnonCreds Revocation Registry Entry ID is very similar in composition to the [Revocation Registry Definition Object](revocation-registry-definition-object.md).

The only difference is that the Revocation Registry Entry ID includes the Revocation Registry Entry ID `objectType`, which is "`5`".

The structure of the AnonCreds Revocation Registry Entry ID is as follows:

```
<objectType><RevocRegDefId>
```

For example:

```
5:zF7rhDBfUt9d1gJPjx7s1J:4:zF7rhDBfUt9d1gJPjx7s1J:3:CL:7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7:credDefDegree:CL_ACCUM:degreeCredRevRegDef
```

{% hint style="warning" %}
Important: Each AnonCreds Revocation Registry Entry has the **same ID** for a given Revocation Registry Definition.&#x20;
{% endhint %}

### AnonCreds Revocation Registry Entry Content

The required content and data model for the AnonCreds Revocation Registry Entry Object  are as follows:

1. `revocDefType`: as [defined here](revocation-registry-definition-object.md#anoncreds-revocation-registry-definition-object-id)
2. `revocRegDefId`: as [defined here](revocation-registry-definition-object.md#anoncreds-revocation-registry-definition-object-id)
3. `accum`:  the calculated cryptographic accumulator reflecting the initial state of the Revocation Registry Definition.&#x20;

For example:

```json
{
  "revocDefType": "CL_ACCUM",
  "revocRegDefId": "Gs6cQcvrtWoZKsbBhD3dQJ:4:Gs6cQcvrtWoZKsbBhD3dQJ:3:CL:140389:mctc:CL_ACCUM:1-1024",
  "value": {
    "accum": "21 10B...33D"
  }
}
```

Therefore, for each new Revocation Registry Entry, the 'ID' remains the same, as does the `revocRegType` and `revocRegDefId` - the only value that changes is the `accum,` reflecting the delta between the previous and most recent Revocation Registry Entries. ``&#x20;

## cheqd AnonCreds Object Method for Revocation Registry Definition Objects

### cheqd Revocation Registry Entry Object ID

cheqd [on-ledger resources](../../resources/) identify individual resources using DID URLs.&#x20;

cheqd resources module uses the following format:

`did:cheqd:mainnet:<issuerDid>/resources/<revRegRevEntryId>`

Rather than using a composite string for the Revocation Registry Entry Resource ID. The cheqd AnonCreds object method uses a UUID to identify the Revocation Registry Entry Object Content which includes additional Revocation Registry Entry Object Content Metadata, providing the required fields for equivalence with Hyperledger Indy implementations.&#x20;

For example, the following DID URL is cheqd's representation of a `revocRegRevEntryId`:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/9d26b902-555d-43bd-bac3-0bedeb462887`

{% hint style="warning" %}
Important: Unlike in Hyperledger Indy implementations, cheqd uses a **unique identifier for each specific Revocation Registry Entry ID**. This enables better indexing and searchability.&#x20;
{% endhint %}

### cheqd Revocation Registry Entry Object Content

Before creating any on-ledger resource transaction, it is important to assemble to required Revocation Registry Entry Object Content and save it as a file locally.&#x20;

cheqd's approach to AnonCreds Revocation Entry Objects implements the following logic:

1. The required (ledger-agnostic) content of the Revocation Registry Entry Object Content should be included in the body of the content.
2. Anything that is network-specific should be included within AnonCreds Object Metadata.&#x20;

In the example below, the content should be saved as a file, for example: `degreeCredRevocRegEntry1.json` with the following content:

```json
{
"AnonCredsRevRegEntry: {
  "revocDefType": "CL_ACCUM",
  "revocRegDefId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
  "value": {
    "accum": "21 10B...33D"
  }
"AnonCredsObjectMetadata: {
  "objectFamily": "anoncreds",
  "objectFamilyVersion": "v2",
  "objectType": "5",  
  "issuerDid": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J",      
  "objectUri": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/9d26b902-555d-43bd-bac3-0bedeb462887"
  }
}
```

This implementation uses AnonCredsObjectMetadata to provide equivalency between cheqd's AnonCreds Object Method and other AnonCreds Object Methods, including the fields, where:

| Object Metadata field | Response                                                                        | Method Specification / Equivalency |
| --------------------- | ------------------------------------------------------------------------------- | ---------------------------------- |
| objectFamily          | anoncreds                                                                       | did:indy Objects Method            |
| objectFamilyVersion   | v2                                                                              | did:indy Objects Method            |
| objectType            | 5                                                                               | Legacy Indy Objects Method         |
| issuerDid             | Fully qualified DID to easily identify the publisher of the Revocation Registry | cheqd Objects Method               |
| objectUri             | Fully qualified DID URL to easily access the Revocation Registry Entry          | cheqd Objects Method               |

### create Revocation Registry Definition transaction

To create a Revocation Registry Entry on cheqd, you need to carry out a resource transaction, specifying the following information.&#x20;

```
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

```
"resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/af20b1f0-5c4d-4037-9669-eaedddb9c2df",
    "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
    "resourceId": "9d26b902-555d-43bd-bac3-0bedeb462887",
    "resourceName": "degreeCredRevocRegEntry",
    "resourceType": "CL_ACCUM",
    "mediaType": "application/json",
    "created": "2022-08-21T08:40:00Z",
    "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
    "previousVersionId": null,
    "nextVersionId": null
```

{% hint style="info" %}
Next and Previous Revocation Registry Entries will appear in the resource Metadata when a new Revocation Registry Entry is made with the same resourceName and resourceType
{% endhint %}

### Traversing Revocation Registry Entries using a DID Resolver

#### Obtain all Revocation Registry Entry Content

Using existing DID Resolvers, it is made possible to traverse the history of Revocation Registry Entries to query deltas in the cryptographic accumulator in order to produce a proof of non-revocation.&#x20;

A DID URL such as the following will display all of the accumulators associated with a particular Revocation Registry:

&#x20;`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resouceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&all`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resouceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&all`

{% hint style="info" %}
It is recommended that applications using this revocation method implement a way of caching a historical list of accumulators and deltas to prevent the application from having to fetch the entire list of Revocation Registry Entries every time a proof of non-revocation is required.
{% endhint %}

#### Obtain Revocation Registry Entry Content at a point in time

Furthermore, it will be possible to query Revocation Entries at certain times. This may be very useful if you want to prove whether a Verifiable Credential was valid in the past.&#x20;

For example:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resouceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&versionAt=2022-08-21T08:40:00Z`

#### Obtain latest Revocation Registry Entry

It will be very common for a proof of non-revocation to require the latest Revocation Registry Entry and work its way back from there.&#x20;

The following DID URL will be able to call the latest Revocation Registry Entry

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resouceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&latest`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resouceName=degreeCredRevocRegEntry&resourceType=CL_ACCUM&latest`
