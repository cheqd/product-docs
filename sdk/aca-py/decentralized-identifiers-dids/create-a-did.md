---
description: Create a DID using the did:cheqd method from ACA-Py Agent
---

# Create a DID

Follow these instructions to create a new DID from ACA-Py Agent and publish the associated DID Document on cheqd ledger.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup-aca-py-agent.md) for ACA-Py.

For wallet storage, utilise a Postgres database to ensure key persistence, enabling future updates to the DID.

{% swagger src="../../../.gitbook/assets/swagger.json" path="/did/cheqd/create" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endswagger %}

#### Request Body

These are the variables that can be passed in the Body:

<details>

<summary>features (optional)</summary>

Optional Object with Key-Value format for additional features of the new DID. This is for future use.

</details>

<details>

<summary>options (optional)</summary>

Optional Object with Key-Value format for additional configuration options, recommendations below:

"network": testnet or mainnet

"key\_type": "ed25519" or "bls12381g2"

</details>

### List DIDs associated with your wallet <a href="#list-dids-associated-with-your-account" id="list-dids-associated-with-your-account"></a>

After creating a DID or multiple DIDs, users can list all the created DIDs associated with their wallet. Using the `/wallet/did` API.

{% swagger src="../../../.gitbook/assets/swagger.json" path="/wallet/did" method="get" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endswagger %}





