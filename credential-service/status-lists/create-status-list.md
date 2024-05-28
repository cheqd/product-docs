---
description: Create Status List 2021 Resource on cheqd.
---

# Create Status List

Users are able to create [Verifiable Credential Status List v2021](https://www.w3.org/TR/vc-status-list/) entries on-ledger, in order to revoke or suspend Verifiable Credentials. This is a derivation from the core spec made by cheqd to support a more decentralized and resilient approach to storing Status Lists.

## Step 1: Set up your account

Make sure you have set up your account with cheqd Studio and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Studio and log in to start using the APIs.</td><td><a href="../set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Create a DID

Before you can create a Status List, you need to create a DID which is used to link the Status List on-ledger. Use the API in the page below to create a DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

## Step 3: Select the unencrypted Status List API

When [creating a Status List](./), a user will have the following options:&#x20;

<details>

<summary>/credential-status/create/encrypted</summary>

Using this API will encrypt the bitstring of the Status List, meaning that a set of Payment Conditions will need to be met in order to access the Status List contents.

</details>

<details>

<summary>/credential-status/create/unencrypted</summary>

Using this API will create a regular Status List on-ledger, where the contents of the Status List are visible to any users who queries the blockchain.&#x20;

</details>

For the purpose of this tutorial, we will assume the user is creating an unencrypted Status List. For encrypted Status Lists, [follow the tutorial here](../payments/charge.md).

## Step 4: Create unencrypted Status List

Using the `/credential-status/create/unencrypted` API, users have two options for creating an encrypted Status List on-ledger:

1. **Filling out a simple form** using the `application/x-www-url-form-encoded` option on the Swagger UI.
2. **Compiling a Status List payload yourself** using the `application/json` option on the Swagger UI.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/credential-status/create/unencrypted" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

### Option 1. Choose from a few variables and we will compile the Status List for you

This is the easiest way to create unencrypted Status Lists on cheqd and is recommended for users who are not overly familiar with compiling JSON objects.&#x20;

Using the `application/x-www-url-form-encoded` option on the Swagger UI, users are able to choose between the following variables and options to create an unencrypted Status List on-ledger:

<details>

<summary>statusPurpose (required)</summary>

* revocation (creates a Status List where each entry refers to revocation status)
* suspension (creates a Status List where each entry refers to suspension status. Note that suspended Credentials may become unsuspended).

</details>

<details>

<summary>did (required)</summary>

Enter the Decentralized Identifier (DID) of the Status List publisher [created in Step 2](create-status-list.md#step-2-create-a-did). For example:

```json
did:cheqd:testnet:0ff9df5d-653b-4f77-a66c-0035abc34d63
```

</details>

<details>

<summary>statusListName (required)</summary>

This is the name of your Status List. This will also need to be kept consistent for all future updates of the same Status List. For example:

```json
employmentCredentialRevocationList
```

</details>

<details>

<summary>length (optional)</summary>

The length of the Status List to be created. The default and minimum length is 140000 which is 16kb. For example:

```json
140000
```

</details>

<details>

<summary>encoding (optional)</summary>

The encoding format of the encrypted StatusList DID-Linked Resource to be created.&#x20;

* base64url
* base64
* hex

</details>

<details>

<summary>statusListVersion (optional)</summary>

A user set value to represent the version of the Status List. For example:

```json
1.0
```

</details>

<details>

<summary>alsoKnownAs (optional)</summary>

A user set field to assign a set of alternative URIs where the DID-Linked Resource can be fetched from. For example:

```json
https://www.foo.com
```

</details>

### Option 2. Publish a JSON payload yourself

Instead of using simple form variables, you can create an encrypted Status List using a JSON payload yourself using the `application/json` option on the Swagger UI.&#x20;

#### Request format

An example of the JSON payload needed to be submitted is below:

```json
{
  "did": "did:cheqd:testnet:7c2b990c-3d05-4ebf-91af-f4f4d0091d2e",
  "statusListName": "cheqd-employee-credentials",
  "length": 140000,
  "encoding": "base64url"
}
```

## Step 5: Hit the API

Once the Issuer has populated the requisite information for the unencrypted Status List request, they can use the API below to submit it to the ledger.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/credential-status/create/unencrypted" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}
