---
description: Verify W3C Verifiable Presentations using cheqd Studio.
---

# Verify Presentation

A Verifiable Presentation is a collection of multiple Verifiable Credentials that are being presented by a `holder` to a `verifier`. In addition to checking whether the Credentials are untampered, Verifiable Presentation verification also checks that the `holder` subject DID is valid.&#x20;

## Step 1: Obtain Presentation to Verify

To verify a Credential, you can either pass the full VP-JWT string or a JSON object. These can be either obtained from a Verifiable Presentation presented to the user.

## Step 2: Configure Verification Parameters

The user is able to set verification parameters to filter whether they want to verify certain aspects of a Presentation, including:

<details>

<summary>verifyStatus</summary>

* true (indicates that the user wants to verify the Credential Statuses within the Presentatiuon, requiring a credentialStatus property to be present in at least one Credential within the Presentation)
* false (Default. Indicates that the user does not want to verify the Credential Status.&#x20;

</details>

<details>

<summary>fetchRemoteContexts</summary>

When dealing with JSON-LD type Verifiable Credentials you also MUST provide the proper contexts within a Credential body. Set this to `true` ONLY if you want the `@context` URLs to be fetched in case they are a custom context.

* true
* false (default)

</details>

## Step 3: Execute the API

Use the API below to verify a Presentation

{% openapi-operation spec="cheqd-studio-api" path="/presentation/verify" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}
