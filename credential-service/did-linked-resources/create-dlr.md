---
description: Create a DID-Linked Resource associated with a cheqd DID over REST API
---

# Create DID-Linked Resource

## Create DID-Linked Resources (DLRs)

Using the `/resource/create` API, users are able to create **custom DID-Linked Resources**, including:

1. Schemas
2. Credential Definitions
3. Trust Registries
4. Logos associated with DIDs
5. Governance files

### Step 1: Set up your account

Make sure you have set up your account with Credential Service and are logged in, using our guide below:

* [Set up your account](../set-up-account.md)

### Step 2: Create a DID

Before you can create a DID-Linked Resource, you need to create a _**"parent"**_ DID which is used to link the Resource on-ledger. Use the API in the page below to create a DID:

* [Create a DID](create-dlr.md)
* [Learn more about DID-Linked Resources](understanding-dlrs/)

### Step 3. Create your Resource content and save the file locally

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

### Step 4: Encode the file in Base64

Prepare a file with resource and encode it into `base64`. On Unix systems, you can use the following command input:

```bash
$ base64 -w 0 resource.json
<path-to-the-resource-file>
```

Expected output:

```bash
$ base64 -w 0 resource.json
SGVsbG8sIHdvcmxk
```

### Step 5: Create a consistent name and type

Resources are grouped by having identical **names** and **types**. This means if you want to create a new version of the same Resource, you will need to **specify the same name and type in the following request**.&#x20;

### Step 6: Populate the request inputs and hit the API

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/resource/create/{did}" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

