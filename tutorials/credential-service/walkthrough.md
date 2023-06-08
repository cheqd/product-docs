# Walkthrough

## Summary

These instructions will help developers create DIDs and issue Verifiable Credentials on cheqd using the Credential Service.

## 1. Understanding your account

The GET /account functionality provides two important pieces of information:

1.  The **cheqd wallet address** for your account (this is used to pay for the on-ledger transactions such as creating DIDs and Resources).\


    This will look something like this:\


    ```json
    cheqd1xddzwh8mlckldlfdytfacwru6004zvvr2dnlka
    ```
2. Your **customer ID** (you don't need to worry about this)

The GET account API becomes important when you are using an **Credential Service in a mode where the APIs require authentication to access**. This is because you will need to find your cheqd wallet address in order **to top it up with CHEQ tokens to pay for transactions**.&#x20;

In a mode where the APIs are **unauthenticated**, you will not need to worry about the /account function, as you will be using a default account with CHEQ tokens already available on testnet.&#x20;

## 2. Adding CHEQ tokens to your account

{% hint style="info" %}
This is only applicable if you are using Credential Service with an Authenticated API
{% endhint %}

If you are using an authenticated API and need to top up your account with CHEQ tokens, there are three steps you need to follow:

1. Please follow the [tutorial here to setup your Keplr wallet](../../guides/wallets/keplr-setup.md).&#x20;
2. If you are using testnet, you can add free tokens to your account [via our testnet faucet](https://testnet-faucet.cheqd.io/)
3. If you are using mainnet, you will need to [purchase CHEQ tokens](https://cheqd.io/get-involved/).&#x20;
4. You will need to send tokens from your Keplr account to your cheqd wallet address for using the Credential Service.&#x20;

## Understanding Creating cheqd DIDs

To create a cheqd DID there are two ways of building the payload for the request:

#### Simply choose from a few variables and we will compile the DID for you

Copy the code below, based on the variables of your choice.

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

* "uuid"
* "base58btc"

</details>

<details>

<summary>network</summary>

* "testnet"
* "mainnet"

</details>

<details>

<summary>verificationMethod / type</summary>

* "Ed25519VerificationKey2018"
* "Ed25519VerificationKey2020"
* JSONWebKey2020

</details>

From this request, we will create a DID, compile a DIDDoc and return it as a response.

#### Publish a DID Document body yourself

**Request format:**

```json
{
  "didDocument": {
    "authentication": [
      "did:cheqd:testnet:ca9ff47c-0286-4614-a4be-8ffa83911e09#key-1"
    ],
    "controller": [
      "did:cheqd:testnet:ca9ff47c-0286-4614-a4be-8ffa83911e09"
    ],
    "id": "did:cheqd:testnet:ca9ff47c-0286-4614-a4be-8ffa83911e09",
    "verificationMethod": [
      {
        "controller": "did:cheqd:testnet:ca9ff47c-0286-4614-a4be-8ffa83911e09",
        "id": "did:cheqd:testnet:ca9ff47c-0286-4614-a4be-8ffa83911e09#key-1",
        "publicKeyMultibase": "z6Mkt9Vg1a1Jbg5a1NkToUeWH23Z33TwGUua5MrqAYUz2AL3",
        "type": "Ed25519VerificationKey2020"
      }
    ]
  }
}
```

#### Check your DID is live

You can check your DID on the [Universal Resolver](https://dev.uniresolver.io/) or by going to `https://resolver.cheqd.net/1.0/identifiers/{yourDID}`

## Creating a "subject" DID

We currently support two types of "subject" DIDs:

* did:key
* did:vda

With the latter, you can setup your did:vda subject DID on your [Verida wallet](https://www.verida.io/). Using the Credential Service, you will be able to send credentials to your Verida wallet and use it to store and securely back them up.

## Understanding Issuing Credentials

Once you have created your cheqd "issuer" DID and you know the subject DID you want to issue to, you are now able to begin issuing Credentials.

Within the JSON object below, you will need to input this information, as well as the "attributes" which you want to issue in the Credential.&#x20;

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
    "id": "did:cheqd:mainnet:8790869e-624c-4325-98df-c30e0e473228"
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

## Verifying a Credential

Once you have issued your credential and have a JWT as part of the credential proof, you can use the /verify API to check that the JWT has not been tampered.

## Creating a Resource

We will soon update the Credential Service to be able to create specific DID-Linked Resources such as StatusLists and Trust Registries.&#x20;
