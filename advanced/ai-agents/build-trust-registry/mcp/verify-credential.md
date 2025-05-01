---
description: 'Tutorial: Verify a Credential using MCP Toolkit'
---

# Verify a Credential

Follow these instructions to Verify a Credential from your MCP Client (e.g. Claude Desktop) using the MCP Toolkit.

> ⚠️ **Before you begin...**
>
> Make sure you have configured the [MCP Client as per the setup instructions](./#getting-started) and [issued a Credential](issue-credential.md) from the same MCP Client.

## Step 1: Create Connection between Holder and Claude Desktop.

If the connection was disrupted, [create a new connection](issue-credential.md#step-2-create-connection-between-holder-and-claude-desktop) between the Holder and Claude Desktop.

## Step 2: Ask Claude to issue a Proof Request

Ask Claude to issue a proof request with some conditions based on the attributes of your credential.&#x20;

For this tutorial, we asked Claude to generate a proof request with **score > 50**.

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-04-02 at 14.08.05.png" alt=""><figcaption></figcaption></figure>

## Step 3: Holder checks Request and sends Proof

The next steps are at the Holder Agent side to accept the proof request and send the response. It can be simulated by running the following APIs in sequence from the Holder API at [http://localhost:4001/api/doc](http://localhost:4001/api/doc) or Postman.

{% openapi src="../../../../.gitbook/assets/swagger.json" path="/present-proof-2.0/records" method="get" %}
[swagger.json](../../../../.gitbook/assets/swagger.json)
{% endopenapi %}

**Parameters**

To narrow down the latest proof request, pass the following parameters

<details>

<summary>limit</summary>

Set this to 10.

</details>

<details>

<summary>offset</summary>

set this to 0.

</details>

<details>

<summary>state</summary>

Set this to "request-received"

</details>

Copy and save the `pres_ex_id` and the proof request `by_format.pres_request.anoncreds` from the response.

{% openapi src="../../../../.gitbook/assets/swagger.json" path="/present-proof-2.0/records/{pres_ex_id}/credentials" method="get" %}
[swagger.json](../../../../.gitbook/assets/swagger.json)
{% endopenapi %}

Fetch the relevant credentials and then create a proof response. Send the proof response by calling the below API.

{% openapi src="../../../../.gitbook/assets/swagger.json" path="/present-proof-2.0/records/{pres_ex_id}/send-presentation" method="post" %}
[swagger.json](../../../../.gitbook/assets/swagger.json)
{% endopenapi %}

**Request Body**

For this tutorial, as the credential is AnonCreds, we are sending an AnonCreds proof in the body. The values will be based on the presentation request and the credentials retrieved in the previous two steps.

```json
{
    "anoncreds": {
        "requested_attributes": <<requestedAttributes>>,
        "requested_predicates": <<requestedPredicates>>,
        "self_attested_attributes": {}
}
```

## Step 4: Ask Claude to validate the Presentation Response

Finally, you can ask Claude to check the status of the Proof Request with the following prompt.

> "Can you check the status of the proof request?"

Claude should get the latest status and summarise if the verification result was true or false.

