---
description: 'Tutorial: Issue a Verifiable Credential using MCP Toolkit'
---

# Issue a Verifiable Credential

Follow these instructions to Issue a new Credential from your MCP Client (e.g. Claude Desktop) using the MCP Toolkit.

> ⚠️ **Before you begin...**
>
> Make sure you have configured the [MCP Client as per the setup instructions](./#getting-started).

## Step 1: Start your Holder Agent

Our [docker-compose file](https://github.com/cheqd/mcp-toolkit/blob/main/docker/docker-compose.yml) contains "demo" profile, which includes a Holder ACA-py Agent setup. Start the Holder Agent with the following command

```bash
// from mcp-toolkit folder 
cd docker
docker compose --profile demo up -d
```

## Step 2: Create Connection between Holder and Claude Desktop.

Although, either agents can start the connection, in this tutorial we will start the invite from Holder.

Open the Holder API at [http://localhost:4001/api/doc](http://localhost:4001/api/doc) or use Postman, to create a new connection request. Copy the `invitation_url` from the response.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/out-of-band/create-invitation" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

**Parameters and Request Body**

For automated acceptance, pass the following parameters:

<details>

<summary>auto_accept</summary>

Set this to true.

</details>

A simple request body is below:

```json
{
    "alias": "Faber",
    "handshake_protocols": ["https://didcomm.org/didexchange/1.1"],
    "use_public_did": false,
    "my_label": "Invitation to Faber"
}
```

## Step 3: Claude Desktop accepts the invitation <a href="#id-1b-holder-receives-invitation" id="id-1b-holder-receives-invitation"></a>

Go to Claude Desktop, and accept the connection by pasting the `invitation_url` value.

{% hint style="success" %}
Always "Allow for this chat" or "Allow once" when the prompt appears. This is a security feature to have human interaction for MCP tools.
{% endhint %}

<figure><img src="../../../.gitbook/assets/Screenshot 2025-04-02 at 11.37.56.png" alt=""><figcaption></figcaption></figure>

## Step 4: Ask Claude to issue a Credential

Now you can ask Claude Desktop to issue a Credential Offer to the connection. Claude will interpret the message automatically and do all the steps necessary to issue a Credential Offer.

For this tutorial, we asked Claude to create a credential offer with **score as 80 and subject as Mathematics**.

<figure><img src="../../../.gitbook/assets/Screenshot 2025-04-02 at 11.40.26.png" alt=""><figcaption></figcaption></figure>

When all the steps are completed, a Credential Offer will be sent to Holder agent.

## Step 5: Holder accepts and stores the Credential.

The rest of the steps are from the Holder to accept the credential offer and store the credential. This can be achieved by the following API Calls from the Holder API at [http://localhost:4001/api/doc](http://localhost:4001/api/doc) or Postman.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/records" method="get" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

**Parameters**

Filter on "offer-received" state

<details>

<summary>state</summary>

Set this to "offer-received".

</details>

Copy and save the `cred_ex_id` from the response and pass that in the next request to Accept the Offer.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/records/{cred_ex_id}/send-request" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

Now, store the received Credential into the Holder wallet.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/records/{cred_ex_id}/store" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}



