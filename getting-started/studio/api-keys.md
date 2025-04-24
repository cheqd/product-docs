---
description: Generate API Keys to authenticate with cheqd Studio APIs.
---

# 🗝️ API Keys

API keys are unique data strings used to authenticate a user and enable access to privileged operations on cheqd Studio APIs. All cheqd Studio APIs use API keys as the mechanism to **authenticate client requests**. Your API key should be kept confidential and secure at all times.

* Authentication is required for all API requests; without it, the requests will fail.
* All API requests must be made over HTTPS.

{% hint style="info" %}
### 🚧Keep Your API Keys Safe

Because our API keys allow access to cheqd Studio APIs, you must keep them secure at all times.

* Ensure your API key is always stored securely as soon as it is initially generated.
* Make sure to copy your API key into a secure place, such as a Password Manager.
* Never share it or record it in a publicly accessible medium (client-side code, public repositories, etc.).

**Caution:** If you lose secure control of your API keys, your entity may not be able to access your created DIDs, DID-Linked Resources or Credential Payments.
{% endhint %}

## Step 1: Sign into cheqd Studio

Follow the tutorial here to get started with your cheqd Studio account.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Get started with cheqd Studio, setting up your account and selecting a Billing Plan.</td><td><a href="set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Generate API key

Navigate to the "**Developers**" tab on cheqd Studio and select "**Create an API Key**". You are able to choose the expiry date of the API key, and it will automatically be set to expire 1 year from the current date.

## Step 3: Use your API key to authenticate with cheqd Studio APIs

Navigate to the "**APIs**" tab and copy/paste your API key into the API key box towards the top of your page. This will enable you to **authenticate with the cheqd Studio APIs**, allowing users to use cheqd Studio APIs within their existing Billing Plan and usage limits.

## Step 4: Start using cheqd

Get started with our tutorials for cheqd Studio functionality.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DIDs and Identity Keys</strong></mark></td><td>Create <code>did:cheqd</code> DIDs for credential issuers and for DID-Linked Resources</td><td><a href="../../studio/dids/create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Trust Registries</strong></mark></td><td>Create an end-to-end trusted ecosystem, using Trust Chains to build a full Trust Registry.</td><td><a href="../../studio/trust-registries/">trust-registries</a></td></tr><tr><td><mark style="color:blue;"><strong>Payments</strong></mark></td><td>Create commercial models for verifiable credentials, such as verifier-pays-issuer</td><td><a href="../../studio/payments/">payments</a></td></tr><tr><td><mark style="color:blue;"><strong>Status Lists</strong></mark></td><td>Create status lists, including Bitstring Status List and Token Status List</td><td><a href="../../studio/status-lists/">status-lists</a></td></tr><tr><td><mark style="color:blue;"><strong>Credentials</strong></mark></td><td>Issue any type of credential, including SD-JWT, VCDM and AnonCreds via our partners, including trust registries, status lists and payments.</td><td><a href="../../studio/credentials/">credentials</a></td></tr><tr><td><mark style="color:blue;"><strong>Resources</strong></mark></td><td>Create custom resources for policies, presentation definitions, schemas and visual representations of credentials</td><td><a href="../../studio/did-linked-resources/">did-linked-resources</a></td></tr></tbody></table>
