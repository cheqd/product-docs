# Create a DID

The ACA-Py Agent will now have the following new endpoints:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-12-24 at 14.08.25.png" alt=""><figcaption></figcaption></figure>

To create a DID using Cheqd, use the `POST /did/cheqd/create` method. For wallet storage, utilise a Postgres database to ensure key persistence, enabling future updates to the DID.
