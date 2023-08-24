---
description: Create and Publish Status List
---

# Create and Publish Status List

Users are able to create [Verifiable Credential Status List v2021](https://www.w3.org/TR/vc-status-list/) entries on-ledger, in order to revoke or suspend Verifiable Credentials. This is a derivation from the core spec made by cheqd to support a more decentralized and resilient approach to storing Status Lists.

### Step 1: Set up your account

Make sure you have set up your account with Credential Service and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Credential Service and log in to start using the APIs.</td><td><a href="../set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

### Step 2: Create a DID

Before you can create a Status List, you need to create a DID which is used to link the Status List on-ledger. Use the API in the page below to create a DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

### Step 3: Understanding Status Lists

When [creating a Status List](./), a user will have the following options:&#x20;

<details>

<summary>statusPurpose: Revocation</summary>

This creates a revocation Status List, whereby the Credentials, once revoked are permanently revoked.

</details>

<details>

<summary>statusPurpose: Suspension</summary>

This creates a Suspension Status List, whereby Credentials suspended by the issuer may be unsuspended in the future.&#x20;

</details>

<details>

<summary>Encrypted: true</summary>

Setting the value of encrypted to **true** will encrypt the bitstring of the Status List, meaning that a set of Payment Conditions will need to be met in order to access the Status List contents.

</details>

<details>

<summary>Encrypted: false</summary>

Setting the value of encrypted to **false** will create a regular Status List on-ledger, where the contents of the Status List are visible to any users who queries the blockchain.&#x20;

</details>

If a user sets the value to `encrypted:true`, then they will be [creating a Payment-gated Status List and should follow the page here](../payments/charge.md). Therefore, this page assumes the user is creating an unencrypted Status List.&#x20;

### Step 4: Create your Status List

Hit the API below to create your Status List :

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/create" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

### (Optional) Step 5: Publish pre-existing Status List

If a User has pre-existing Status Lists or has assembled the Status List themselves, they are able to publish the full contents of the Status List to the ledger.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/publish" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}
