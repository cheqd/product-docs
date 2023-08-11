# Create DID-Linked Resource

## Create DID-Linked Resources (DLRs)

Using the `/resource/create` API, users are able to create **custom DID-Linked Resources**, including:

1. Schemas
2. Credential Definitions
3. Trust Registries
4. Logos associated with DIDs
5. Governance files

### Step 1: Create a DID

Follow the page below to create a DID.

* [Create a DID](create-dlr.md)

### Step 2. Create your Resource content and save the file locally

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

### Step 3: Encode the file in Base64

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

### Step 4: Create a consistent name and type

Resources are grouped by having identical **names** and **types**. This means if you want to create a new version of the same Resource, you will need to **specify the same name and type in the following request**.&#x20;

### Step 5: Populate the request inputs and hit the API

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/resource/create/{did}" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

