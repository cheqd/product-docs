---
description: Create a DID using the did:cheqd method
---

# Create Issuer DID

## Create Decentralized Identifiers (DIDs)

To create a cheqd DID and associated DID Document there are two ways of building the payload for the request:

### Step 1: Set up your account

Make sure you have set up your account with Credential Service and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Credential Service and log in to start using the APIs.</td><td><a href="../set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

### Step 2: Compile your DID Document

#### Option 1. Choose from a few variables and we will compile the DID for you

This is the easiest way to create DIDs on cheqd and is recommended for those who are not overly familiar with compiling DID Documents.&#x20;

Simply, copy the code below, based on the variables of your choice.

**Request format:**

```json
{
  "options": {
    "methodSpecificIdAlgo": "uuid",
    "network": "testnet"
  },
  "secret": {
    "verificationMethod": {
      "id": "key-1",
      "type": "Ed25519VerificationKey2018"
    }
  }
}
```

Here, you are able to choose between the following variables to compile your DID:

<details>

<summary>methodSpecificIdAlgo</summary>

* "uuid" - this is a Universally Unique Identifier (recommended)
* "base58btc" - this is an identifier which is commonly used for Hyperledger Indy transactions

</details>

<details>

<summary>network</summary>

* "testnet" (recommended for testing)
* "mainnet" (recommended for production)

</details>

<details>

<summary>verificationMethod / type</summary>

* "Ed25519VerificationKey2018" (recommended)
* "Ed25519VerificationKey2020"
* "JSONWebKey2020"

</details>

From this request, we will create a DID, compile a DIDDoc and return it as a response.

#### Option 2. Publish a DID Document body yourself

Instead of generating a DID Document using simple parameters, you can create a fully formatted DID Document yourself. Before, submitting a manually created DID, you will need to have [created a set of identity keys](create-subject-did.md) to input the key material into the DID document.

#### Step 1: Create a new keypair

Use the [`/key/create` API](create-subject-did.md) to generate a new keypair within the Credential Service key management store. Copy the "publicKeyHex".&#x20;

#### Step 2: Utilise the DID Document template helper&#x20;

To simplify this process of formatting a DID Document using your own keys, we've created a [Helper Tool in our DID Registrar here](https://did-registrar.cheqd.net/api-docs/#/Cheqd%20Helpers/get\_did\_document). Simply paste in your publicKeyHex and choose the variables to compile your DID Document template.

#### Step 3: Paste the response

Within the `/did/create` JSON payload, paste the response of your DID Document template, with your own signing keys.&#x20;

**Request format:**

```json
{
  "didDocument": {
    "id": "did:cheqd:testnet:0ff9df5d-653b-4f77-a66c-0035abc34d63",
    "controller": [
      "did:cheqd:testnet:0ff9df5d-653b-4f77-a66c-0035abc34d63"
    ],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:0ff9df5d-653b-4f77-a66c-0035abc34d63#key-1",
        "type": "JsonWebKey2020",
        "controller": "did:cheqd:testnet:0ff9df5d-653b-4f77-a66c-0035abc34d63",
        "publicKeyJwk": {
          "crv": "Ed25519",
          "kty": "OKP",
          "x": "BFSLOxwMJgpmWRtTUuo0JAvz6VXGp4WDDcN0dFfCQKo"
        }
      }
    ],
    "authentication": [
      "did:cheqd:testnet:0ff9df5d-653b-4f77-a66c-0035abc34d63#key-1"
    ]
  }
}

```

### Step 3: Create a DID using the API below:

Hit the API below to create your DID for issuing Verifiable Credentials.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/did/create" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## List DIDs associated with your account

After creating a DID or multiple DIDs, users can list all the created DIDs associated with their account. Using the `/did/list` API.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/did/list" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}
