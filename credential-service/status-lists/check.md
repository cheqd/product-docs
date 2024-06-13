---
description: Querying Status List entries or indices
---

# Check Status List v2021

Using the `/credential-status/check` API, users are able to query specific Credential indices within a Status List to ascertain whether the Credential is revoked, suspended or currently valid.

## Step 1: Set parameters for check

Using the `/credential-status/check` API, users have two options for checking whether a particular Credential index is revoked or suspended:

1. **Filling out a simple form** using the `application/x-www-url-form-encoded` option on the Swagger UI.
2. **Compiling a DID Document body yourself** using the `application/json` option on the Swagger UI.

### Option 1. Choose from a few variables

This is the easiest way to check whether a particular credential index is revoked or suspended.

&#x20;Using the `application/x-www-url-form-encoded` option on the Swagger UI, users are able to choose between the following variables to compile your DID:

<details>

<summary>statusPurpose</summary>

The purpose of the status list that has already been created on-ledger. Can be:

* revocation
* suspension

</details>

<details>

<summary>did</summary>

DID of the StatusList2021 publisher, or the DID linked to the Status List resources. For example:

```json
did:cheqd:testnet:7bf81a20-633c-4cc7-bc4a-5a45801005e0
```



</details>

<details>

<summary>statusListName</summary>

The name of the existing Status List resource to be checked. For example:

<pre><code><strong>employmentCredentialRevocationList
</strong></code></pre>

</details>

<details>

<summary>index</summary>

The index within the bitstring that the user wants to query. For example:

<pre><code><strong>10
</strong></code></pre>

</details>

### Make Fee Payment

To automatically make a payment to an Issuer in order to verify an encrypted Status List, follow the tutorial here:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Verifier pays Issuer</strong></mark></td><td>Understand how a Verifier pays an Issuer to decrypt an encrypted Status List and verify a Credential Status.</td><td><a href="../payments/verifier-pays-issuer.md">verifier-pays-issuer.md</a></td></tr></tbody></table>

### Option 2. Publish a JSON Payload

Instead of using simple parameters, users can submit a JSON payload to the same effect using the `application/json` option on the Swagger UI. For example:

```json
{
  "did": "string",
  "statusListName": "string",
  "index": 0,
  "makeFeePayment": false
}
```

## Step 2: Execute the API request

Execute the API request using the API below:

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/credential-status/check" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}
