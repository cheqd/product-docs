---
description: How a Verifier pays an Issuer to decrypt an encrypted Status List
---

# Create Verifier pays Issuer flow

cheqd Studio supports payments for verifying Credential Status. This is an innovative feature that is also commonly known as cheqd's Payment Rails. Using the API in this tutorial, there are multiple ways for a Verifier to pay an Issuer to unlock and verify a Credential's status.

## Step 1: Set up an account

A Verifier will need a cheqd Studio account to take advantage of the `/credential-status/check` API and easily use cheqd's Credential Payments. Make sure you are set up and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Studio and log in to start using the APIs.</td><td><a href="../../getting-started/studio/set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Set parameters for check

Using the `/credential-status/check` API, users have two options for checking whether a particular Credential index is revoked or suspended:

1. **Filling out a simple form** using the `application/x-www-url-form-encoded` option on the Swagger UI.
2. **Compiling the JSON transaction yourself** using the `application/json` option on the Swagger UI.

This is the easiest way to check whether a particular credential index is revoked or suspended.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/credential-status/check" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

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

## Option 1: Automatically paying an Issuer to unlock a Credential Status

To automatically make a payment to an Issuer in order to verify an encrypted Status List, there is an additional variable:

<details>

<summary>makeFeePayment</summary>

This will automatically make the fee payment to the issuer (if required) based on payment conditions to unlock encrypted StatusList2021 DID-Linked Resource. This can be set to:

* true (automatically make fee payment)
* false (do not automatically make fee payment)

</details>

If there is sufficient CHEQ in the account of the Verifier, this will automatically make a payment to the Issuer and meet the Access Control Conditions.

### Step 3: Hit the API

In the same action, the Credential Service will perform a verification check on the Credential Status and pay the issuer the fee specified in the Payment Conditions.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/credential-status/check" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

#### Response format

The response format below will indicate clearly whether the check is successful and whether the Credential index in question is revoked / suspended or not.

```json
{
  "checked": true,
  "revoked": false
}
```

## Option 2: Manually Paying an Issuer to unlock a Credential Status

If a Verifier does not want to automatically pay the Issuer using the API, they can choose to make a manual payment to unlock the Credential Status information to build a higher level of trust within the Credential presented to them.

### Step 1: Locating Issuers' payment address and conditions

If a Verifier wants to unlock access to the Resource, to gain access to additional information about a Credential presented to them, such as the Credential Status, firstly, the Verifier will be presented Credential, including a link to the Status List within the `"credentialStatus"` section of the Credential body.

Through following the link in`"credentialStatus"` section of the Credential body. the The Verifier will be directed to an on-ledger Resource, identifiable through a DID URL, for example:&#x20;

{% embed url="https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99?resourceName=revocation-list-encrypted-inverse-timelock&resourceType=StatusList2021Revocation" %}
Example of encrypted Status List identifiable via a DID URL
{% endembed %}

This on-ledger Resource will contain:

* An encrypted potion of the Resource, such as a Status List bitstring. identified by the `"encodedList"` property.
* Unencrypted metadata about the Resource, including the issuers' payment address `"feePayerAddress"` and and the Payment Conditions, `"feePaymentAmount"` and `"intervalInSeconds"`.

This gives the verifier requisite information they need in order to pay the Issuer to unlock the Credential Status. You can learn more about Access Control Conditions below.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Understanding Access Control Conditions</strong></mark></td><td>Learn what different Payment Conditions mean and how they affect a Verifier when verifying Credentials.</td><td><a href="learn/access-control-conditions.md">access-control-conditions.md</a></td></tr></tbody></table>

### Step 2: Pay the Issuer

Using one of [cheqd's supported wallets](../../network/wallets/), Verifiers can make a payment of the amount specified in the `"feePaymentAmount"` to the `"feePayerAddress"`.

This payment should be made in CHEQ.&#x20;

{% hint style="info" %}
Note that the `"feePaymentAmount"` may be specified in ncheq. This is lowest denomination of the CHEQ token, "nano" CHEQ which is 1 x 10^-9 CHEQ.
{% endhint %}

### Step 3: Hit the API

cheqd Studio will perform a verification check on the Credential Status and pay the issuer the fee specified in the Payment Conditions.&#x20;

{% hint style="info" %}
Note the "`intervalInSeconds`" which is the amount of time after making the payment that the Verifier has to hit the API and request access to the encrypted Status List.
{% endhint %}

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/credential-status/check" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

#### Response format

The response format below will indicate clearly whether the check is successful and whether the Credential index in question is revoked / suspended or not.

```json
{
  "checked": true,
  "revoked": false
}
```

Alternatively, if Verifiers have made the payment manually they can also use the /credential/verify API in the tutorial below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Verify Credential</strong></mark></td><td>Verify a W3C Verifiable Credential using the cheqd Studio APIs.</td></tr></tbody></table>

