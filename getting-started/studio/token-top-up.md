# 🪙 Token top up

Users may exhaust their usage limits in their cheqd Studio billing plan, but may want to continue using the Studio before their usage limits refresh. Therefore, users can manually top up their account with CHEQ tokens to use any APIs that involve a ledger-write, e.g:

1. Creating, Updating or Deactivating DIDs
2. Creating unencrypted or encrypted Status Lists
3. Creating Trust Registries
4. Creating other DID-Linked Resources

## Step 1: Locate your cheqd address (for CHEQ)

The following API can be used to fetch the new account information:

* `customer_id` is used as an identifier for the particular customer using cheqd Studio. It is generated as a sub-field of the JWT token used in the authorization header.
* `cheqd_account` is used to pay for identity transactions on either `testnet` or `mainnet`. A cheqd account is automatically generated when a new `customer_id` is generated.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/account" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

## Step 2: Adding CHEQ tokens to your account

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

## Step 3: Continue using cheqd

Get started with our tutorials for cheqd Studio functionality.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DIDs and Identity Keys</strong></mark></td><td>Create <code>did:cheqd</code> DIDs for credential issuers and for DID-Linked Resources</td><td><a href="../../studio/dids/create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Trust Registries</strong></mark></td><td>Create an end-to-end trusted ecosystem, using Trust Chains to build a full Trust Registry.</td><td><a href="../../studio/trust-registries/">trust-registries</a></td></tr><tr><td><mark style="color:blue;"><strong>Payments</strong></mark></td><td>Create commercial models for verifiable credentials, such as verifier-pays-issuer</td><td><a href="../../studio/payments/">payments</a></td></tr><tr><td><mark style="color:blue;"><strong>Status Lists</strong></mark></td><td>Create status lists, including Status List v2021 and Token Status List</td><td><a href="../../studio/status-lists/">status-lists</a></td></tr><tr><td><mark style="color:blue;"><strong>Credentials</strong></mark></td><td>Issue any type of credential, including SD-JWT, VCDM and AnonCreds via our partners, including trust registries, status lists and payments.</td><td><a href="../../studio/credentials/">credentials</a></td></tr><tr><td><mark style="color:blue;"><strong>Resources</strong></mark></td><td>Create custom resources for policies, presentation definitions, schemas and visual representations of credentials</td><td><a href="../../studio/did-linked-resources/">did-linked-resources</a></td></tr></tbody></table>
