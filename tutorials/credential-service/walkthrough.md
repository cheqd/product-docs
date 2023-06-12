# Walkthrough

## Summary

These instructions will help developers create DIDs and issue Verifiable Credentials on cheqd using the Credential Service.

## 1. Understanding your account

There are two options for authentication with the cheqd Credential Service:

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

## 2. Adding CHEQ tokens to your account

{% hint style="info" %}
This is only applicable if you are using Credential Service **with AUTH enabled.**
{% endhint %}

If you need to setup a wallet which can hold CHEQ tokens and top up your Credential Service `cheqd_account`, there are a few steps you need to follow:

<details>

<summary>Mainnet</summary>

* First you need to setup a wallet which is able to hold CHEQ tokens. We recommend using [Leap Wallet](https://www.leapwallet.io/) which natively supports all CHEQ transactions in a browser plugin or on mobile. Alternatively, follow the [tutorial here to setup your Keplr wallet](../../guides/wallets/keplr-setup.md).
* You will then need to [purchase CHEQ tokens](https://cheqd.io/get-involved/) from either a Centralized Exchange (CEX) or exchanging tokens on a Decentralized Exchange (DEX).
* You will need to send tokens from your new cheqd Wallet account to your `cheqd_account` address for using the Credential Service.

</details>

<details>

<summary>Testnet</summary>

* First you need to setup a wallet which is able to hold CHEQ tokens. We recommend using [Leap Wallet](https://www.leapwallet.io/) which natively supports all CHEQ transactions in a browser plugin or on mobile. Alternatively, follow the [tutorial here to setup your Keplr wallet](../../guides/wallets/keplr-setup.md).
* You can add free CHEQ tokens to your account [via our testnet faucet](https://testnet-faucet.cheqd.io/) by inputting your cheqd account address.
* You may need to send tokens from your new cheqd Wallet account to your `cheqd_account` address for using the Credential Service.

</details>

## 3. Understanding Creating cheqd DIDs

To create a cheqd DID there are two ways of building the payload for the request:

#### Option 1. Choose from a few variables and we will compile the DID for you

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

#### Option 2. Publish a DID Document body yourself

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

#### Check your DID is live

You can check your DID on the [Universal Resolver](https://dev.uniresolver.io/) or by going to `https://resolver.cheqd.net/1.0/identifiers/{yourDID}`

## 4. Creating a "subject" DID

We currently support two types of "subject" DIDs:

* did:key
* did:vda

With the latter, you can setup your did:vda subject DID on your [Verida wallet](https://www.verida.io/). Using the Credential Service, you will be able to send credentials to your Verida wallet and use it to store and securely back them up.

## 5. Understanding Issuing Credentials

Once you have created your cheqd "issuer" DID and you know the subject DID you want to issue to, you are now able to begin issuing Credentials.

Within the JSON object below, you will need to input this information, as well as the "attributes" which you want to issue in the Credential.

**Request format:**

```json
{
  "@context": [
    "https://schema.org/Person" // you can add a reference to a schema for the credentials you are issuing
  ],
  "attributes": {  // describe the claims and attributes you want to issue in your credential
    "gender": "male",
    "name": "Bob"
  },
  "format": "jwt",
  "issuerDid": "did:cheqd:testnet:ca9ff47c-0286-4614-a4be-8ffa83911e09", // input your cheqd "issuer" DID here
  "subjectDid": "did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK", // input the subject DID here (did:key or did:vda)
  "type": [
    "Person" // you can reference the type of credential that matches your schema
  ]
}
```

Going forward, we will make it easier to issue credentials with simple input fields, without needing to format a full JSON body yourself.&#x20;

Within the response, after issuing the credential you will find a JWT with which you can verify the integrity of the Credential.

<details>

<summary>Example response</summary>

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://schema.org/Person"
  ],
  "credentialSubject": {
    "gender": "male",
    "id": "did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK",
    "name": "Bob"
  },
  "issuanceDate": "2023-03-22T07:00:47.749Z",
  "issuer": {
    "id": "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0"
  },
  "proof": {
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vc2NoZW1hLm9yZy9QZXJzb24iLCJodHRwczovL3ZlcmFtby5pby9jb250ZXh0cy9wcm9maWxlL3YxIl0sInR5cGUiOlsiVmVyaWZpYWJsZUNyZWRlbnRpYWwiLCJQZXJzb24iXSwiY3JlZGVudGlhbFN1YmplY3QiOnsiaWQiOiJkaWQ6a2V5Ono2TWtoYVhnQlpEdm90RGtMNTI1N2ZhaXp0aUdpQzJRdEtMR3Bibm5FR3RhMmRvSyIsImdlbmRlciI6Im1hbGUiLCJuYW1lIjoiQm9iIn19LCJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vc2NoZW1hLm9yZy9QZXJzb24iLCJodHRwczovL3ZlcmFtby5pby9jb250ZXh0cy9wcm9maWxlL3YxIl0sInR5cGUiOlsiVmVyaWZpYWJsZUNyZWRlbnRpYWwiLCJQZXJzb24iXSwiaXNzdWVyIjp7ImlkIjoiZGlkOmNoZXFkOnRlc3RuZXQ6MzFmYjVmYzEtMGQyNC00M2Y5LTg3MGYtYmU4YjZiNmI5YzMzIn0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImlkIjoiZGlkOmtleTp6Nk1raGFYZ0JaRHZvdERrTDUyNTdmYWl6dGlHaUMyUXRLTEdwYm5uRUd0YTJkb0siLCJnZW5kZXIiOiJtYWxlIiwibmFtZSI6IkJvYiJ9LCJpc3N1YW5jZURhdGUiOiIyMDIzLTA1LTEyVDEyOjQ4OjE2LjU1NloiLCJzdWIiOiJkaWQ6a2V5Ono2TWtoYVhnQlpEdm90RGtMNTI1N2ZhaXp0aUdpQzJRdEtMR3Bibm5FR3RhMmRvSyIsIm5iZiI6MTY4Mzg5NTY5NiwiaXNzIjoiZGlkOmNoZXFkOnRlc3RuZXQ6MzFmYjVmYzEtMGQyNC00M2Y5LTg3MGYtYmU4YjZiNmI5YzMzIn0.MP2MvNnjxGXNKqT4itdEfRBd82nad30-a4-BupjskETb-10-Kyj94b5FDMxdwe3HQ39ztNwFRj3TPFiZS8z1Bw",
    "type": "JwtProof2020"
  },
  "type": [
    "VerifiableCredential",
    "Person"
  ]
}
```

</details>

## 6. Verifying a Credential

Once you have issued your credential and have a JWT as part of the credential proof, you can use the `/verify` API to check that the JWT has not been tampered.

Simply paste the JWT which you obtain from the response of the `/issue` Credential API into the request field, and the API will give you a response indicating whether the Credential JWT has maintained data integrity.&#x20;

## 7. Creating a Resource

You are able to create a DID-Linked Resource associated with your DID. This may be useful for creating Credential schemas, Status Lists or Trust Registries associated with a particular DID.&#x20;

In order to create a resource, you need to provide the following inputs:

1. **An existing DID**

Please enter the existing DID under which you would like your resources to be linked and associated.&#x20;

2. **Resource file**

You need to provide a base 64 encoded file of the actual resource data in the `"data"` request input. This file should generally be less than 100kb in size.&#x20;

3. **Resource metadata**

You need to provide a resourceName in the `"name"` field and resourceType in the `"type"` field for the specific resource which can be used for linking multiple resources together.

Read more about the composition of DID-Linked Resources and how they may be useful for your particular use case in our [guide on understanding DID-Linked Resources](../../guides/did-linked-resources/).

