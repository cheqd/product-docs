---
description: Issue a Verifiable Credential using the ACA-Py API endpoints.
---

# Issue a Verifiable Credential

Using the `/issue-credential-2.0` API endpoints, it is possible to issue Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code. By following the following steps, you can effectively issue verifiable credentials using ACA-Py integrated with the cheqd ecosystem.

## Step 1: Create a Connection with Holder

Use any supported method to create a connection with the Holder of the credential. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) is recommended.

### 1a: Issuer Creates Connection Invite

The Issuer agent will create a new connection invite for the Holder. This is needed to securely communicate between the Issuer and the Holder agents.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/out-of-band/create-invitation" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

#### Parameters and Request Body

For automated acceptance, pass the following parameters:

<details>

<summary>auto_accept</summary>

Set this to true

</details>

A simple request body is below:

```json
{
    "alias": "Holder",
    "handshake_protocols": ["https://didcomm.org/didexchange/1.0"],
    "use_public_did": false,
    "my_label": "Invitation to Holder"
}
```

### 1b: Holder receives invitation

The above request will have an `invitation` in the response. Holder will have to copy that invitation and pass in the body of the following API call.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/out-of-band/receive-invitation" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

#### Parameters and Request Body

<details>

<summary>auto_accept</summary>

Set this to true

</details>

<details>

<summary>alias</summary>

Pass an alias for the connection

</details>

<details>

<summary>use_existing_connection</summary>

Set this to true

</details>

The request body should be the `invitation` value from the `/create-invitation` call by the Issuer.

## Step 2: Prepare Credential Offer

Generate a credential offer using the `/issue-credential-2.0/send-offer` API. This offer is sent to the holder, informing them about the available credential and its attributes.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/send-offer" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

## Step 3: Holder accepts credential offer

The Holder has to retrieve the credential exchange id, and call this API to accept the offer.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/records/{cred_ex_id}/send-request" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

## Step 4: Issue Credential

Once the credential request is received and validated, issue the credential using the `/issue-credential-2.0/records/<exchange-id>/issue` API endpoint.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/records/{cred_ex_id}/issue" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

## Step 5: Holder stores Credential

The Holder has to store the issued credential into their wallet using the following API.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/records/{cred_ex_id}/store" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}



