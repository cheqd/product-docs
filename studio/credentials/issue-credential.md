---
description: Issue W3C conformant Verifiable Credentials over REST API, using cheqd Studio.
---

# Issue a Verifiable Credential

Using the `/credential/create` API, it is possible to issue Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code.&#x20;

## Step 1: Set up your account

Make sure you have set up your account with cheqd Studio and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set Up Your Account</strong></mark></td><td>Set up your account with <strong>cheqd Studio</strong> and log in to start using the APIs.</td><td><a href="../../getting-started/studio/set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Create an Issuer DID

Before you can issue a Verifiable Credential, you need to create an Issuer DID which is used to sign the Credential payload. Use the API in the page below to create an Issuer DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

## (Optional) Step 3: Create a Subject DID

Again, before you issue a Verifiable Credential, you need to know to whom you are issuing it. If you need to create a Subject DID, you can take a look at the page here:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a Subject DID</strong></mark></td><td>Create an off-ledger <code>did:key</code> or <code>did:vda</code> Subject DID to receive a Verifiable Credential.</td><td><a href="../dids/create-subject-did.md">create-subject-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Setup Verida Wallet</strong></mark></td><td>Learn about setting up your Verida wallet to receive a <code>did:vda</code> address.</td><td><a href="verida.md">verida.md</a></td></tr></tbody></table>

{% hint style="info" %}
It is also possible to use a `did:cheqd`  DID for holders and subjects, but this is only recommended when the holder is not a natural person (e.g. is an AI Agent, organisation or object).
{% endhint %}

## Step 4: Compile your Credential body

Within the JSON object of the API request, you will need to input the `issuer` and `subject` information, as well as the `attributes` which you want to issue in the Credential. You may also want to add additional fields such as a `credentialSchema`.

