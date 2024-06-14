---
description: Learn how to set up your account on cheqd Studio.
---

# 👉 Set up your account

The user is required to **Log In** to cheqd Studio and authenticate in order to access the APIs. This guards the API from unauthorized access and is required for both testing production environments.

## Step 1: Click Log In on cheqd Studio Swagger

Head to our [**cheqd Studio**](https://credential-service.cheqd.net/swagger/#/) and click **Log In** to get started.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Studio</strong></mark></td><td>Click here to access cheqd Studio Swagger UI to get started with the REST API interface. Log in to get started.</td><td><a href="https://credential-service.cheqd.net/swagger/#/">https://credential-service.cheqd.net/swagger/#/</a></td></tr></tbody></table>

cheqd Studio uses a [third party service called LogTo](https://logto.io/) to handle user authentication and login. This allows users to create new accounts as well as sign in using their email, Google single sign-on, or Discord login.&#x20;

<div align="left">

<figure><img src="../../.gitbook/assets/Credential Service Sign In.png" alt="" width="375"><figcaption><p>Sign into your account</p></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/Credential Service Sign up.png" alt="" width="375"><figcaption><p>Create a new account</p></figcaption></figure>

</div>

## Step 2: Locate your cheqd address (for CHEQ)

The following API can be used to fetch the new account information:

* `customer_id` is used as an identifier for the particular customer using cheqd Studio. It is generated as a sub-field of the JWT token used in the authorization header.
* `cheqd_account` is used to pay for identity transactions on either `testnet` or `mainnet`. A cheqd account is automatically generated when a new `customer_id` is generated.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/account" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

## (Optional) Step 3: Adding CHEQ tokens to your account

Users will need to add our token, CHEQ, to their `cheqd_account`  on `mainnet` in order to be able to use the ledger-based identity functionality on cheqd Studio, such as creating DIDs and DID-Linked Resources.

If you need to add CHEQ tokens to your cheqd Studio `cheqd_account`, there are a few steps you need to follow:

<details>

<summary>Mainnet</summary>

* First you need to [setup your wallet](../../network/wallets/) which is able to hold CHEQ tokens. We recommend using [Leap Wallet](https://www.leapwallet.io/) which natively supports all CHEQ transactions in a browser plugin or on mobile. Alternatively, follow the [tutorial here to setup your Keplr wallet](../../network/wallets/keplr-setup.md).
* You will then need to [get CHEQ tokens from any of the listed providers here](https://cheqd.io/get-involved/).
* You will need to send CHEQ tokens from your new cheqd Wallet account to your `cheqd_account` address for using the Credential Service.

</details>

<details>

<summary>Testnet</summary>

* It is unlikely that you will ever need to top up your Testnet account, as this is handled automatically. However, in the event that your Testnet account runs out of tokens, you can follow the step below.
* This is super simple. You can add free CHEQ tokens to your cheqd\_account [via our Testnet Faucet](../../network/tools/testnet-faucet.md) by inputting your cheqd account address. Access the [Testnet Faucet directly here](https://testnet-faucet.cheqd.io/).

</details>

Once you have successfully created an account and have topped up your CHEQ tokens, you are ready to get started!
