# Issue a Verifiable Credential

Using the `/issue-credential-2.0` API endpoints, it is possible to issue Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code. By following the following steps, you can effectively issue verifiable credentials using ACA-Py integrated with the cheqd ecosystem.

## Step 1: Create a Connection with Holder

Use any supported method to create a connection with the Holder of the credential. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) is recommended.

## Step 2: Prepare Credential Offer

Generate a credential offer using the `/issue-credential-2.0/send-offer` API. This offer is sent to the holder, informing them about the available credential and its attributes.

{% swagger src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/send-offer" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endswagger %}

## Step 3: Holder accepts credential offer

The Holder has to retrieve the credential exchange id, and call this API to accept the offer.

{% swagger src="../../../.gitbook/assets/swagger.json" path="/issue-credential/records/{cred_ex_id}/send-request" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endswagger %}

## Step 4: Issue Credential

Once the credential request is received and validated, issue the credential using the `/issue-credential-2.0/records/<exchange-id>/issue` API endpoint.

{% swagger src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/records/{cred_ex_id}/issue" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endswagger %}

## Step 5: Holder stores Credential

The Holder has to store the issued credential into their wallet using the following API.

{% swagger src="../../../.gitbook/assets/swagger.json" path="/issue-credential-2.0/records/{cred_ex_id}/store" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endswagger %}



