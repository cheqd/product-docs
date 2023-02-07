---
description: cheqd support for Ledger-Agnostic AnonCreds CredDefs
---

# CredDef Object

## Overview

In the ledger-agnostic [AnonCreds](https://hyperledger.github.io/anoncreds-spec/)[ specification](https://hyperledger.github.io/anoncreds-spec/), Credential Definitions are used to specify the following information all in one place, to create an immutable record of:

1. The DID of the credential issuer
2. The schema the issued credentials will be based upon
3. The public/private key pairs that will be used to sign the claims within an issued credential
4. A cryptographic secret, embedded within the CredDef Object content, creating an uncorrelatable 'link secret' between the issuer and holder
5. Information necessary for the revocation of credentials, if revocation is to be enabled by the Issuer for this type of credential (Optional).

This documentation will guide an implementor of AnonCreds on cheqd on how the cheqd AnonCreds Object Method defines and structures CredDef IDs and associated content, with and without revocation enabled.

### AnonCreds CredDef Object ID

Each specific AnonCreds identifier must be defined within an AnonCreds Object Method in the [AnonCreds Object Method Registry](https://hyperledger.github.io/anoncreds-spec/).

This means that an AnonCreds CredDef Object ID does not need to be formatted in any particular syntax, in the latest version of the AnonCreds Specification.

{% hint style="info" %}
See the collapsible tile below to learn about how the Ledger-Agnostic AnonCreds specification handles these objects.
{% endhint %}

<details>

<summary>Ledger-Agnostic AnonCreds CredDef Object Content</summary>

Credential Definition Object Content is distinct in the way it is structured. The inputs and outputs generated when creating a CredDef are as follows:

#### Create CredDef input

The following inputs are required to create a CredDef:

* A schema on which the CredDef will be based
* A `tag`, an arbitrary string defined by the Issuer, enabling an Issuer to create multiple CredDefs for the same schema.
* An optional flag `support_revocation` (default `false`) which if true generates some additional data in the CredDef to enable credential revocation.

The operation produces two objects, as follows.

* The [Private Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:private-credential-definition), an internally managed object that includes the private keys generated for the [Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:credential-definition) and stored securely by the issuer.
* The [Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:credential-definition), that includes the public keys generated for the [Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:credential-definition), returned to the calling function and then published on a VDR.

#### Create CredDef output (private)

```json
{
  "p_key": {
    "p": "123...782",
    "q": "234...456"
  },
  "r_key": {
    "x": "332...566",
    "sk": "992...237"
  }
}
```

Where:

* `r_key` is an object defining the private key for the CKS revocation scheme.
  * `x` is a Big (128-bit?) integer selected at random from the the group of integers defined by the order of the bilinear groups `q`
  * `sk` is a Big (128-bit?) integer selected at random from the the group of integers defined by the order of the bilinear groups `q`

`x` and `sk` are used as part of the revocation public key generation as defined below.

#### Create CredDef output (public)

The output of a create CredDef transaction is a JSON structure that is generated using cryptographic primitives.

A CredDef has a couple of important characteristics:

1. Each of the schema attributes is individually signed using a public key fragment.
2. Each CredDef includes a `master_secret` attribute. This value is blindly contributed to the credential during issuance and used to bind the issued credential to the entity it was issued to.
3. The output includes the:
   1. `issuerID`
   2. `schemaID`
   3. `type`
   4. `tag`

Further details regarding the use of cryptographic primatives are described in the [CredDefs section of the AnonCreds specification here](https://anoncreds-wg.github.io/anoncreds-spec/#generating-a-cred\_def-without-revocation-support).

An example of a CredDef Object content without revocation is below:

```json
{
  "issuerId": "did:indy:sovrin:SGrjRL82Y9ZZbzhUDXokvQ",
  "schemaId": "did:indy:sovrin:SGrjRL82Y9ZZbzhUDXokvQ/anoncreds/v0/SCHEMA/MemberPass/1.0",
  "type": "CL",
  "tag": "latest",
  "value": {
    "primary": {
      "n": "779...397",
      "r": {
        "birthdate": "294...298",
        "birthlocation": "533...284",
        "citizenship": "894...102",
        "expiry_date": "650...011",
        "facephoto": "870...274",
        "firstname": "656...226",
        "link_secret": "521...922",
        "name": "410...200",
        "uuid": "226...757"
      },
      "rctxt": "774...977",
      "s": "750..893",
      "z": "632...005"
    }
  }
}
```

Using the Legacy AnonCreds Object method as a reference point, the composite string of the `cred_def_id` should include the **same values** as are in the CredDef Object Content.

An example of a CredDef Object content with revocation is below:

```json
{
  "issuerId": "did:indy:sovrin:F72i3Y3Q4i466efjYJYCHM",
  "schemaId": "did:indy:sovrin:F72i3Y3Q4i466efjYJYCHM/anoncreds/v0/SCHEMA/state_license/4.2.0",
  "type": "CL",
  "tag": "latest",
  "value": {
    "primary": {...},
    "revocation": {
      "g": "1 154...813 1 11C...D0D 2 095..8A8",
      "g_dash": "1 1F0...000",
      "h": "1 131...8A8",
      "h0": "1 1AF...8A8",
      "h1": "1 242...8A8",
      "h2": "1 072...8A8",
      "h_cap": "1 196...000",
      "htilde": "1 1D5...8A8",
      "pk": "1 0E7...8A8",
      "u": "1 18E...000",
      "y": "1 068...000"
    }
  }
}
```

</details>

## cheqd AnonCreds Object Method for CredDefs

### cheqd CredDef ID

cheqd [DID-Linked Resources](../did-linked-resources/README.md) identify individual resources using DID URLs.

cheqd resources module uses the following format:

`did:cheqd:mainnet:<IssuerDid>/resources/<CredDefResourceId>`

Rather than using a composite string for the CredDef Resource ID. The cheqd AnonCreds object method uses a UUID to identify the CredDef Object Content which includes additional CredDef Object Content Metadata, providing the required fields for equivalence with Hyperledger Indy implementations.

For example, the following DID URL is cheqd's representation of a `cred_def_id`:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0`

### cheqd CredDef Object Content

Before creating any on-ledger resource transaction, it is important to assemble to required CredDef Object Content and save it as a file locally.

cheqd's approach to AnonCreds CredDefs implements the following logic:

1. The required (network-agnostic) content of the CredDef should be included in the body of the content.
2. Anything that is network-specific should be included within AnonCreds Object Metadata.

In the example below, the content should be saved as a file, for example: `credDefDegree.json` with the following content (without revocation):

```json
{
"AnonCredsObject: {
  "schemaId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
  "type": "CL",
  "tag": "credDefDegree",
  "value": {
    "primary": {
      "n": "779...397",
      "r": {
        "birthdate": "294...298",
        "birthlocation": "533...284",
        "citizenship": "894...102",
        "expiry_date": "650...011",
        "facephoto": "870...274",
        "firstname": "656...226",
        "link_secret": "521...922",
        "name": "410...200",
        "uuid": "226...757"
      },
      "rctxt": "774...977",
      "s": "750..893",
      "z": "632...005"
    }
  }
}
"AnonCredsObjectMetadata": {
  "objectFamily": "anoncreds",
  "objectFamilyVersion": "v1",
  "objectType": "3",
  "typeName": "CLAIM_DEF",
  "objectUri": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0"
  }
}
```

Or with revocation:

```json
{
"AnonCredsObject: {
  "schemaId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
  "type": "CL",
  "tag": "credDefDegree",
  "value": {
    "primary": {...},
    "revocation": {
      "g": "1 154...813 1 11C...D0D 2 095..8A8",
      "g_dash": "1 1F0...000",
      "h": "1 131...8A8",
      "h0": "1 1AF...8A8",
      "h1": "1 242...8A8",
      "h2": "1 072...8A8",
      "h_cap": "1 196...000",
      "htilde": "1 1D5...8A8",
      "pk": "1 0E7...8A8",
      "u": "1 18E...000",
      "y": "1 068...000"
    }
  }
},
"AnonCredsObjectMetadata": {
  "objectFamily": "anoncreds",
  "objectFamilyVersion": "v1",
  "objectType": "3",
  "typeName": "CLAIM_DEF",
  "objectUri": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0"
  }
}
```

This implementation uses AnonCredsObjectMetadata to provide equivalency between cheqd's AnonCreds Object Method and other AnonCreds Object Methods, including the fields, where:

| Object Metadata field | Response                                                    | Method Specification / Equivalency     |
| --------------------- | ----------------------------------------------------------- | -------------------------------------- |
| objectFamily          | anoncreds                                                   | did:indy Objects Method                |
| objectFamilyVersion   | v1                                                          | did:indy Objects Method                |
| objectType            | 3                                                           | Legacy Hyperledger Indy Objects Method |
| typeName              | `CLAIM_DEF`                                                 | Legacy Hyperledger Indy Objects Method |
| objectUri             | Fully qualified DID URL to easily access the CredDef Object | cheqd Objects Method                   |

{% hint style="info" %}
Note: The cheqd ledger will not provide any checks on the Schema Object Content or Metadata. Therefore, it is the responsibility of the Schema creator to make sure that the `name,` `version` and AnonCredsObjectMetadata are correct.
{% endhint %}



<details>

<summary>Legacy AnonCreds CredDef Object ID</summary>

Like with [Schema Objects](schema-object.md), each CredDef Object ID (`cred_def_id`) was previously a **composite** string, consisting of the following elements:

* `issuer DID`: The DID of the Issuer, the issuer of the credentials that will utilise the CredDef.
* `object type`: The type of object. `3` is used for CredDefs.
* `signature_type`: The `signature_type` item for the CredDef (this is 'CL' in the [AnonCreds Specification](https://anoncreds-wg.github.io/anoncreds-spec/))
* `schema_id`: Also referred to as the `ref,` this should be a [URL](https://www.rfc-editor.org/rfc/rfc1738) for the `schema_id.`
* `tag`: A unique name or tag given to the CredDef.

Note: the schema\_id as part of the\_ cred\_def\_id is a recent change from the AnonCreds Working Group. Previously, this was the Schema TXN ID. However, the Schema TXN ID could not accommodate for non-Hyperledger Indy networks.

The `cred_def_id` therefore was formatted in the following way:

```bash
<issuerDid>:<objectType>:<signatureType>:<schemaId>:<tag>
```

For example a Legacy AnonCreds `cred_def_id` could be:

```bash
zF7rhDBfUt9d1gJPjx7s1J:3:CL:7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7:credDefDegree
```

Through combining each of the components into one string, it provides client applications all of the information they need to know about the CredDef in a simple and easily consumable format.

This is important to mention, since many client applications may still expect CredDef IDs or CredDef Content to contain the information or specific syntax of this Legacy `cred_def_id.`

This legacy format is now attributed to the [Hyperledger Indy Legacy AnonCreds Objects Method](https://hyperledger-indy.readthedocs.io/projects/node/en/latest/requests.html#requests)

</details>

### create CredDef transaction

To create a CredDef on cheqd, you should follow the [tutorials for creating a DID-Linked Resource here](../../tutorials/on-ledger-resources/), and pass the relevant JSON file for the object in the transaction.&#x20;

### cheqd resource Metadata

Once you have created your resource on cheqd, the following metadata will be generated in the DID Document Metadata associated with `did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J`

```json
"linkedResourceMetadata": [
  {
  "resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0",
  "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
  "resourceId": "77465164-5646-42d9-9a0a-f7b2dcb855c0",
  "resourceName": "credDefDegree",
  "resourceType": "claimDef",
  "resourceVersion": "0.0.1",
  "mediaType": "application/json",
  "created": "2022-07-19T08:40:00Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": null,
  "nextVersionId": null
  }
]
```

### Fetching a cheqd CredDef Object

Rather than requiring a specific GET\_CRED\_DEF function and interface to fetch the CredDef Object Content (such as that required on Indy for the `cred_def_id` (`zF7rhDBfUt9d1gJPjx7s1J:3:CL:7BPMqYgYLQni258J8JPS8K:2:degreeSchema:1.5.7:credDefDegree`), existing DID Resolvers will be able to query for the CredDef Object Content using the following parameters:

Common and standardized `resource` parameters:

| Parameter                 | Type                                                          | Example                                                                                                                                                                                                                                                                                                                                                  |
| ------------------------- | ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"resourceId"`            | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                           |
| `"resourceName"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree                                                                                                                                                                                                                                                                                |
| `"resourceType"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef                                                                                                                                                                                                                                                         |
| `"resourceVersionId"`     | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceVersionId=0.0.1                                                                                                                                                                                                                                                       |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&versionTime=2015-03-11T05:30:02Z                                                                                                                                                                                                                       |
| `"versionId"`             | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?versionId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                            |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&versionTime=2018-07-19T08:40:00Z                                                                                                                                                                                                                       |
| `"linkedResource"`        | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?linkedResource=true // _note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document._                                                                                                                                                               |
| `"resourceMetadata"`      | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&versionTime=2018-07-19T08:40:00Z\&resourceMetadata=true or, did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceMetadata=true // note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document. |
| "`latestResourceVersion`" | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&latestResourceVersion=true                                                                                                                                                                                                                             |
| "`allResourceVersions`"   | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&allResourceVersions=true                                                                                                                                                                                                                               |

#### Query by name and version

Like via the Legacy AnonCreds `cred_def_id`, it is possible to obtain the CredDef Object Content by querying the CredDef Publisher DID and CredDef tag. The following query will dereference to the Schema Object Content itself:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=credDefDegree&resourceType=claimDef&versionTime=2022-08-21T08:40:00Z`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=credDefDegree&resourceType=claimDef&versionTime=2022-08-21T08:40:00Z`

This would return the AnonCredsCredDef data and the AnonCredsObjectMetadata.

#### Query by resource ID

For applications which are cheqd-aware, it would be possible to find the Schema Object Content via the `resourceId` using a fully qualified DID URL path or query, for example:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0`

or,

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceId=77465164-5646-42d9-9a0a-f7b2dcb855c0`

using a DID Resolver:

`https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceId=77465164-5646-42d9-9a0a-f7b2dcb855c0`

This would return the AnonCredsCredDef data and the AnonCredsObjectMetadata.
