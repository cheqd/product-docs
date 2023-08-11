# Create Issuer DID

## Create Decentralized Identifiers (DIDs)

To create a cheqd DID and associated DID Document there are two ways of building the payload for the request:

### Option 1. Choose from a few variables and we will compile the DID for you

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

### Option 2. Publish a DID Document body yourself

**Request format:**

```json
{
  "didDocument": {
    "authentication": [
      "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0#key-1"
    ],
    "controller": [
      "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0"
    ],
    "id": "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0",
    "verificationMethod": [
      {
        "controller": "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0",
        "id": "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0#key-1",
        "publicKeyBase58": "BTJiso1S4iSiReP6wGksSneGfiKHxz9SYcm2KknpqBJt",
        "type": "Ed25519VerificationKey2018"
      }
    ]
  }
}

```

## Get started using the API below:

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/swagger-host/src/static/swagger.json" path="/did/create" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/swagger-host/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/swagger-host/src/static/swagger.json)
{% endswagger %}

## List DIDs associated with your account

Using the `/did/list` API, users can list all the created DIDs associated with their account.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/swagger-host/src/static/swagger.json" path="/did/list" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/swagger-host/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/swagger-host/src/static/swagger.json)
{% endswagger %}
