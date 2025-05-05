---
description: Revoke a W3C Verifiable Credential using cheqd Studio.
---

# Revoke Credential

Using cheqd's innovative DID-Linked Resource module, cheqd is able to support decentralized and scalable Verifiable Credential revocation and suspension. Using this API, the user is able to choose whether they would like to publish the revocation status to the cheqd ledger or elsewhere.

## Step 1: Locate the Credential Body or JWT to be revoked

It is best practice for issuers to keep a record of the Credentials they have issued, including the `"statusListIndex`" of the Credentials. From this record system, issuers should be able to fetch either the full Credential Body or the JWT proof of the Credential they want to revoke.

## Step 2: Decide whether to publish update to the ledger

When revoking a Credential, issuers can decide whether they want to publish an updated Status List on-ledger, with the revoked credential index updated in the bitstring. The parameter below can be changed to reflect this:

<details>

<summary>publish</summary>

* true (indicates the issuer wants to publish the updated Status List on ledger)
* false (indicates the issuer wants to manually publish a Status List update)

</details>

## Step 3: Paste the Credential Body or JWT into the API

Paste the Credential Body or JWT into the API below and execute the API to revoke the Credential.

{% openapi-operation spec="cheqd-studio-api" path="/credential/revoke" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}
