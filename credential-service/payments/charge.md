---
description: Creating an encrypted Status List 2021 Resource using cheqd Studio
---

# Charge for Status List

To create an encrypted Verifiable Credential Status List v2021 with a set of Access Control Conditions, an Issuer will need to follow the steps below:

## Step 1: Set up your account

Make sure you have set up your account with cheqd Studio and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Studio and log in to start using the APIs.</td><td><a href="../set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Create an Issuer DID

Before you can create an encrypted Status List for charging for Credentials, you need to create a DID which is used to link the Status List on-ledger. Use the API in the page below to create a DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

## Step 3: Select the encrypted Status List API

When [creating a Status List](../status-lists/), a user will have the following options:&#x20;

<details>

<summary>/credential-status/create/encrypted</summary>

Using this API will encrypt the bitstring of the Status List, meaning that a set of Payment Conditions will need to be met in order to access the Status List contents.

</details>

<details>

<summary>/credential-status/create/unencrypted</summary>

Using this API will create a regular Status List on-ledger, where the contents of the Status List are visible to any users who queries the blockchain.&#x20;

</details>

For the purpose of this tutorial, we will assume the user is creating an encrypted Status List. For unencrypted Status Lists, [follow the tutorial here](../status-lists/create-status-list.md).

## Step 4: Create encrypted Status List

Using the `/credential-status/create/encrypted` API, users have two options for creating an encrypted Status List on-ledger:

1. **Filling out a simple form** using the `application/x-www-url-form-encoded` option on the Swagger UI.
2. **Compiling a Status List payload yourself** using the `application/json` option on the Swagger UI.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/credential-status/update/encrypted" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

### Option 1. Choose from a few variables and we will compile the Status List for you

This is the easiest way to create encrypted Status Lists on cheqd and is recommended for users who are not overly familiar with compiling JSON objects.&#x20;

Using the `application/x-www-url-form-encoded` option on the Swagger UI, users are able to choose between the following variables and options to create an encrypted Status List on-ledger:

<details>

<summary>statusPurpose (required)</summary>

* revocation (creates a Status List where each entry refers to revocation status)
* suspension (creates a Status List where each entry refers to suspension status. Note that suspended Credentials may become unsuspended).

</details>

<details>

<summary>did (required)</summary>