{% openapi-operation spec="cheqd-studio-api" path="/credential/issue" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

Users have two options for compiling the Credential bodies and issuing Verifiable Credentials:

1. **Filling out a simple form** using the `application/x-www-url-form-encoded` option within an API client of your choice.
2. **Compiling a Credential body yourself** using the `application/json` option within an API client of your choice.

### Option 1. Choose from a few variables and we will compile the Credential body for you

This is the easiest way to issue Credentials and is recommended for users who are not overly familiar with compiling JSON objects.&#x20;

Using the `application/x-www-url-form-encoded` option, users are able to choose between the following variables and options to issue Verifiable Credentials:

<details>

<summary><strong>issuerDid (required)</strong></summary>

This is the DID of the Credential issuer, created in [Step 2](issue-credential.md#step-2-create-an-issuer-did). This needs to be a `did:cheqd` DID. For example:

```json
did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0
```

</details>

<details>

<summary><strong>subjectDid (required)</strong></summary>

This is the DID of the Credential subject, created in [Step ](issue-credential.md#step-2-create-an-issuer-did)[3](issue-credential.md#optional-step-3-create-a-subject-did). This needs to be a `did:key` or `did:vda` DID. For example:

```json
did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK
```

</details>

<details>

<summary><strong>attributes (required)</strong></summary>

These are the claims or attributes attested to within the Verifiable Credential. This must be a JSON object, following the [syntax defined in the Verifiable Credential Data Model](https://www.w3.org/TR/vc-data-model/). For example:

```json
{
  "name": "Bob",
  "gender": "male"
}
```

</details>

<details>

<summary><strong>credentialSchema (required for Verida wallet)</strong></summary>

This is the Schema which the Credential body takes the form of. For the Verida wallet, to display a credential, it needs to have a schema associated with it.&#x20;

```json
https://common.schemas.verida.io/health/pathology/tests/covid19/pcr/v0.1.0/schema.json
```

</details>

<details>

<summary><strong>@context (optional)</strong></summary>

This is an optional property that defines semantic information about the Credential, conforming to the [@contexts section of the Verifiable Credential Data Model](https://www.w3.org/TR/vc-data-model/#contexts). For example:

```json
https://www.w3.org/2018/credentials/v1
```

</details>

<details>

<summary><strong>type (optional)</strong></summary>

This is an optional property that defines information about the type of Verifiable Credential, conforming to the [types section of the Verifiable Credential Data Model](https://www.w3.org/TR/vc-data-model/#types). For example:

```json
VerifiableCredential
```

</details>

<details>

<summary><strong>expirationDate (optional)</strong></summary>

This is an optional property that defines information about the expiration date of a Verifiable Credential, conforming to the [expiration section of the Verifiable Credentials Data Model](https://www.w3.org/TR/vc-data-model/#expiration). For example:

```json
2023-06-08T13:49:28.000Z
```

</details>

<details>

<summary><strong>format (optional)</strong></summary>

Format of the Verifiable Credential. Defaults to VC-JWT.

* jwt (VC-JWT)
* lds (JSON-LD)

</details>

<details>

<summary><strong>credentialStatus (optional)</strong></summary>

`credentialStatus` properties for VC revocation or suspension. Takes `statusListName` and `statusListPurpose` as inputs. If you have already created a Status List, you can include the same inputs here to map this issued credential within the created bitstring.&#x20;

Note that this is the same for [unencrypted Status Lists](../status-lists/) and for [encrypted Status Lists](../payments/charge.md). For example:

```json
{
  "statusPurpose": "revocation",
  "statusListName": "employee-credentials"
}
```

</details>

#### Additional options for specifying credentialStatus bitstring index

Below are a set of examples of alternative input parameters for users to specify the bitstring index of the issued Credential. The bitstring index is where exactly the issued credential will map to within the Status List. This should be noted and stored by the issuer to keep a record of which issued credentials are active, revoked or suspended:

<details>

<summary>Example Request Format: Random Bitstring index</summary>

```json
{
  "statusPurpose": "revocation",
  "statusListName": "employee-credentials"
}

```

</details>

<details>

<summary>Example Request Format: Specified Bitstring index</summary>

```json
{
  "statusPurpose": "revocation",
  "statusListName": "employee-credentials",
  "statusListIndex": 1543
}

```

</details>

<details>

<summary>Example Request Format: Bitstring index within a given range</summary>

```json
{
  "statusPurpose": "revocation",
  "statusListName": "employee-credentials"
  "statusListRangeStart": 1000,
  "statusListRangeEnd": 2000
}
```

</details>

<details>

<summary>Example Request Format: Bitstring including omitted bits </summary>

```json
{
  "statusPurpose": "revocation",
  "statusListName": "employee-credentials"
  "statusListRangeStart": 1000,
  "statusListRangeEnd": 2000,
  "indexNotIn": 1001. 1264. 1268, 1854
}

```

</details>

{% hint style="info" %}
Ensure that the `"statusPurpose"` and `"statusListName"` is the same as the existing Status List on-ledger.
{% endhint %}

### Option 2. Publish a JSON payload yourself

Instead of using simple form variables, you can issue a Verifiable Credential using a JSON payload with the `application/json` option.&#x20;

#### Request format

Below is an example of the request format for issuing a Verifiable Credential using a custom JSON payload, including some of the possible parameters:

```json
{
  "issuerDid": "did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0",
  "subjectDid": "did:key:z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK",
  "attributes": {
    "gender": "male",
    "name": "Bob"
  },
  "@context": [
    "https://schema.org"
  ],
  "type": [
    "Person"
  ],
  "credentialSchema": "https://common.schemas.verida.io/identity/kyc/FinClusive/individual-basic/v0.1.0/schema.json",
  "format": "jwt",
  "credentialStatus": {
    "statusPurpose": "revocation",
    "statusListName": "employee-credentials",
    "statusListIndex": 10
  }
}
```

## Step 5: Issue a Credential using the API below

Execute the API below to issue a Verifiable Credential, signed by your issuer DID.

{% openapi-operation spec="cheqd-studio-api" path="/credential/issue" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

## Alternatives

Below are a list of alternatives for using Credentials with cheqd support. Each offers a different set of protocols and underlying technical capabilities.

<table data-card-size="large" data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../sdk/veramo/">veramo</a></td></tr><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK which <a href="https://hyperledger.github.io/anoncreds-spec/"><strong>supports ZKCreds (AnonCreds)</strong></a> and regular Verifiable Credentials natively with cheqd support. </td><td><a href="../../sdk/credo/">credo</a></td></tr><tr><td><mark style="color:blue;"><strong>ACA-Py</strong></mark></td><td>ACA-Py (Aries Cloud Agent Python) is an SDK that can issue AnonCreds, JSON-LD and SD-JWT credentials with full cheqd support.</td><td><a href="../../sdk/aca-py/">aca-py</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id SSI Kit</strong></mark></td><td>Walt.id SSI Kit is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with full cheqd support. </td><td><a href="../../sdk/walt-id.md">walt-id.md</a></td></tr></tbody></table>

t
