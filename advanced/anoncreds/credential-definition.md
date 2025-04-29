---
description: cheqd support for Ledger-Agnostic AnonCreds CredDefs
---

# Credential Definitions

## cheqd AnonCreds Object Method for CredDefs

In the ledger-agnostic [AnonCreds](https://hyperledger.github.io/anoncreds-spec/) [specification](https://hyperledger.github.io/anoncreds-spec/), Credential Definitions are used to specify the following information all in one place, to create an immutable record of:

1. The DID of the credential issuer
2. The schema the issued credentials will be based upon
3. The public/private key pairs that will be used to sign the claims within an issued credential
4. A cryptographic secret, embedded within the CredDef Object content, creating an uncorrelatable 'link secret' between the issuer and holder
5. Information necessary for the revocation of credentials, if revocation is to be enabled by the Issuer for this type of credential (Optional).

This documentation will guide an implementor of AnonCreds on cheqd on how the cheqd AnonCreds Object Method defines and structures **cheqd CredDef IDs**, **CredDef Request formats** and **CredDef Response formats**, with and without revocation enabled.

### Hyperledger AnonCreds CredDef Object

If you are not familiar with the latest Ledger-Agnostic AnonCreds CredDef structure, click the collapsible tile below to learn about the new format.

<details>

<summary>Ledger-Agnostic AnonCreds CredDef Object Content</summary>

Each specific AnonCreds identifier must be defined within an AnonCreds Object Method in the [AnonCreds Object Method Registry](https://hyperledger.github.io/anoncreds-spec/).

This means that an AnonCreds CredDef Object ID does not need to be formatted in any particular syntax, in the latest version of the AnonCreds Specification.

Credential Definition Object Content is distinct in the way it is structured. The inputs and outputs generated when creating a CredDef are as follows:

**Create CredDef input**

The following inputs are required to create a CredDef:

* A schema on which the CredDef will be based
* A `tag`, an arbitrary string defined by the Issuer, enabling an Issuer to create multiple CredDefs for the same schema.
* An optional flag `support_revocation` (default `false`) which if true generates some additional data in the CredDef to enable credential revocation.

The operation produces two objects, as follows.

* The [Private Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:private-credential-definition), an internally managed object that includes the private keys generated for the [Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:credential-definition) and stored securely by the issuer.
* The [Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:credential-definition), that includes the public keys generated for the [Credential Definition](https://hyperledger.github.io/anoncreds-spec/#term:credential-definition), returned to the calling function and then published on a VDR.

**Create CredDef output (private)**

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

**Create CredDef output (public)**

The output of a create CredDef transaction is a JSON structure that is generated using cryptographic primitives.

A CredDef has a couple of important characteristics:

1. Each of the schema attributes is individually signed using a public key fragment.
2. Each CredDef includes a `master_secret` attribute. This value is blindly contributed to the credential during issuance and used to bind the issued credential to the entity it was issued to.
3. The output includes the:
   1. `issuerID`
   2. `schemaID`
   3. `type`
   4. `tag`

Further details regarding the use of cryptographic primatives are described in the [CredDefs section of the AnonCreds specification here](https://anoncreds-wg.github.io/anoncreds-spec/#generating-a-cred_def-without-revocation-support).

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

### cheqd CredDef ID

cheqd uses [DID-Linked Resources](../../studio/did-linked-resources/understanding-dlrs/) to identify individual resources, associated with a DID, using fully resolvable DID URLs.

cheqd resources module uses the following path-based syntax:

`did:cheqd:mainnet:<IssuerDid>/resources/<CredDefResourceId>`

Rather than using a composite string for the CredDef Resource ID. The cheqd AnonCreds object method uses a UUID to identify the CredDef Object Content which includes additional CredDef Object Content Metadata, providing the required fields for equivalence with Hyperledger Indy implementations.

For example, the following DID URL is cheqd's representation of a `credDefId`:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0`

Another supported format for a `credDefId` may be used in applications where it is important to derive the `credDefId`, `revocRegDefId` and `statusListEntryId` from the same root.

This format uses query-based syntax, for example:

`did:cheqd:mainnet:<IssuerDid>?resourceName=<resourceName>&resourceType=<resourceType>`

For example:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsCredDef`

### Understanding Request vs Response formats

It is important to differentiate between the **Request format** for creating an AnonCreds object on cheqd, and the **Response format**, for how an AnonCreds objectshould be compiled by SDKs and the [cheqd DID Resolver](../../architecture/adr-list/adr-003-did-resolver.md).

The request format _**may**_ be specific to each AnonCreds Object Method. However, the response format _**should**_ be standardised to enable any AnonCreds supported application to understand the object, without needing custom or method-specific logic.

### cheqd CredDef Request Format

The cheqd CredDef request format comprises of:

1. A Resource file for the CredDef object content (e.g. `degreeCredDef.json`); and
2. A Payload file (including the signing keys and additional inputs to create a DID-Linked Resource).

Both of these inputs are required to provide the ledger enough information to:

1. Populate a [cheqd DID-Linked Resource](../../studio/did-linked-resources/understanding-dlrs/); and
2. Compile a standardised AnonCreds CredDef object in the [Response format](credential-definition.md#cheqd-creddef-response-format).

#### cheqd CredDef Resource file

Before creating any on-ledger resource transaction, it is important to assemble the required CredDef Resource file and save it as a file locally.

In the example below, the content should be saved as a file, for example: `degreeCredDef.json` with the following content (without revocation):

```json
{
  "schemaId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
  "type": "CL",
  "tag": "1.0",
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

Or with revocation:

```json
{
  "schemaId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
  "type": "CL",
  "tag": "1.0",
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

This CredDef Resource file inputs should be replicated where possible within the Payload file, to populate a [DID-Linked resource](../../studio/did-linked-resources/understanding-dlrs/) stored on cheqd, with the following mapping:

| CredDef Object field | CredDef Object expected input | Mapped Payload file field | Mapped Payload file input |
| -------------------- | ----------------------------- | ------------------------- | ------------------------- |
| "type"               | "CL"                          | "resourceType"            | "anonCredsCredDef"        |
| "tag"                | ""                            | "resourceVersion"         | ""                        |

What this means is that if the **Resource file** has an object of **"type" = "CL"** then this should be represented as the "**resourceType" = "anonCredsCredDef"** within the **Payload file**.

#### CredDef Payload File

The **Payload file** utilises the inputs from the **Resource file** where possible, mapping common fields across. The **Payload file** may also require additional inputs to be provided by the creator to create a DID-Linked Resource for inputs not provided in the **Resource file**.

Below is an example of a Payload file:

```json
{
  "payload": {
    "collectionId": "zF7rhDBfUt9d1gJPjx7s1J",
    "id": "77465164-5646-42d9-9a0a-f7b2dcb855c0",
    "name": "universityDegree", // this is an additional input
    "version": "1.0", // this is an additional input which can be derived from the tag
    "resourceType": "anonCredsCredDef",
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

| Additional parameter | Expected input     | Rationale                                                                                                                                                                                                                              |
| -------------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "name"               | "universityDegree" | The Payload file drawing inputs from the Resource file on its own does not provide the ledger the requisite amount of information to create a full DID-Linked Resource. resourceName must be provided as an additional input parameter |

#### Publishing resource using CLI

For example, the full request format using a CLI should be structured as follows:

```bash
cheqd-noded tx resource create [payload.json] [degreeCredDef.json] \

  --chain-id cheqd \
  --keyring-backend test \
  --output json \
  --fees 10000000000ncheq \
  --gas auto \
  --gas-adjustment 1.8 \
  --from base_account \
```

### cheqd resource Metadata

Once you have created your resource on cheqd, the following metadata will be generated in the DID Document Metadata associated with `did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J`

```json
"linkedResourceMetadata": [
  {
  "resourceURI": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0",
  "resourceCollectionId": "zF7rhDBfUt9d1gJPjx7s1J",
  "resourceId": "77465164-5646-42d9-9a0a-f7b2dcb855c0",
  "resourceName": "universityDegree",
  "resourceType": "anonCredsCredDef",
  "resourceVersion": "1.0",
  "mediaType": "application/json",
  "created": "2022-07-19T08:40:00Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": null,
  "nextVersionId": null
  }
]
```

### cheqd CredDef Response format

Using the [cheqd CredDef Request format](credential-definition.md#cheqd-creddef-request-format) and [associated resource metadata](credential-definition.md#cheqd-resource-metadata), the ledger has enough information to compile the following data structure as a response format.

This can either be compiled by the relevant SDK handling cheqd AnonCreds, or it can be assembled by the cheqd DID resolver.

```json
{
  "issuerId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J"
  "schemaId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
  "type": "CL",
  "tag": "1.0",
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

#### Compiling Response format in cheqd DID Resolver

The cheqd DID resolver will use the following logic to compile the standardised response format:

{% hint style="info" %}
_If "**resourceType=anonCredsCredDef**" then **append "issuerId"** to the beginning of the Response Format for the resource presented_
{% endhint %}

### create CredDef transaction

To create a CredDef on cheqd, you should follow the [tutorials for creating a DID-Linked Resource here](../../sdk/veramo/did-linked-resources/), and pass the relevant JSON file for the object in the transaction.

## Tying CredDef, RevRegDef and StatusList Objects together

Across the [cheqd CredDef Object Method](credential-definition.md#cheqd-anoncreds-object-method-for-creddefs), the [Revocation Registry Definition Object Method](revocation-registry-definition.md) and the [StatusListEntry Object Method](revocation-status-list.md) - each resource is associated with the same issuer DID and Collection ID.

Importantly, this allows each new resource to be indexed and versioned by their:

1. `resourceName`
2. `resourceType`

New resources can be created to update the existing CredDef or RevRegDef, whilst maintaining the historical state of the previous versions. See the documentation on [Publishing a New Version of a Resource](../tooling/cheqd-node-cli/add-more-resources.md) to understand this further.

### Fetching a cheqd CredDef Object

Existing DID Resolvers will be able to query for the CredDef Object Content using the [same patterns and parameters as the Schema Object found here](schema.md#fetching-a-cheqd-resource).

The cheqd AnonCreds method also enables applications to derive the [CredDef](credential-definition.md), [Revocation Registry Definition Object](revocation-registry-definition.md) and [Status List](revocation-status-list.md) from the same root:

### Same Resource Name, different Resource type

We propose that the `resourceName` for CredDefs, Revocation Registry Definitions and Status Lists **should remain the same** when each of these resources is part of the same AnonCred. This will make it easier for resources to query by `resourceName` and `resourceType` to delineate between the three resources using a common root.

Using this logic, the following queries can be used to dereference to [CredDefs](credential-definition.md), [Revocation Registry Definitions](revocation-registry-definition.md) and [Status List](revocation-status-list.md), in a way which can derive all three resources from the same root:

#### Dereference to CredDef

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsCredDef`

#### Dereference to Revocation Registry Definition

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsRevocRegDef`

#### Dereference to Revocation Status List

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J?resourceName=universityDegree&resourceType=anonCredsStatusList`

{% hint style="info" %}
**Note**: across all three of these queries, the resolver would fetch the latest version of the resource by default
{% endhint %}

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

### Legacy AnonCreds CredDef Object

<details>

<summary>Legacy AnonCreds CredDef Object</summary>

Like with [Schema Objects](schema.md), each CredDef Object ID (`cred_def_id`) was previously a **composite** string, consisting of the following elements:

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

This legacy format is now attributed to the [Hyperledger Indy Legacy AnonCreds Objects Method](https://hyperledger.github.io/anoncreds-methods-registry/#hyperledger-indy-legacy-anoncreds-method).

</details>
