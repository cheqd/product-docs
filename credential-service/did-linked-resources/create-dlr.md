---
description: Create a DID-Linked Resource associated with a cheqd DID over REST API
---

# Create DID-Linked Resource

Using the `/resource/create` API, users are able to create **custom DID-Linked Resources**, including:

1. Schemas
2. Credential Definitions
3. Trust Registries
4. Status Lists
5. Logos associated with DIDs
6. Governance files

## Step 1: Set up your account

Make sure you have set up your account with cheqd Studio and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Studio and log in to start using the APIs.</td><td><a href="../../getting-started/get-started/set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Create a DID

Before you can create a DID-Linked Resource, you need to create a _**"parent"**_ DID which is used to link the Resource on-ledger. Use the API in the page below to create a DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../dids/create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Learn more about DID-Linked Resources</strong></mark></td><td>Understand the context and technical composition of cheqd's DID-Linked Resources.</td><td><a href="understanding-dlrs/">understanding-dlrs</a></td></tr></tbody></table>

## Step 3. Create your Resource content and save the file locally

DID-Linked Resources can be any type of file or content that is **below \~45kb in size**.

For the purpose of an example, lets use an AnonCreds schema (JSON file) as the resource:

```json
{
  "name": "degreeSchema",
  "version": "1.5.7",
  "attrNames": ["name", "age", "degree", "grade"]
 }
```

Save this file locally and call it something like `resource.json.`

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

## Step 5: Set a consistent name and type

Resources are grouped by having identical **names** and **types**. This means if you want to create a new version of the same Resource, you will need to **specify the same name and type in the following request**.&#x20;

## Step 6: Populate the request inputs and hit the API

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/resource/create/{did}" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}