Enter the Decentralized Identifier (DID) of the Status List publisher [created in Step 2](charge.md#step-2-create-an-issuer-did). For example:

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

<summary>feePaymentAddress (required)</summary>

The cheqd payment address where payments to unlock the encrypted StatusList2021 DID-Linked Resource need to be sent. For example:

```json
cheqd1qs0nhyk868c246defezhz5eymlt0dmajna2csg
```

</details>

<details>

<summary>feePaymentAmount (required)</summary>

The amount in CHEQ tokens to unlocked the encrypted StatusList2021 DID-Linked Resource. For example:

```json
100
```

</details>

<details>

<summary>feePaymentWindow (required)</summary>

Time window (in minutes) within which the payment to unlock the encrypted StatusList2021 DID-Linked Resource is considered valid. For example:

```json
10
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

From this request, cheqd Studio **will automatically create and publish an encrypted Status List to the ledger** with set Payment Conditions required to be met to unlock.

### Option 2. Publish a JSON payload yourself

Instead of using simple form variables, you can create an encrypted Status List using a JSON payload yourself using the `application/json` option on the Swagger UI.&#x20;

#### Request format

An example of the JSON payload needed to be submitted is below:

```json
{
  "did": "did:cheqd:testnet:7c2b990c-3d05-4ebf-91af-f4f4d0091d2e",
  "statusListName": "cheqd-employee-credentials-encrypted",
  "paymentConditions": [
    {
      "feePaymentAddress": "cheqd1qs0nhyk868c246defezhz5eymlt0dmajna2csg",
      "feePaymentAmount": 20,
      "feePaymentWindow": 10
    }
  ]
}
```

The table below expands on some of the required parameters:

| Parameter             | Example value                                    | Description                                                                                                                                                                                                                               |
| --------------------- | ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"feePaymentAddress"` | `"cheqd1xl5wccz667lk06ahama26pdqvrkz5aws6m0ztp"` | This specifies the cheqd address to which the payment fee should be sent. **This address is associated with the User who wants to charge for Credential Status**                                                                          |
| `"feePaymentAmount"`  | `"1000"`                                         | <p>This defines the amount of the <strong>payment fee in CHEQ which the Issuer will charge</strong> for a third party to unlock the Credential Status. <br><br>The amount may be up to 2 decimal points, e.g. "<code>1000.25</code>".</p> |
| `"feePaymentWindow"`  | "`10`"                                           | The amount of time, in minutes, after making the payment in which a Verifier is able to unlock an encrypted resource.                                                                                                                     |

## Step 5: Hit the API

Once the Issuer has populated the requisite information for the encrypted Status List request, they can use the API below to submit it to the ledger.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/credential-status/update/encrypted" method="post" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

## Encrypted Status List Response format

The following code snippet shows an example of an encrypted Status List response format, which will be published to the ledger:

{% hint style="info" %}
Note: it is important to save the response, including the "`encryptedSymmetricKey`" locally, as this will be used for [updating encrypted Status List operations](update.md).
{% endhint %}

```json
{
  "created": true,
  "resource": {
    "StatusList2021": {
      "encodedList": "496fdfbeb745b4db03fcdb40566f9c4c4a1c0f184b31255e641b6e7bdfb9b6946c12be87ca3763be0393c00b67ac1e8737c106b32f46ef59c765754415b5e8cc7c65fccaa3374620430ea476301a5e0dd63340e7a27a68bc627518471f22e4a2",
      "type": "StatusList2021Revocation",
      "validFrom": "2023-06-26T11:45:19.349Z"
    },
    "metadata": {
      "type": "StatusList2021Revocation",
      "encoding": "base64url",
      "encrypted": true,
      "encryptedSymmetricKey": "b11182dc524b8181f9a6aef4c4ad0a1c14e40033b9112dffd8d1bcf6cc3b85abc07ded2205ee94068a99f4202502cb0855f322583fa6ce1534d3a05bf36891766ea2c5f90a982b3040680762977d404d758a2370224a239c8279aa7d21e980931c42055b17ca4c7dbffa4782480a8b6279cf989b2f166d5fdb4b2c1b5a63927200000000000000203018dcaba26df45a415bb599218b27ca853a70289d7a3ed3ed0e3730452e8f8d9af91b6e71312565d2c069341f6660ab",
      "paymentConditions": [
        {
          "feePaymentAddress": "cheqd1qs0nhyk868c246defezhz5eymlt0dmajna2csg",
          "feePaymentAmount": "20000000000ncheq",
          "intervalInSeconds": 600,
          "type": "timelockPayment"
        }
      ]
    },
    "resourceMetadata": {
      "resourceURI": "did:cheqd:testnet:7c2b990c-3d05-4ebf-91af-f4f4d0091d2e/resources/5945233a-a4b5-422b-b893-eaed5cedd2dc",
      "resourceCollectionId": "7c2b990c-3d05-4ebf-91af-f4f4d0091d2e",
      "resourceId": "5945233a-a4b5-422b-b893-eaed5cedd2dc",
      "resourceName": "cheqd-revocation-encrypted-1",
      "resourceType": "StatusList2021Revocation",
      "mediaType": "application/json",
      "resourceVersion": "2023-06-26T11:45:19.349Z",
      "created": "2023-06-26T11:45:20Z",
      "checksum": "909e22e371a41afbb96c330a97752cf7c8856088f1f937f87decbef06cbe9ca2",
      "previousVersionId": null,
      "nextVersionId": null
    },
    "symmetricKey": "dfe204ee95ae74ea5d74b94c3d8ff782273905b07fbc9f8c3d961c3b43849f18"
  }
}
```

{% hint style="info" %}
Note: The only encrypted element is the "`encodedList`" element. This provides the Verifier sufficient information to be able to make the payment back the the Issuer and to fulfill the [Access Control Conditions](../../sdk/veramo-plugin/payments/learn/access-control-conditions.md), without being able to see the contents of the Status List itself.
{% endhint %}
