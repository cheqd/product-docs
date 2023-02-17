---
description: cheqd support for Ledger-Agnostic AnonCreds CredDefs
---

# CredDef Object

## cheqd AnonCreds Object Method for CredDefs

In the ledger-agnostic [AnonCreds](https://hyperledger.github.io/anoncreds-spec/) [specification](https://hyperledger.github.io/anoncreds-spec/), Credential Definitions are used to specify the following information all in one place, to create an immutable record of:

1. The DID of the credential issuer
2. The schema the issued credentials will be based upon
3. The public/private key pairs that will be used to sign the claims within an issued credential
4. A cryptographic secret, embedded within the CredDef Object content, creating an uncorrelatable 'link secret' between the issuer and holder
5. Information necessary for the revocation of credentials, if revocation is to be enabled by the Issuer for this type of credential (Optional).

This documentation will guide an implementor of AnonCreds on cheqd on how the cheqd AnonCreds Object Method defines and structures **cheqd CredDef IDs**, **CredDef Request formats** and **CredDef Response formats**, with and without revocation enabled.

### Hyperledger AnonCreds CredDef Object ID

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

### cheqd CredDef ID

cheqd uses [DID-Linked Resources](../did-linked-resources/) to identify individual resources, associated with a DID, using fully resolvable DID URLs.

cheqd resources module uses the following path-based syntax:

`did:cheqd:mainnet:<IssuerDid>/resources/<CredDefResourceId>`

Rather than using a composite string for the CredDef Resource ID. The cheqd AnonCreds object method uses a UUID to identify the CredDef Object Content which includes additional CredDef Object Content Metadata, providing the required fields for equivalence with Hyperledger Indy implementations.

For example, the following DID URL is cheqd's representation of a `cred_def_id`:

`did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J/resources/77465164-5646-42d9-9a0a-f7b2dcb855c0`

### Understanding Request vs Response formats

It is important to differentiate between the **Request format** for creating an AnonCreds object on cheqd, and the **Response format**, for how an AnonCreds objectshould be compiled by SDKs and the [cheqd DID Resolver](../../architecture/adr-list/adr-003-did-resolver.md).&#x20;

The request format _**may**_ be specific to each AnonCreds Object Method. However, the response format _**should**_ be standardised to enable any AnonCreds supported application to understand the object, without needing custom or method-specific logic.

### cheqd CredDef Request Format

The cheqd schema request format comprises of:

1. A CredDef object file (e.g. `degreeCredDef.json`);
2. A Payload file (including the signing keys)
3. Additional input parameters.

Both of these inputs are required to provide the ledger enough information to:

1. Populate a [cheqd DID-Linked Resource](../did-linked-resources/); and
2. Compile a standardised AnonCreds CredDef object in the [Response format](creddef-object.md#cheqd-schema-response-format).&#x20;

#### cheqd CredDef Object file

Before creating any on-ledger resource transaction, it is important to assemble to required CredDef Object Content and save it as a file locally.

In the example below, the content should be saved as a file, for example: `credDefDegree.json` with the following content (without revocation):

```json
{
  "schemaId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
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

Or with revocation:

```json
{
"AnonCredsObject: {
  "schemaId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
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

This CredDef Object file maps the fields of the CredDef Object to populate a [DID-Linked resource](../did-linked-resources/) stored on cheqd, with the following mapping:

| Object Metadata field | Object Metadata input | Mapped cheqd field | Mapped field output |
| --------------------- | --------------------- | ------------------ | ------------------- |
| "type"                | "CL"                  | "resourceType"     | "anonCredsCredDef"  |
| "tag"                 | ""                    | "resourceVersion"  | ""                  |

What this means is that if the resource data file has an object of "type" = "CL" then it will automatically populate the "resourceType" as "anonCredsCredDef" when creating the "payload file".&#x20;

#### CredDef Payload File

The Payload file extracts the information from the CredDef Object file to populate the following:

```json
{
  "Payload": {
    "collectionId": "zF7rhDBfUt9d1gJPjx7s1J",
    "id": "77465164-5646-42d9-9a0a-f7b2dcb855c0",
    "resourceType": "anonCredsCredDef",
    "version": "latest",
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

#### **CredDef Request format additional inputs**

When passing the payload file to the ledger, additional information MUST also be provided as `flags` to fully populate the [DID-Linked resource](../did-linked-resources/).&#x20;

| Additional parameter | Expected input     | Rationale                                                                                                                                                                                        |
| -------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| "name"               | "universityDegree" | The payload file on its own does not provide the ledger the requisite amount of information to create a full DID-Linked Resource. resourceName must be provided as an additional input parameter |

For example:

```bash
cheqd-noded tx anonCredsCredDef create \
  --chain-id cheqd \
  --keyring-backend test \
  --output json \
  --fees 10000000000ncheq \
  --gas auto \
  --gas-adjustment 1.8 \
  --from base_account \
  --name universityDegree \
  "payload.json" degreeCredDef.json
```

### cheqd CredDef Response format

Using the cheqd CredDef Request format and associated resource metadata, the ledger has enough information to compile the following data structure as a response format.

This can either be compiled by the associated SDK handling cheqd AnonCreds, or it can be assembled by the cheqd DID resolver.&#x20;

```
{
  "issuerId": "did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1J"
  "schemaId": "did:cheqd:mainnet:7BPMqYgYLQni258J8JPS8K/resources/6259d357-eeb1-4b98-8bee-12a8390d3497",
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

#### Compiling Response format in cheqd DID Resolver

The cheqd DID resolver will use the following logic to compile the standardised response format:

{% hint style="info" %}
_If "**resourceType=anonCredsCredDef**" then **append "issuerId"** to the beginning of the Response Format for the resource presented_
{% endhint %}

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
  "resourceVersion": "0.0.1",
  "mediaType": "application/json",
  "created": "2022-07-19T08:40:00Z",
  "checksum": "7b2022636f6e74656e74223a202274657374206461746122207d0ae3b0c44298",
  "previousVersionId": null,
  "nextVersionId": null
  }
]
```

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

To create a CredDef on cheqd, you should follow the [tutorials for creating a DID-Linked Resource here](../../tutorials/on-ledger-resources/), and pass the relevant JSON file for the object in the transaction.

### Fetching a cheqd CredDef Object

Existing DID Resolvers will be able to query for the CredDef Object Content using the following parameters:

Common and standardized `resource` parameters:

| Parameter                 | Type                                                          | Example                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `"resourceId"`            | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                                   |
| `"resourceName"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree                                                                                                                                                                                                                                                                                        |
| `"resourceType"`          | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef                                                                                                                                                                                                                                                                 |
| `"resourceVersionId"`     | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceVersionId=0.0.1                                                                                                                                                                                                                                                               |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&versionTime=2015-03-11T05:30:02Z                                                                                                                                                                                                                               |
| `"versionId"`             | [String](https://infra.spec.whatwg.org/#string)               | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?versionId=0f964a80-5d18-4867-83e3-b47f5a756f02                                                                                                                                                                                                                                                                    |
| `"versionTime"`           | [XML Datetime](https://www.w3.org/TR/xmlschema11-2/#dateTime) | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&versionTime=2018-07-19T08:40:00Z                                                                                                                                                                                                                               |
| `"linkedResource"`        | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?linkedResource=true // _note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document._                                                                                                                                                                       |
| `"resourceMetadata"`      | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&versionTime=2018-07-19T08:40:00Z\&resourceMetadata=true or, did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceMetadata=true // note that this would only be a valid query if there is ONLY ONE resource associated with the DID and DID Document. |
| "`latestResourceVersion`" | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&latestResourceVersion=true                                                                                                                                                                                                                                     |
| "`allResourceVersions`"   | [Boolean](https://infra.spec.whatwg.org/#booleans)            | did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c?resourceName=credDefDegree\&resourceType=claimDef\&allResourceVersions=true                                                                                                                                                                                                                                       |
