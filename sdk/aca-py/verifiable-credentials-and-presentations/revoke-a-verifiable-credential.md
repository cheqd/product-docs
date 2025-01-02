# Revoke a Verifiable Credential

To revoke credentials within the ACA-Py framework, follow these steps:

1. **Identify Credential**: Determine the credential you need to revoke by referencing its unique identifier.
2. **Send Revoke Command**: Utilise the `/anoncreds/credentials/revoke` API endpoint to initiate the revocation process, supplying the necessary credential identifiers like the connection id and the credential exchange id.
3. **Confirm Revocation**: Verify that the credential has been successfully revoked by checking the response from the revocation API and sending a new presentation request (which should fail).

Revoking credentials is crucial for maintaining control over issued credentials and preventing misuse in the cheqd ecosystem.
