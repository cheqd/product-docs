---
description: >-
  Create an 'Issuer' Decentralized Identifier (DID) with the did:cheqd DID
  method using cheqd Studio.
---

# Create Issuer DID

In cheqd Studio, you can easily create and publish a `did:cheqd` DID to the **cheqd testnet or mainnet**, anchoring it on-ledger with associated public keys and metadata. This DID can then be used to:

* Sign and issue verifiable credentials.
* Establish a trusted identity on cheqd for entities like organisations, digital products or AI Agents.
* Serve as the parent identifier for DID-Linked Resources (e.g. status lists, trust registries).

This tutorial walks through the process of creating an Issuer DID using cheqd Studio's API or interface, including how to configure your keys, DID Document, and optional service endpoints.

> 🔐 Once registered, the DID becomes publicly resolvable and forms the cryptographic foundation of your identity as an issuer in decentralized ecosystems.

## Step 1: Set up your account

Make sure you have set up your account with **cheqd Studio** and have generated an API key to authenticate with our APIs, using our guides below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set Up Your Account</strong></mark></td><td>Set up your account with <strong>cheqd Studio</strong> and get your API key to start using the APIs.</td><td><a href="../../getting-started/studio/set-up-account.md">set-up-account.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create API Keys</strong></mark></td><td>Create one or more API keys used to authenticate with the cheqd Studio APIs.</td><td><a href="../../getting-started/studio/api-keys.md">api-keys.md</a></td></tr></tbody></table>

## Step 2: Create a DID and associated DID Document

Using the `/did/create` API, users have two options for creating a `did:cheqd` DID and associated DID Document on-ledger:

1. **Filling out a simple form** using the `application/x-www-url-form-encoded` or `application/json` option within an API client of your choice.
2. **Compiling a full DID Document body yourself** using the `application/json` option, using already created identity keys, within an API client of your choice.

### Option 1. Choose from a few variables and we will compile the DID for you

This is the easiest way to create DIDs on cheqd and is recommended for users who are not overly familiar with compiling DID Documents.&#x20;

#### Using application/x-www-url-form-encoded

Using the `application/x-www-url-form-encoded` option, users are able to choose between the following variables to compile your DID:

<details>

<summary>network (required)</summary>

* "testnet" (recommended for testing)
* "mainnet" (recommended for production)

</details>

<details>

<summary>methodSpecificIdAlgo (required)</summary>

* "uuid" - this is a Universally Unique Identifier (recommended)
* "base58btc" - this is an identifier which is commonly used for Hyperledger Indy transactions

</details>

<details>

<summary>verificationMethod / type (required)</summary>

* "Ed25519VerificationKey2018" (recommended)
* "Ed25519VerificationKey2020"
* "JSONWebKey2020"

</details>

<details>

<summary>didDocument (optional)</summary>

This input field contains either a complete DID document, or an incremental change (diff) to a DID document. For example:

```json
{
  "service": [
    {
      "id": "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0#service-1",
      "type": "LinkedDomains",
      "serviceEndpoint": [
        "https://example.com"
      ]
    }
  ]
}
```

</details>

From this request, cheqd Studio **will automatically create and publish a DID and associated DID Document to the ledger** and return it as a response.

<details>

<summary>Expected response format</summary>

```json
{
  "did": "did:cheqd:testnet:ca029356-69dc-4cb4-a4c6-13b959ffaad6",
  "keys": [
    {
      "kid": "1a07cb379cd6f2d9acddf9a6d6af4e930d5696818a38020bde2b6ead5cd85039",
      "kms": "postgres",
      "type": "Ed25519",
      "publicKeyHex": "1a07cb379cd6f2d9acddf9a6d6af4e930d5696818a38020bde2b6ead5cd85039",
      "meta": {
        "algorithms": [
          "EdDSA",
          "Ed25519"
        ]
      },
      "controller": "did:cheqd:testnet:ca029356-69dc-4cb4-a4c6-13b959ffaad6"
    }
  ],
  "services": [],
  "provider": "did:cheqd:testnet",
  "controllerKeyRefs": [
    "1a07cb379cd6f2d9acddf9a6d6af4e930d5696818a38020bde2b6ead5cd85039"
  ],
  "controllerKeys": [
    {
      "kid": "1a07cb379cd6f2d9acddf9a6d6af4e930d5696818a38020bde2b6ead5cd85039",
      "kms": "postgres",
      "type": "Ed25519",
      "publicKeyHex": "1a07cb379cd6f2d9acddf9a6d6af4e930d5696818a38020bde2b6ead5cd85039",
      "meta": {
        "algorithms": [
          "EdDSA",
          "Ed25519"
        ]
      },
      "controller": "did:cheqd:testnet:ca029356-69dc-4cb4-a4c6-13b959ffaad6"
    }
  ],
  "controllerKeyId": "1a07cb379cd6f2d9acddf9a6d6af4e930d5696818a38020bde2b6ead5cd85039"
}
```

</details>

#### Using application/json

Alternatively, you can use the `application/json` option and pass only a few specific inputs, for example:

```json
{
  "network": "testnet",
  "identifierFormatType": "uuid",
  "options": {
    "verificationMethodType": "Ed25519VerificationKey2018"
  }
}
```

