# Present a Verifiable Credential

To present verifiable credentials, follow these steps:

1. **Establish a Connection with Verifier**: Initiate a DIDComm connection with the verifier using an out-of-band invitation or pre-existing connection.
2. **Receive Proof Request**: The verifier will request proof by sending a `/present-proof-2.0/send-request` message that outlines the required attributes and predicates.
3. **Create Proof**: Construct a proof presentation with valid credentials using the `/present-proof-2.0/records/<exchange-id>/send-presentation` API.
4. **Send Presentation**: Deliver the proof to the verifier and await their response.
5. **Verifiable Proof**: After the verifier evaluates the presentation, they will respond with either acceptance or rejection.

This process enables secure and trustworthy sharing of verifiable credentials within the ACA-Py framework and cheqd ecosystem.
