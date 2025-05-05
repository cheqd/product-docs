---
description: >-
  Understand how to issue a W3C Verifiable Credential including an encrypted
  Status List in the body.
---

# Issue Credential with Encrypted Status List

Issuers are able to add encrypted Status Lists to the body of the Credential if they have previously created an Encrypted Status List on-ledger.

## Step 1: Set up your account

Make sure you have set up your account with cheqd Studio and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Studio and log in to start using the APIs.</td><td><a href="../../getting-started/studio/set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Create an Issuer DID

Before you can create an encrypted Status List for charging for Credentials, you need to create a DID which is used to link the Status List on-ledger. Use the API in the page below to create a DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

## Step 3: Create an Encrypted Status List

Follow the tutorial here to create an encrypted Status List with a set of Payment Conditions to unlock:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Charge for Status List</strong></mark></td><td>Create an encrypted Status List on-ledger with Payment Conditions to unlock, to charge for unlocking Status Lists.</td><td><a href="charge.md">charge.md</a></td></tr></tbody></table>

## Step 4: Specify the encrypted Status List within Credential body

Follow the instructions within the `/credential/create` API below to format and issue a Verifiable Credential.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Credential</strong></mark></td><td>Issue a W3C conformant Verifiable Credential using the cheqd Studio APIs.</td><td><a href="../credentials/issue-credential.md">issue-credential.md</a></td></tr></tbody></table>

{% hint style="info" %}
Ensure that the "`statusListName`" and "`statusPurpose`" are the same as the Status List [created in Step 3](issue-credential.md#step-3-create-an-encrypted-status-list). This makes sure that if the Credential Status is changed, for example, if it is revoked or suspended, the correct Status List will be updated.
{% endhint %}
