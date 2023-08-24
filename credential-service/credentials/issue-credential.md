---
description: Issue conformant W3C Verifiable Credentials over REST API
---

# Issue Credential

## Issue Verifiable Credentials

Using the `/credential/create` API, it is possible to issue Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code.&#x20;

### Step 1: Set up your account

Make sure you have set up your account with Credential Service and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Credential Service and log in to start using the APIs.</td><td><a href="../set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

### Step 2: Create an Issuer DID

Before you can issue a Verifiable Credential, you need to create an Issuer DID which is used to sign the Credential payload. Use the API in the page below to create an Issuer DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

### (Optional) Step 3: Create a Subject DID

Again, before you issue a Verifiable Credential, you need to know to whom you are issuing it. If you need to create a Subject DID, you can take a look at the page here:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a Subject DID</strong></mark></td><td>Create an off-ledger <code>did:key</code> or <code>did:vda</code> Subject DID to receive a Verifiable Credential.</td><td><a href="../dids/create-subject-did.md">create-subject-did.md</a></td></tr></tbody></table>

### Step 4: Compile your Credential body

Within the JSON object of the API request, you will need to input the `issuer` and `subject` information, as well as the `attributes` which you want to issue in the Credential. You may also want to add additional fields such as a `credentialSchema`.

### Step 5: Issue a Credential using the API below

Hit the API below to issue a Verifiable Credential, signed by your issuer DID.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/issue" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## Alternatives

Below are a list of alternatives for using Credentials with cheqd support. Each offers a different set of protocols and underlying technical capabilities.

<table data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../sdk/veramo-plugin/">veramo-plugin</a></td></tr><tr><td><mark style="color:blue;"><strong>Aries Framework JavaScript</strong></mark></td><td>Aries Framework JavaScript is an SDK which <a href="https://hyperledger.github.io/anoncreds-spec/"><strong>supports ZKCreds (AnonCreds)</strong></a> natively with cheqd support. </td><td><a href="../../sdk/aries-framework-js.md">aries-framework-js.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id SSI Kit</strong></mark></td><td>Walt.id SSI Kit is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with full cheqd support. </td><td><a href="../../sdk/ssi-kit.md">ssi-kit.md</a></td></tr></tbody></table>

t
