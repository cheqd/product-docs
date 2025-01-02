# Issue a Verifiable Credential

After creating cheqd DIDs, you can issue verifiable credentials using the ACA-Py agent. Here's how:

1. **Create a Schema**: Create as Anoncreds Schema with the newly created DID as `issuerId` using the API `POST /anoncreds/schema`. This will create a [DID-Linked Resource](../../veramo-plugin/did-linked-resources/) of type `anonCredsSchema`.
2. **Create a Credential Definition**: Using the Schema ID from the previous step, create an Anoncreds Credential Definition using the API `POST /anoncreds/credential-definition`. Here you can pass the `support_revocation` option. Ensure that a [Tails Server](https://github.com/bcgov/indy-tails-server) is configured for the issuer.&#x20;
3. **Create Connection with Holder:** Use any supported method to create the connection with the holder of the credential. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) recommended.
4. **Prepare Credential Offer**: Generate a credential offer using the `/issue-credential-2.0/send-offer` API. This offer is sent to the holder, informing them about the available credential and its attributes.
5. **Receive Credential Request**: Wait for the holder to respond with a credential request, which will include proof of possession of the necessary private keys.
6. **Issue Credential**: Once the credential request is received and validated, issue the credential using the `/issue-credential-2.0/records/<exchange-id>/issue` API endpoint.

By following these steps, you can effectively issue verifiable credentials using ACA-Py integrated with the cheqd ecosystem.
