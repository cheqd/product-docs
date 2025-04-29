---
description: >-
  Get started with cheqd's API product offering for creating DIDs, trust
  registries and monetising credentials: cheqd Studio.
cover: ../../.gitbook/assets/cheqd Studio Banner (Orange Transparent).png
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# ➡️ Get started with cheqd Studio

## Introduction

**cheqd Studio** is a set of APIs, guides and tutorials to help users establish an end-to-end trusted ecosystem for digital credentials.

Using REST APIs, customers can build cheqd's trust infrastructure into existing applications. All of cheqd’s existing open-source libraries remain available, and cheqd Studio does not necessitate developers to switch their SSI stack in their entirety, but allows them to build into their existing tooling, for example alongside APIs such as the Universal Resolver.

## Get started

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set Up Your Account</strong></mark></td><td>Begin your cheqd Studio set up, creating your account, creating an API key and authenticating with our APIs.</td><td><a href="set-up-account.md">set-up-account.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Advanced Configuration Options</strong></mark></td><td>Run cheqd Studio yourself or utilise your own external database.</td><td><a href="advanced.md">advanced.md</a></td></tr></tbody></table>

## Features

cheqd Studio directly leverages our [Veramo SDK Plugin](https://github.com/cheqd/did-provider-cheqd), making a wide array of features available from launch, including:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DIDs and Identity Keys</strong></mark></td><td>Create <code>did:cheqd</code> DIDs for credential issuers and for DID-Linked Resources</td><td><a href="../../studio/dids/create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Trust Registries</strong></mark></td><td>Create an end-to-end trusted ecosystem, using Trust Chains to build a full Trust Registry.</td><td><a href="../../studio/trust-registries/">trust-registries</a></td></tr><tr><td><mark style="color:blue;"><strong>Payments</strong></mark></td><td>Create commercial models for verifiable credentials, such as verifier-pays-issuer</td><td><a href="../../studio/payments/">payments</a></td></tr><tr><td><mark style="color:blue;"><strong>Status Lists</strong></mark></td><td>Create status lists, including Bitstring Status List and Token Status List</td><td><a href="../../studio/status-lists/">status-lists</a></td></tr><tr><td><mark style="color:blue;"><strong>Verifiable Credentials</strong></mark></td><td>Issue any type of credential, including SD-JWT, VCDM and AnonCreds via our partners, including trust registries, status lists and payments.</td><td><a href="../../studio/credentials/">credentials</a></td></tr><tr><td><mark style="color:blue;"><strong>DID-Linked Resources</strong></mark></td><td>Create custom resources for policies, presentation definitions, schemas and visual representations of credentials</td><td><a href="../../studio/did-linked-resources/">did-linked-resources</a></td></tr></tbody></table>

## Understanding the different modes and client choices

With cheqd Studio, there are multiple ways it can be deployed and hosted to support clients with different requirements.&#x20;

### Option 1: Custodied by cheqd (Custodian Mode)

This mode is the most simple for users, allowing cheqd to custody **both Cosmos AND Identity keys in Veramo KMS.** This means that manages both ledger-writes and signing identity transactions on behalf of the customer.

To ensure this is highly secure, we have deployed an instance of a **Veramo Key Management Store (KMS)** which uses a **Postgress DB** (**TypeOrm)** to store **Cosmos AND identity** **keys** in one encrypted table, so  it cannot be read in plaintext. This design allows us to segment different customers' keys securely and efficiently.

We use similar techniques to Password Managers such as [1Password](https://1password.com/) and [Bitwarden](https://bitwarden.com/) to ensure that even if the database were to be compromised, the keys would remain encrypted and unusable.

#### User optionality and toggles:

Within Custodian mode, we also enable clients to toggle

<details>

<summary>External Database</summary>



Clients are able to choose whether to use our default database for storing keys or utilise their own database.&#x20;

By default, `ENABLE_EXTERNAL_DB` is set to off/`false`. To enable external Veramo KMS database, set `ENABLE_EXTERNAL_DB` to `true`, then define below environment variables in `.env` file:

1. `EXTERNAL_DB_CONNECTION_URL`: PostgreSQL database connection URL, e.g. `postgres://<user>:<password>@<host>:<port>/<database>`.
2. `EXTERNAL_DB_ENCRYPTION_KEY`: Secret key used to encrypt the Veramo key-specific database tables. This adds a layer of protection by not storing the database in plaintext.
3. `EXTERNAL_DB_CERTIFICATE`: Custom CA certificate required to connect to the database (optional).

</details>

<details>

<summary><strong>API Authentication using LogTo</strong></summary>

By default, the application **has API authentication disabled** (which can be changed in configuration). If, however, you'd like to run the app with API authentication features, the following variables need to be configured.

We use a self-hosted version of [LogTo](https://logto.io/), which supports OpenID Connect. Theoretically, these values could also be replaced with [LogTo Cloud](http://cloud.logto.io/) or any other OpenID Connect identity provider.

By default, `ENABLE_AUTHENTICATION` is set to off/`false`. To enable external Veramo KMS database, set `ENABLE_AUTHENTICATION` to `true`, then define below environment variables in `.env` file:

1. **Endpoints**
   1. `LOGTO_ENDPOINT`: API endpoint for LogTo server
   2. `LOGTO_DEFAULT_RESOURCE_URL`: Root of API resources in this application to be guarded. (Default: `http://localhost:3000/api` on localhost.)
   3. `LOGTO_MANAGEMENT_API`: URL of management API for LogTo (default is `https://default.logto.app/api`)
   4. `CORS_ALLOWED_ORIGINS`: CORS allowed origins used in the app
2. **User-facing APIs**
   1. `LOGTO_APP_ID`: Application ID for the cheqd Studio application in LogTo. This can be set up as type "Traditional Web"
   2. `LOGTO_APP_SECRET`: Application secret associated with App ID above.
3. **Machine-to-machine backend APIs**
   1. `LOGTO_M2M_APP_ID`: Application ID for machine-to-machine application in LogTo. This is used for elevated management APIs within LogTo.
   2. `LOGTO_M2M_APP_SECRET`: Application secret
4. **Default role update using** [**LogTo webhooks**](https://docs.logto.io/developers/webhooks): LogTo supports webhooks to fire of requests to an API when it detects certain actions/changes. If you want to automatically assign a role to users, a webhook is recommended to be setup for firing off whenever there's a new account created, or a new sign-in.
   1. `LOGTO_DEFAULT_ROLE_ID`: LogTo Role ID for the default role to put new users into.
   2. `LOGTO_WEBHOOK_SECRET`: Webhook secret to authenticate incoming webhook requests from LogTo.
5. **Miscellaneous**
   1. `DEFAULT_CUSTOMER_ID`: Customer/user in LogTo to use for unauthenticated users
   2. `COOKIE_SECRET`: Secret for cookie encryption.

</details>

### Option 2: Self-custodied (Client-managed mode)

Client-managed mode gives the cheqd Studio user the ability to utilise their own identity keys for signing identity transactions, while still allowing cheqd Studio to manage the CHEQ account keys for writing to the cheqd network. This mode is intended to be used for more production environments where the user signs each identity transaction independently, affording a greater level of security and control to the client.

{% hint style="info" %}
Full client-managed mode is still in development and we will update this documentation as and when it becomes available
{% endhint %}

## Architecture

Under the hood, cheqd Studio leverages our [Veramo SDK Plugin](https://github.com/cheqd/did-provider-cheqd) for its identity functionality. Check out our [guide on supported SDKs](../../sdk/understanding-sdks.md) to understand how cheqd Studio fits together with our other Open Source packages.

## Alternatives

Below are a list of alternatives for integrating with cheqd. Each offers a different set of protocols and underlying technical capabilities.

<table data-card-size="large" data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK that is building directly towards supporting the EUDI standards, as well as support for AnonCreds.</td><td></td><td><a href="../../sdk/credo/">credo</a></td><td><a href="../../.gitbook/assets/Credo.png">Credo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>ACA-Py</strong></mark></td><td>ACA-Py is a quickly expanding SDK, moving from the AnonCreds ecosystem towards wider support for EUDI.</td><td></td><td><a href="../../sdk/aca-py/">aca-py</a></td><td><a href="../../.gitbook/assets/ACA-Py.png">ACA-Py.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td></td><td><a href="../../sdk/veramo/">veramo</a></td><td><a href="../../.gitbook/assets/veramo logo.png">veramo logo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id Community Stack</strong></mark></td><td>Walt.id Community Stack is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with cheqd DID support.</td><td></td><td><a href="../../sdk/walt-id.md">walt-id.md</a></td><td><a href="../../.gitbook/assets/walt.id logo.png">walt.id logo.png</a></td></tr></tbody></table>
