---
description: Create a Token Status List JWT or CWT as a DID-Linked Resource
---

# Create Token Status List

Users are able to create Token Status List entries on-ledger, which may be used to represent whether a Verifiable Credential is active, inactive or suspended. This implementation on cheqd is a derivation from the core spec made by cheqd to support a more decentralised and resilient approach to storing Token Status Lists.

## Step 1: Set up your account

Make sure you have set up your account with cheqd Studio and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Studio and log in to start using the APIs.</td><td><a href="../../set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Create a DID

Before you can create a Status List, you need to create a DID which is used to link the Status List on-ledger. Use the API in the page below to create a DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

## Step 3. Create your Status List JWT/CWT and save the file locally

Token Status Lists are JWT or CWT files that reference lists of bits formatted in JSON or CBOR.

<details>

<summary>Non-normative example for a Status List Token in JWT format</summary>

```json
{
  "alg": "ES256",
  "kid": "12",
  "typ": "statuslist+jwt"
}
.
{
  "exp": 2291720170,
  "iat": 1686920170,
  "iss": "https://example.com",
  "status_list": {
    "bits": 1,
    "lst": "eNrbuRgAAhcBXQ"
  },
  "sub": "https://example.com/statuslists/1"
}
```

</details>

<details>

<summary>Non-normative example for a Status List Token in CWT format (not including the type header yet)</summary>

```json
d28453a20126106e7374617475736c6973742b637774a1044231325860a502782168
747470733a2f2f6578616d706c652e636f6d2f7374617475736c697374732f310173
68747470733a2f2f6578616d706c652e636f6d061a648c5bea041a8898dfea19fffe
56a2646269747301636c73744a78dadbb918000217015d58400f2ca3772e10b09d5d
6ed56461f7cba1a816c6234072d1bb693db277048e5db5a4e64444492a9b781d6c7a
c9714db99cc7aadb3812ec90cab7794170bab5b473
```

</details>

Save this file locally and call it something like `statusListToken.json`

{% hint style="info" %}
Note that each JWT or CWT must be **below \~45kb in size**.
{% endhint %}

## Step 4: Encode the file

Prepare a file with resource and encode it into `base64, base64url` or `hex`. On Unix systems, you can use the following command input:

```bash
$ base64 -w 0 resource.json
<path-to-the-resource-file>
```

Expected output:

```bash
$ base64 -w 0 resource.json
SGVsbG8sIHdvcmxk
```

## Step 5: Set the resource name and type

DID-Linked Resources are grouped by having identical **names** and **types**. This means if you want to create a new version of the same Resource, you will need to **specify the same name and type in the following request**.&#x20;

For Token Status Lists, the "type" MUST be:  **`"TokenStatusList"`.**

For example:

```json
{
    "data": "SGVsbG8sIHdvcmxk",
    "encoding": "base64url",
    "name": "DegreeCredentialStatus",
    "type": "TokenStatusList"
}
```

## Step 6: Populate the request inputs and hit the API

Ensure that you link this Token Status List to the DID that you created in step 3. This will sign the resource with the same verification method keys in your DID Document, ensuring cryptographic integrity and Controllership of the Status List.

As a DID-Linked Resource, the Token Status List will have a **fully resolvable DID URL** which can be referenced within the body of Verifiable Credentials, and queried by verification policies to establish the status of the specific credential.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/resource/create/{did}" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

## Step 7: Reference the Token Status List

Owing to the design of DID-Linked Resources, following the creation of the Token Status List, users are able to reference the **specific version**, or create a query to **always fetch the latest version** of the Token Status List.&#x20;

Using a DID Resolver or the search DID endpoint, users can find the DID URL and unique resourceId of the Token Status List. The unique resourceId allows users to specify this exact version of the Token Status List.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/did/search/{did}" method="get" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

In the DID Document Metadata, users should find "linkedResourceMetadata", like the following snippet:

```json
"linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d/resources/4e1104f9-2ee9-4bde-adc2-ab8ba72b124a",
        "resourceCollectionId": "0a5b94d0-a417-48ed-a6f5-4abc9e95888d",
        "resourceId": "4e1104f9-2ee9-4bde-adc2-ab8ba72b124a",
        "resourceName": "DegreeCredentialStatus",
        "resourceType": "TokenStatusList",
        "mediaType": "application/json",
        "resourceVersion": "",
        "created": "2023-03-24T12:13:45Z",
        "checksum": "6819aaecd4073173b159fedf8077c38e14939d03d58e7f4e2a0ddfe034eb2ed4",
        "previousVersionId": null,
        "nextVersionId": null
      } 
```

### Specific version of the Token Status List

Here, the "`resourceURI`" specifies the DID URL of the specific Token Status List that was created.

### Latest version of the Token Status List

In order to **reference the latest version of the Token Status List**, the following construction needs to be used:

`did:cheqd:<namespace>:<resourceCollectionId>?resourceName=<resourceName>&resourceType=<resourceType>`

For example:

`did:cheqd:testnet:`0a5b94d0-a417-48ed-a6f5-4abc9e95888d`?resourceName=DegreeCredentialStatus&resourceType=TokenStatusList`

### Token Status List at specific point in time

In order to **reference the Token Status List at a particular point in time**, the following construction needs to be used:

`did:cheqd:<namespace>:<resourceCollectionId>?resourceName=<resourceName>&resourceType=<resourceType>&resourceVerionTime=<XMLDateTime>`

For example:

`did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d?resourceName=DegreeCredentialStatus&resourceType=TokenStatusList&resourceVersionTime=2023-02-22T06:58:18.61Z`
