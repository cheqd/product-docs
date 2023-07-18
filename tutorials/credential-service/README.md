# 🔁 Credential Service

## Introduction

The Credential Service is a set of APIs for using cheqd's identity functionality in a lightweight and highly efficient way.&#x20;

With these APIs, it simplifies the developer experience, helping customers build Trusted Data Markets from the ground up, with zero prior knowledge of the technology under the hood. All of cheqd’s existing open-source libraries remain available, and the Credential Service does not necessitate developers to switch their SSI stack in their entirety, but allows them to build into their existing tooling, for example alongside APIs such as the Universal Resolver.

Getting started:

* [Credential Service Swagger file](https://credential-service.cheqd.net/swagger/)
* [User Guide for understanding the Credential Service APIs](user-guide.md)
* [Developer Guide for setting up Credential Service](developer-guide.md)
* [GitHub repository](https://github.com/cheqd/credential-service)

## Features

The Credential Service directly leverages our [Veramo SDK Plugin](https://github.com/cheqd/did-provider-cheqd), making a wide array of features available from launch, including:

|               Category               |            Feature            | Status                                         |
| :----------------------------------: | :---------------------------: | ---------------------------------------------- |
|   **Verifiable Credentials (VCs)**   |        Issue Credential       | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |       Verify Credential       | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |       Revoke Credential       | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |       Suspend Credential      | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |      Unsuspend Credential     | [✅](https://emojipedia.org/check-mark-button/) |
|  **Verifiable Presentations (VPs)**  |      Verify Presentation      | [✅](https://emojipedia.org/check-mark-button/) |
| **Decentralized Identifiers (DIDs)** |  Create DID and DID Document  | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |      Update DID Document      | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |          Resolve DID          | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |         Deactivate DID        | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |           List DIDs           | [✅](https://emojipedia.org/check-mark-button/) |
|           **Identity keys**          |      Create identity keys     | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |      Fetch identity keys      | [✅](https://emojipedia.org/check-mark-button/) |
|        **Revocation Registry**       |       Create status list      | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |      Publish status list      | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |       Update status list      | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |       Check status list       | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |       Query status lists      | [✅](https://emojipedia.org/check-mark-button/) |
|    **DID-Linked Resources (DLRs)**   |   Create DID-Linked Resource  | [✅](https://emojipedia.org/check-mark-button/) |
|              **Account**             |       Create new account      | [✅](https://emojipedia.org/check-mark-button/) |
|                                      |     Fetch account details     | [✅](https://emojipedia.org/check-mark-button/) |
|        **Credential payments**       | Create payment-gated resource | [⌛](https://emojipedia.org/hourglass-done/)    |
|                                      | Update payment-gated resource | [⌛](https://emojipedia.org/hourglass-done/)    |
|                                      |   Pay-to-verify presentation  | [⌛](https://emojipedia.org/hourglass-done/)    |

## Understanding the different modes and client choices

With Credential Service, there are multiple ways it can be deployed and hosted to support clients with different requirements.&#x20;

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
   1. `LOGTO_APP_ID`: Application ID for the Credential Service application in LogTo. This can be set up as type "Traditional Web"
   2. `LOGTO_APP_SECRET`: Application secret associated with App ID above.
3. **Machine-to-machine backend APIs**
   1. `LOGTO_M2M_APP_ID`: Application ID for machine-to-machine application in LogTo. This is used for elevated management APIs within LogTo.
   2. `LOGTO_M2M_APP_SECRET`: Application secret
4. **Default role update using** [**LogTo webhooks**](https://docs.logto.io/next/docs/recipes/webhooks/): LogTo supports webhooks to fire of requests to an API when it detects certain actions/changes. If you want to automatically assign a role to users, a webhook is recommended to be setup for firing off whenever there's a new account created, or a new sign-in.
   1. `LOGTO_DEFAULT_ROLE_ID`: LogTo Role ID for the default role to put new users into.
   2. `LOGTO_WEBHOOK_SECRET`: Webhook secret to authenticate incoming webhook requests from LogTo.
5. **Miscellaneous**
   1. `DEFAULT_CUSTOMER_ID`: Customer/user in LogTo to use for unauthenticated users
   2. `COOKIE_SECRET`: Secret for cookie encryption.

</details>

### Option 2: Self-custodied (Client-managed mode)

Client-managed mode gives the Credential Service user the ability to utilise their own identity keys for signing identity transactions, while still allowing the Credential Service to manage the CHEQ account keys for writing to the cheqd network. This mode is intended to be used for more production environments where the user signs each identity transaction independently, affording a greater level of security and control to the client.

{% hint style="info" %}
Full client-managed mode is still in development and we will update this documentation as and when it becomes available
{% endhint %}

## Architecture

Under the hood, the Credential Service leverages our [Veramo SDK plugin](https://github.com/cheqd/did-provider-cheqd) for its identity functionality. Check out our guide on SDKs below to understand how Credential Service fits together with our other Open Source packages.

{% content-ref url="../../guides/sdk/" %}
[sdk](../../guides/sdk/)
{% endcontent-ref %}
