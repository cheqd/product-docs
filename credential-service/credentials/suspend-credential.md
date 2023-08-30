# Suspend or Unsuspend Credential

Users are also able to suspend Verifiable Credentials. The difference between revocation and suspension is that **suspended Credentials may be unsuspended at a future date**; whereas, **revoked credentials are permanently revoked**.

## Step 1: Locate the Credential Body or JWT to be suspended

It is best practice for issuers to keep a record of the Credentials they have issued, including the `"statusListIndex`" of the Credentials. From this record system, issuers should be able to fetch either the full Credential Body or the JWT proof of the Credential they want to suspend.

## Step 2: Decide whether to publish update to the ledger

When suspending a Credential, issuers can decide whether they want to publish an updated Status List on-ledger, with the suspended credential index updated in the bitstring. The parameter below can be changed to reflect this:

<details>

<summary>publish</summary>

* true (indicates the issuer wants to publish the updated Status List on ledger)
* false (indicates the issuer wants to manually publish a Status List update)

</details>

## Step 3: Paste the Credential Body or JWT into the API

Paste the Credential Body or JWT into the API below and execute the API to suspend the Credential.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/suspend" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## Unsuspend (reinstate) Verifiable Credentials

If a Credential has been suspended, and an Issuer wants to unsuspend the Credential to make it once again valid, the Issuer can **reinstate a suspended Credential**.

## Step 1: Locate the Credential Body or JWT to be unsuspended

It is best practice for issuers to keep a record of the Credentials they have issued, including the `"statusListIndex`" of the Credentials. From this record system, issuers should be able to fetch either the full Credential Body or the JWT proof of the Credential they want to unsuspend.

## Step 2: Decide whether to publish update to the ledger

When unsuspending or reinstating a Credential, issuers can decide whether they want to publish an updated Status List on-ledger, with the unsuspended credential index updated in the bitstring. The parameter below can be changed to reflect this:

<details>

<summary>publish</summary>

* true (indicates the issuer wants to publish the updated Status List on ledger)
* false (indicates the issuer wants to manually publish a Status List update)

</details>

## Step 3: Paste the Credential Body or JWT into the API

Paste the Credential Body or JWT into the API below and execute the API to unsuspend the Credential.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/reinstate" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}
