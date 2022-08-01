# ⏺ Resources

## Overview

cheqd has built a **Resources Module** to extend the functionality of the network.&#x20;

A **Resource** is a blob of information stored on-ledger, within a specific syntax. The use of Resources is potentially very broad; they can provide many different roles, such as:

* A **schema** (Camenisch-Lysyanskaya ‘CL’, JSON-LD, JSON)&#x20;
* A **schema overlay** (such as [OCA](https://humancolossus.foundation/blog/cjzegoi58xgpfzwxyrqlroy48dihwz))&#x20;
* A **document** (such as a Governance Framework in Markdown, like [proposed by ToIP](https://wiki.trustoverip.org/pages/viewpage.action?pageId=71241))&#x20;
* An **image file** (Company logo, brandmark, which can be pulled directly by block explorers and other ledger applications)&#x20;
* A **revocation registry** (more on this in a future blog)&#x20;

Through extending the use of DIDs to identify other on-ledger resources, trust can begin to be chained. This begins to enable new use cases, such as:

| Audience              | Quick wins                                                                                                                                                                                                                                                    | Longer term strategic objectives                                                                                                                                                                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **SSI Community**     | Create a much more **secure, resilient and decentralised format for storing schemas** than [schema.org](https://schema.org/)                                                                                                                                  | Lay the technical foundations for **supporting** [**AnonCreds**](https://hyperledger-indy.readthedocs.io/projects/sdk/en/latest/docs/design/002-anoncreds/README.html) **compatible Verifiable Credentials on cheqd**, in addition to support for JSON and JSON-LD |
| **Web 3.0 community** | Extend the [Trust over IP Resource Parameter Specification](https://wiki.trustoverip.org/display/HOME/DID+URL+Resource+Parameter+Specification), enabling **DIDs to act as #Web3 hyperlinks for any on (or off) ledger URL, Resource, file, or content** | Enable Web3 Companies to use on-ledger Resources to **fetch company information (such as logos, token info, relevant token APIs)** to **populate Block Explorers and Exchanges**, rather than through Keybase or manual processes.                                 |

{% hint style="info" %}
You can think of it as a hyperlink for #Web3. But unlike a hyperlink, it is possible to specify the type of resource to be retrieved, and reject anything unexpected.
{% endhint %}

## Resources

Resources defined in this architecture are identified with a unique identifier (UUID), and are stored within a **Collection** (more on this below). This enables unique resources to be stored directly on-ledger and be **retrievable through DID resolution** and **dereferencing**.&#x20;

For simplicity, we will focus on the use case where a Resource is **a schema**.&#x20;

However, it is important to note that the same logic used in fetching schemas from the ledger can be applied to any of the aforementioned types of Resources.&#x20;

The syntax of a Resource is as follows:

```json
Resource1
{
  "header": {
    "collectionId":       "46e2af9a-2ea0-4815-999d-730a6778227c",
    "id":                 "0f964a80-5d18-4867-83e3-b47f5a756f02",
    "name":               "DegreeLaw",
    "resourceType":       "CL-Schema",
    "created":            "2022-04-20T20:19:19Z",
    "checksum":           "a7c369ee9da8b25a2d6e93973fa8ca939b75abb6c39799d879a929ebea1adc0a",
    "previousVersionId:   "688e0e6f-74dd-43cc-9078-09e4fa05cacb",
    "nextVersionId:       null
  },
  "data":                 <ClSchema.json including '{\"attrNames\":[\"last_name\",\"first_name\"\"degree_type\"\"graduation_year\"\"degree_score\"\"degree_class\"]}` in bytes>,
}
```

## Collections&#x20;

A **Collection** is a group of **Resources** stored directly on-ledger, within the state of the ledger. This means that Collections are written **within** **transactions** on the ledger, and therefore, their authenticity is secured by the consensus and propagation of the nodes on the ledger.&#x20;

Collections can store any type of Resource, but for the purpose of this documentation we will focus on the use case where the **Collection is storing a set of schemas**.&#x20;

Collections are identified by a **Collection ID** which is a **unique identifier (UUID)** which is **different from any Resource ID** stored within it.&#x20;

For example:

```json
Collection
{
  "Collection_id":      "46e2af9a-2ea0-4815-999d-730a6778227c"
  "id":                 "0f964a80-5d18-4867-83e3-b47f5a756f02",
  "name":               "DegreeLaw",
  "resource_type":      "CL-Schema",
  "mime_type":          "application/json"
  "Created":            "2022-04-20T20:19:19Z",
  "Checksum":           "a7c369ee9da8b25a2d6e93973fa8ca939b75abb6c39799d879a929ebea1adc0a",
  "previous_version_id": "688e0e6f-74dd-43cc-9078-09e4fa05cacb",
  "next_version_id":     null,

  …

  "id":                 "99b40fd8-fade-483c-bff4-f037b26dd810",
  "name":               "DegreeMath",
  "resource_type":      "CL-Schema",
  "mime_type":          "application/json"
  "Created":            "2022-04-20T20:19:19Z",
  "Checksum":           "a7c369ee9da8b25a2d6e93973fa8ca939b75abb6c39799d879a929ebea1adc0a",
  "previous_version_id": "bd77dccf-bab4-4d02-8873-27962c2c55ba",
  "next_version_id":     null
}
```

Notably, in the example above, it does not show the actual data / schema attributes within the specific resource. It only shows what we refer to as a **‘Resource Preview’**.&#x20;

This logic prevents `GetResourceCollection` requests returning large quantities of data, which may be stored across multiple Resources within a Collection.&#x20;

In order to fetch the actual data, it is necessary to query **the specific Resource**, rather than **the entire Collection**.&#x20;

For more information about the particulars of requests and responses, please refer to our [**ADR on Resources on ledger**](https://github.com/cheqd/node-docs/blob/adr-008-resources-updates/architecture/adr-list/adr-008-ledger-resources.md).

## Referencing Collections and Resources

The most important concept used in this architecture is that each on-ledger **Collection** is **identified using a DID** and is **described using a** **DID Document**.&#x20;

The DID Document acts as metadata, providing information about the **Collection**, such as who is able to update it, when it was created and what are the latest and deprecated versions of **Resources** within the **Collection**.&#x20;

Moreover, each specific **Resource** within a **Collection** can be fetched via a DID Document relating to a Collection, through the use of the `‘service’` section.&#x20;

Next, we will explain how Collections are intrinsically linked to their DID Documents and how Resources can be directly fetched.&#x20;

### Overall structure&#x20;

_Figure 1_ below shows a complete topology of the architecture, including where different layers of the structure interact with each other.

![Figure 1: cheqd resources architecture diagram](<../../.gitbook/assets/cheqd Resource full architecture.png>)

Since the overall structure of our resource implementation is complex and has multiple layers, we will explain each component in turn.&#x20;

### Collection DID Document&#x20;

Let’s take a look at a **DID Document for a Collection**, and break it down:

```json
{
  "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c",
  "verification_method": [
    {
      "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c",
      "public_key_multibase": "<verification-public-key-multibase>"
    }
  ],
  "authentication": [
    "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#key1"
  ],
  "service": [
    {
        "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeLaw",
        "type": "CL-Schema",
        "serviceEndpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02"]
    },
    {
        "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeMath",
        "type": "CL-Schema",
        "serviceEndpoint": [ "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/99b40fd8-fade-483c-bff4-f037b26dd810"] 
    },
    {
        "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#JSON-Schema",
        "type": "JSONSchema2020",
        "serviceEndpoint": "https://schema.org/Person"
    },
    {
        "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#Markdown",
        "type": "Markdown",
        "serviceEndpoint": "https://github.com/cheqd/node/docs/SCHEMA.md"
    }
  ]
}
```

#### “id” of Collection DID URL references Collection ID&#x20;

The DID URL (“id”) of the Collection DID Document carries out two functions:&#x20;

* Firstly, it exists as a regular DID, anchored on cheqd, and resolvable through any compatible DID Resolver;&#x20;
* Secondly, it directly references the Collection ID of the Collection of Resources associated with the DID.&#x20;

The completed string itself is a DID:&#x20;

`did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c`&#x20;

Whereas, the unique ID on its own is the Collection ID:&#x20;

`46e2af9a-2ea0-4815-999d-730a6778227c`&#x20;

Therefore, the **same unique identifier** is also used as the **identifier of an on-ledger Collection of Resources**.&#x20;

This means that if this UUID alone was queried on the cheqd ledger, without the "did:cheqd:mainnet:", it would return all Resources within a specific Collection.&#x20;

#### "Type" = Type of Resource - Extended&#x20;

In general, the ‘`type`’ within the service section denotes the type of **Resource** expected within the `service endpoint`.&#x20;

Currently, there are two formally recorded types in the [DID Spec Registries](https://www.w3.org/TR/did-spec-registries/), which are:&#x20;

* "Type" = LinkedDomains; and&#x20;
* "Type" = DIDCommMessaging&#x20;

In order to make schemas on ledger feasible, we are suggesting multiple new types which denote different types of schemas on-ledger, such as:&#x20;

* "Type" = CL-Schema&#x20;
* "Type" = JSONSchema2020&#x20;

The `ResourceType` must be non-null and provided by the client app. cheqd will NOT enforce restrictions on what the value can be (this is CL-Schema, JSONSchema2020 etc), which keeps the contents flexible:

```json
{
  “type”                "CL-Schema"             
}
```

#### **MediaType**

A Golang library is used to derive and set media type based on the **file extension of provided resource file**. This makes it much simpler to maintain, since cheqd are not gatekeeping a list as such, and there’s libraries that can do this. See [golang’s main repo](https://github.com/golang/go/blob/master/src/mime/mediatype.go), other [alternatives](https://stackoverflow.com/questions/51209439/mime-type-checking-of-files-uploaded-golang),

#### "serviceEndpoint" = URL to fetch specific Resource on-ledger&#x20;

The contents in the `serviceEndpoint` is intended to directly link to or resolve to a specific Resource on-ledger. It can be directly fetched using a URL, such as [cheqd's DID Resolver driver](https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:zF7rhDBfUt9d1gJPjx7s1JXfUY7oVWkY), which can be used via resolver.cheqd.net or can be simply integrated into any existing resolver.

For example: INPUT

```json
{
  "type":               "CL-Schema"
  "serviceEndpoint":    "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02",  
}
```

OUTPUT:

```json
Resource1
{
  "header": {
    "collectionId":      "46e2af9a-2ea0-4815-999d-730a6778227c",
    "id":                 "0f964a80-5d18-4867-83e3-b47f5a756f02",
    "name":               "DegreeLaw",
    "resourceType":      "CL-Schema",
    "created":            "2022-04-20T20:19:19Z",
    "checksum":           "a7c369ee9da8b25a2d6e93973fa8ca939b75abb6c39799d879a929ebea1adc0a",
    "previousVersionId:   "688e0e6f-74dd-43cc-9078-09e4fa05cacb",
    "nextVersionId:     null
  },
  "data":               <CLSchema.json including '{\"attrNames\":[\"last_name\",\"first_name\"\"degree_type\"\"graduation_year\"\"degree_score\"\"degree_class\"]}` in bytes>,
}
```

### Versioning and Archiving Resources&#x20;

As shown in the example above, there may be **previous** and **next** versions of the **Resource** **ID**.

Whenever a Resource is updated, a new UUID must be generated, and the previous UUID becomes an archived version of the Resource.&#x20;

Importantly, the Name, ResourceType and MimeType of the Resource must remain static.&#x20;

For example, a Resource with the name ‘DegreeLaw’ must always remain the same. This is to allow a user to query the Resource ‘DegreeLaw’ by versionTime in order to find the appropriate schema at any point in time. You can search for an archived resource using the cheqd resolver, for example, through:

[https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/688e0e6f-74dd-43cc-9078-09e4fa05cacb](https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/688e0e6f-74dd-43cc-9078-09e4fa05cacb)

Or,

[https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#degreeLaw?versionTime=2016-10-17T02:41:00Z](https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#degreeLaw?versionTime=2016-10-17T02:41:00Z)

Returning:

```json
Resource1
{
  "header": {
    "collectionId":      "46e2af9a-2ea0-4815-999d-730a6778227c",
    "id":                "688e0e6f-74dd-43cc-9078-09e4fa05cacb",
    "name":              "DegreeLaw",
    "resourceType":      "CL-Schema",
    "created":           "2015-02-20T14:12:57Z",
    "checksum":          "a7c369ee9da8b25a2d6e93973fa8ca939b75abb6c39799d879a929ebea1adc0a",
    "previousVersionId:  null,
    "nextVersionId:      "0f964a80-5d18-4867-83e3-b47f5a756f02",
  }
  "data":                "<CLSchema.json including '{\"attrNames\":[\"last_name\",\"first_name\"\"degree_type\"\"graduation_year\"\"degree_percentage\"]}` in bytes>"
}
```

Here, the result shows a CL-Schema with different data fields to the more recent 2022 version. For example, it shows ‘degree\_percentage’ rather than ‘degree\_score’ and omits ‘degree\_class’ altogether.&#x20;

The next version, the more up-to-date version of the CL-Schema can be queried through:&#x20;

[https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02](https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02)

Or,

[https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#degreeLaw?versionTime=2022-06-20T02:41:00Z](https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#degreeLaw?versionTime=2022-06-20T02:41:00Z)

Returning:

```json
Resource1
{
  "header": {
    "collectionId":       "46e2af9a-2ea0-4815-999d-730a6778227c",
    "id":                 "0f964a80-5d18-4867-83e3-b47f5a756f02",
    "name":               "DegreeLaw",
    "resourceType":       "CL-Schema",
    "created":            "2022-04-20T20:19:19Z",
    "checksum":           "a7c369ee9da8b25a2d6e93973fa8ca939b75abb6c39799d879a929ebea1adc0a",
    "previousVersionId:   "688e0e6f-74dd-43cc-9078-09e4fa05cacb",
    "nextVersionId:       null
  },
  "data":                 <CLSchema.json including '{\"attrNames\":[\"last_name\",\"first_name\"\"degree_type\"\"graduation_year\"\"degree_score\"\"degree_class\"]}` in bytes>,
}
```

As you can see, the more recent version of the Resource **shares the** **same Collection ID**, **name, resource type and mime type**, but includes **different schema attributes**.&#x20;

Therefore, this architecture **easily allows historic Resources to be fetched, recovered and verified against**. For example, if you were presented a Law Degree Credential from 2015, you would still be able to prove that it conformed to the correct schema at the time of its issuance, even if the latest schema had been updated in 2022.&#x20;

### Linking Resource and Collection to DID Document&#x20;

The logic for tying a Resource within a Collection together with its DID Document is carried out by the ledger itself, through the **Resource module**.&#x20;

A Collection, Resource and DID Document may be linked together through the following flow:&#x20;

#### 1. Collection DID Document is created&#x20;

At the point the Collection DID Document is created, the service section does not reference any particular Resource. This will therefore be a very basic DID Document, similar to any other&#x20;

#### 2. DID Document Resource is created&#x20;

An authorised party then creates a Resource, specifying:&#x20;

* A Collection ID with the same unique identifier as the Collection DID Document&#x20;
* A new Resource ID&#x20;
* Resource name, Resource type, Mime type&#x20;

The authorised party then signs this createResource request with the same verificationMethod or authentication keys as is specified within the Collection DID Document.&#x20;

#### 3. Authorised party updates Collection DID Document accordingly&#x20;

Through an update DID operation, the authorised party will be able to **update the service section** within the **Collection DID Document** to **include the appropriate Collection and Resource IDs and Resource name**, as below:

```json
  "service": [
    {
        "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeLaw",
        "type": "CL-Schema",
        "serviceEndpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02"]
    }
```

### Collection DID Document metadata includes Resource IDs&#x20;

After the Resource has been linked to the Collection DID Document (this is done when the Create Resource transaction is committed, where the same keys are used to sign the transaction), it will be referenced within the **Collection DID Document metadata itself**, in the following format.

```json
QueryGetDidResponse {
  "did": {
    "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c",
    ...
  },
  "metadata": {
    "created": "2020-12-20T19:17:47Z",
    "updated": "2020-12-20T19:19:47Z",
    "deactivated": false,
    "versionId": "1B3B00849B4D50E8FCCF50193E35FD6CA5FD4686ED6AD8F847AC8C5E466CFD3E",
    "resources": [
      {
        "resourceURI":      "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02",
        "resourceType":      "CL-Schema",
        "mimeType":          "application/json"
      }
    ]
  }
}
```

Importantly, this is again a **Resource Preview**, meaning that the actual raw data from the Resource will not be displayed.&#x20;

This will need to be queried directly in order to be fetched.&#x20;

You may also notice that the resourceURI here is a DID rather than a HTTPs link. We elected to include this approach to enable resolvers to dereference the DID Document to fetch resources, rather than solely relying on the URL format.&#x20;

Thus, this does not require any exposure to the cheqd resolver, and should be able to be resolved by any compatible DID resolver via the Universal Resolver.&#x20;

### Referencing a Collection DID Document, Collection and specific Resource within an Issuer DID Document&#x20;

Once a Collection DID Document has been created and is tied to the appropriate Resources, an **Issuer** may want to **update their own DID Document** to demonstrate that such Resources are used by the Issuer. An example of an Issuer DID Document which references a Collection DID Document, Collection and specific Resource is shown in the example below:

```json
{
  "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c",
  "verification_method": [
    {
      "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c",
      "public_key_multibase": "<verification-public-key-multibase>"
    }
  ],
  "authentication": [
    "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c#key1"
  ],
  "service": [
    {
      "id": "did:cheqd:mainnet:17dd8754-c5ad-45d3-8f6c-078bfa72c63c#website",
      "type": "LinkedDomains",
      "serviceEndpoint": "https://www.cheqd.io"
    },
    {
        "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeLaw",
        "type": "CL-Schema",
        "serviceEndpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/0f964a80-5d18-4867-83e3-b47f5a756f02"
    },
    {
        "id": "did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c#DegreeMath",
        "type": "CL-Schema",
        "serviceEndpoint": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:46e2af9a-2ea0-4815-999d-730a6778227c/resources/99b40fd8-fade-483c-bff4-f037b26dd810"
    }
  ]
}
```

In this case, an **Issuer will carry out a manual update to their DID Document**, through authenticating with their appropriate verification keys and:

* Specify the **Collection DID URL** within the “`id`” fields of the ‘`service`’ sections, within which the Issuer wants to reference that particular Collection.&#x20;
* Specify the `resourceName` as the **fragment (#)** appended to the end of the **service ‘`id`’ URL**&#x20;
* Specify the **same URL as is defined within the Collection DID URL** to directly point-to and fetch the specific Resource, **within the `serviceEndpoint` field**.&#x20;

Through linking in this way, the **Issuer is able to clearly and transparently illustrate, for example, which Schemas it conforms to when issuing Verifiable Credentials**. Or alternatively, the Issuer could use this to point to the canonical and latest version of their Governance Framework.

Moreover, the Resource referenced in the Issuer DID Document does not need to be controlled by the Issuer, they could show conformance to any Schema, controlled by any party. This architecture therefore provides a W3C compliant and highly flexible way to tie Resources to DIDs.&#x20;

Read more:

- [AnonCreds Design (Hyperledger Indy SDK](https://hyperledger-indy.readthedocs.io/projects/sdk/en/latest/docs/design/002-anoncreds/README.html))

- [ADR 008: On-ledger Resources with DIDs](https://github.com/cheqd/node-docs/blob/adr-008-resources-updates/architecture/adr-list/adr-008-ledger-resources.md)
