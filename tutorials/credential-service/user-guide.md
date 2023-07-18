---
description: Simple guide for using the cheqd Credential Service
---

# User Guide

These instructions will help developers create DIDs and issue Verifiable Credentials on cheqd using the Credential Service. This Walkthrough should be read in conjunction with [our APIs defined in our Swagger File here](https://credential-service.cheqd.net/swagger/).

## Verifiable Credentials

Verifiable Credentials are a tamper-evident data format for asserting a set of claims about a subject. Using the cheqd Credential Service, it is possible to issue Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code.&#x20;

{% hint style="info" %}
Once you have [created your cheqd `issuer` DID](user-guide.md#did-create) and you know the `subject` DID you want to issue to, you are now able to begin issuing Credentials.
{% endhint %}

Within the JSON object of the API request, you will need to input the `issuer` and `subject` information, as well as the `attributes` which you want to issue in the Credential.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/issue" method="post" expanded="false" fullWidth="false" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

Once you have issued your credential and have a JWT as part of the credential proof, you can use the `/verify` API to check that the JWT has not been tampered.

Simply paste the JWT or the full credential body which you obtain from the response of the `/issue` Credential API into the request field, and the API will give you a response including the following verification policies:

1. Whether the Credential has been tampered
2. Whether the Credential has a valid issuance date
3. Whether the Credential has expired
4. Whether the Credential Status is valid

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/verify" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

Using cheqd's innovative DID-Linked Resource module, cheqd is able to support decentralized and scalable Verifiable Credential revocation and suspension. Using this API, the user is able to choose whether they would like to publish the revocation status to the cheqd ledger or elsewhere.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/revoke" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

Users are also able to suspend Verifiable Credentials. The difference between revocation and suspension is that **suspended Credentials may be unsuspended at a future date**; whereas, **revoked credentials are permanently revoked**.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/suspend" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/reinstate" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## Verifiable Presentations

A Verifiable Presentation is a collection of multiple Verifiable Credentials that are being presented by a `holder` to a `verifier`. In addition to checking whether the Credentials are untampered, Verifiable Presentation verification also checks that the `holder` subject DID is valid.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/presentation/verify" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## Account

Understanding the account APIs:

<details>

<summary>AUTH is enabled</summary>

If authentication is enabled, then the user is required to provide a JWT Bearer token in the authorization header to access the Credential Service via a specific **account**. This guards the API from unauthorized access and is recommend for production environments.

The user is able to provide the JWT bearer token via logging into [LogTo](https://logto.io/) (the service we use for authentication).&#x20;

With AUTH enabled:

* `customer_id` is used as an identifier for the particular customer using the Credential Service. It is generated as a sub-field of the JWT token used in the authorization header.
* `cheqd_account` is used to pay for identity transactions on either `testnet` or `mainnet`. A cheqd account is automatically generated when a new `customer_id` is generated.

Users will need to add our token, CHEQ, to their `cheqd_account` on either `testnet` or `mainnet` in order to be able to use the ledger-based identity functionality on Credential Service, such as creating DIDs and DID-Linked Resources. This can be carried out by following the instructions in Section 2 below.&#x20;

</details>

<details>

<summary>AUTH is disabled</summary>

If authentication is disabled, the user **does not** need to provide a JWT Bearer token to access the Credential Service. The user can simply start using the APIs without authentication. This is recommended to **only be used for testing environments**.&#x20;

With AUTH disabled:

* `customer_id` is a default account, which is the same for all users of Credential Service.
* `cheqd_account` is also a default fee-payer account, which can be used to create `testnet` transactions on the Credential Service for demo and testing purposes.&#x20;

</details>

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/account" method="get" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/account" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## Identity keys

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/key/create" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/key/{kid}" method="get" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## Creating a Subject DID

We currently support two types of `subject` DIDs:

* did:key
* did:vda

With the former, you can follow the [did:key specification](https://w3c-ccg.github.io/did-method-key/) to create a subject DID based on a generated keypair.&#x20;

With the latter, you can setup your did:vda subject DID on your [Verida wallet](https://www.verida.io/). Using the Credential Service, you will be able to send credentials to your Verida wallet and use it to store and securely back them up.

## Decentralized Identifiers (DIDs)

To create a cheqd DID there are two ways of building the payload for the request:

### Option 1. Choose from a few variables and we will compile the DID for you

This is the easiest way to create DIDs on cheqd and is recommended for those who are not overly familiar with compiling DIDDocuments.&#x20;

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

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/did/create" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/did/update" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/did/deactivate/{did}" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/did/list" method="get" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/did/{did}" method="get" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## DID-Linked Resources (DLRs)

Users are able to create custom DID-Linked Resources, including:

1. Schemas
2. Credential Definitions
3. Trust Registries
4. Logos associated with DIDs
5. Governance files

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/resource/create/{did}" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## Verifiable Credential Status Lists

Using the DID-Linked Resources (DLR) implementation, cheqd is able to natively support [Verifiable Credential Status List v2021](https://www.w3.org/TR/vc-status-list/) on-ledger.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/create" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/publish" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/search" method="get" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/update" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/check" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}
