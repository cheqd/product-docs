---
description: Verify a Credential using cheqd Studio.
---

# Verify Credential

Once you have issued your credential and have a JWT as part of the credential proof, you can use the `/credential/verify` API to check that the JWT has not been tampered.

## Step 1: Obtain Credential to Verify

To verify a Credential, you can either pass the full Credential body or the JWT proof. These can be either obtained from a [Credential that has been issued](issue-credential.md) or from a Verifiable Presentation presented to the user.

## Step 2: Configure Verification Parameters

The user is able to set verification parameters to filter whether they want to verify certain aspects of a Credential, including:

<details>

<summary>verifyStatus</summary>

* true (indicates that the user wants to verify the Credential Status, requiring a credentialStatus property to be present in the Credential)
* false (Default. Indicates that the user does not want to verify the Credential Status.&#x20;

</details>

<details>

<summary>fetchRemoteContexts</summary>

When dealing with JSON-LD type Verifiable Credentials you also MUST provide the proper contexts within a Credential body. Set this to `true` ONLY if you want the `@context` URLs to be fetched in case they are a custom context.

* true
* false (default)

</details>

## Step 3: Pass the Credential to the API

Simply paste the JWT or the full credential body  into the request field of the `/credential/verify` API, and the API will give you a response including the following verification policies:

1. Whether the Credential has been tampered
2. Whether the Credential has a valid issuance date
3. Whether the Credential has expired
4. Whether the Credential Status is valid

{% openapi-operation spec="cheqd-studio-api" path="/credential/verify" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