Or, if you have [created a keypair already](create-identity-keys.md) that you want to use, you can reference the created key ID, `kid`,  in the request:

```json
{
  "network": "testnet",
  "identifierFormatType": "uuid",
  "options": {
    "key": "8255ddadd75695e01f3d98fcec8ccc7861a030b317d4326b0e48a4d579ddc43a", // Pass kid if you have created a key separately
    "verificationMethodType": "Ed25519VerificationKey2018"
  }
}
```

{% hint style="info" %}
Note that if you are passing a `kid` that is already created, you must also specify the `verificationMethodType` within `options`.
{% endhint %}

Using the `application/json` option, users are able to choose between the following variables to compile your DID:

<details>

<summary>network (required)</summary>

* "testnet" (recommended for testing)
* "mainnet" (recommended for production)

</details>

<details>

<summary>identifierFormatType (required)</summary>

* "uuid" - this is a Universally Unique Identifier (recommended)
* "base58btc" - this is an identifier which is commonly used for Hyperledger Indy transactions

</details>

<details>

<summary>verificationMethodType (required)</summary>

* "Ed25519VerificationKey2018" (recommended)
* "Ed25519VerificationKey2020"
* "JSONWebKey2020"

_Note that this should be nested under `options`_

</details>

<details>

<summary>key (optional)</summary>

* "8255ddadd75695e01f3d98fcec8ccc7861a030b317d4326b0e48a4d579ddc43a"
* This is a `kid` that should have been created using our [Key Create API](create-identity-keys.md).

_Note that this should be nested under `options`_

</details>

### Option 2. Publish a fully compiled DID Document body yourself

Instead of generating a DID Document using simple parameters, you can create a fully formatted DID Document yourself. Before, submitting a manually created DID, you will need to have [created a set of identity keys](create-subject-did.md) to input the key material into the DID document.

#### Step 1: Create a new keypair

Use the [`/key/create` API](create-subject-did.md) to generate a new keypair within the Credential Service key management store. Copy the `publicKeyHex`.&#x20;

#### Step 2 (option 1): Utilise the DID Document template helper&#x20;

To simplify this process of formatting a DID Document using your own keys, we've created a [Helper Tool in our DID Registrar here](https://did-registrar.cheqd.net/api-docs/#/Cheqd%20Helpers/get_did_document). Simply paste in your `publicKeyHex` and choose the variables to compile your DID Document template.

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

#### Step 2 (option 2) Use application/json options

Alternatively, you can use the `application/json` request format below.&#x20;

You can use the `kid` created from Step 1 within the options section, and then compile the remainder of tour DID Document.

```json
"options": {
    "key": "8255ddadd75695e01f3d98fcec8ccc7861a030b317d4326b0e48a4d579ddc43a",
    "verificationMethodType": "Ed25519VerificationKey2018"
  },
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1"
    ],
    "id": "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0",
    "controller": [
      "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0"
    ],
    "authentication": [
      "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0#key-1"
    ],
    "service": [
      {
        "id": "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0#service-1",
        "type": "LinkedDomains",
        "serviceEndpoint": [
          "https://example.com"
        ]
      }
    ]
  }
}
```

## Step 3: Hit execute on the API

Hit execute on the API below to create your `did:cheqd` DID and associated DID Document.

{% openapi-operation spec="cheqd-studio-api" path="/did/create" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

## List DIDs associated with your account

After creating a DID or multiple DIDs, users can list all the created DIDs associated with their account. Using the `/did/list` API.&#x20;

{% openapi src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/did/list" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endopenapi %}

## Alternatives

Below are a list of alternatives for creating cheqd DIDs.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK which <strong>supports the European Architecture and Reference Framework (ARF)</strong> <strong>standards as well as AnonCreds</strong> with full cheqd support for DIDs. </td><td><a href="../../sdk/credo/">credo</a></td><td><a href="../../.gitbook/assets/Credo.png">Credo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>ACA-Py</strong></mark></td><td>ACA-Py plugin supports full cheqd support for DIDs as well as Verifiable Credentials. </td><td><a href="../../sdk/aca-py/">aca-py</a></td><td><a href="../../.gitbook/assets/ACA-Py.png">ACA-Py.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> supports JSON, JSON-LD credentials as well as cheqd Credential Payments in an SDK.</td><td><a href="../../sdk/veramo/">veramo</a></td><td><a href="../../.gitbook/assets/veramo logo.png">veramo logo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the Universal Registrar.</td><td><a href="../../advanced/did-registrar/">did-registrar</a></td><td><a href="../../.gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id Community Stack</strong></mark></td><td>Walt.id Community Stack is an SDK that supports the <strong>European Architecture and Reference Framework (ARF)</strong> standards for identity, with full cheqd support. </td><td><a href="../../sdk/walt-id.md">walt-id.md</a></td><td><a href="../../.gitbook/assets/walt.id logo.png">walt.id logo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos CLI which directly communicates with the cheqd network. This should only be used for testing environments.</td><td><a href="../../advanced/tooling/cheqd-node-cli/">cheqd-node-cli</a></td><td><a href="../../.gitbook/assets/cosmos and cheqd.png">cosmos and cheqd.png</a></td></tr></tbody></table>
