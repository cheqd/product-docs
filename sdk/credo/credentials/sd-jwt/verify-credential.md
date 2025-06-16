---
description: >-
  Verify a SD-JWT Verifiable Credential, signed by a did:cheqd Decentralized
  Identifier (DID), using Credo and OpenID4VP.
---

# Verify a Verifiable Credential

Using the OpenID4VC module, you can verify OpenID compatible SD-JWT Credentials signed by a `did:cheqd` identifier. This guide walks through the flow of verification using the **Credo Agent**.

## Prerequisites

Before you being, ensure you have:

* Basic knowledge of [Credo Agent configuration](https://docs.cheqd.io/product/sdk/credo/set-up-agent).
* [Issued a SD-JWT Verifiable Credential](issue-credential.md).
* [Presented a SD-JWT Verifiable Credential](present-credential.md)

## Step 1: Configure the Verifier Agent <a href="#step-2-set-up-the-issuer-agent" id="step-2-set-up-the-issuer-agent"></a>

After setting the verifier as [per these steps](issue-credential.md#step-2-set-up-the-issuer-agent), configure the verifier as follows

```typescript
// Create a verifier, assuming the agent is called 'verifier'
const openId4VcVerifier = await verifier.modules.openId4VcVerifier.createVerifier({})

// Create a did:key that we will use for signing OpenID4VP authorization requests
const verifierDidResult = await issuer.dids.create<KeyDidCreateOptions>({
  method: 'key',
  options: {
    keyType: KeyType.Ed25519,
  },
})

if (verifierDidResult.didState.state !== 'finished') {
  throw new Error('DID creation failed.')
}

const verifierDidKey = DidKey.fromDid(verifierDidResult.didState.did)
```

## Step 2: Create an Authorization Request

Once you have configured the verifier, you can create an authorization request including an OpenID4VP presentation request based on [DIF Presentation Exchange V2](https://identity.foundation/presentation-exchange/spec/v2.0.0/). The authorization request method will generate an authorization request URI that you can share with a holder.

{% code overflow="wrap" %}
```typescript
const { authorizationRequest, verificationSession } =
  await verifier.modules.openId4VcVerifier.createAuthorizationRequest({
    verifierId: openId4VcVerifier.verifierId,
    requestSigner: {
      didUrl: `${verifierDidKey.did}#${verifierDidKey.key.fingerprint}`,
      method: 'did',
    },
    // Add DIF presentation exchange data
    presentationExchange: {
      definition: {
        id: '9ed05140-b33b-445e-a0f0-9a23aa501868',
        name: 'Employee Verification',
        purpose: 'We need to verify your employee status to grant access to the employee portal',
        input_descriptors: [
          {
            id: '9c98fb43-6fd5-49b1-8dcc-69bd2a378f23',
            constraints: {
              // Require limit disclosure
              limit_disclosure: 'required',
              fields: [
                {
                  filter: {
                    type: 'string',
                    const: 'AcmeCorpEmployee',
                  },
                  path: ['$.vct'],
                },
              ],
            },
          },
        ],
      },
    },
  })
```
{% endcode %}

## Step 3: Add Event Listener to check Verification State Change

Add an event listener that listens for state changed events, this allows Verifier to know when the authorization session is complete.

{% code overflow="wrap" %}
```typescript
// Listen and react to changes in the verification session
verifier.events.on<OpenId4VcVerificationSessionStateChangedEvent>(
  OpenId4VcVerifierEvents.VerificationSessionStateChanged,
  async (event) => {
    if (event.payload.verificationSession.id === verificationSession.id) {
      console.log('Verification session state changed to ', event.payload.verificationSession.state)
    }

    if (event.payload.verificationSession.state === OpenId4VcVerificationSessionState.ResponseVerified) {
      const verifiedAuthorizationResponse = await verifier.modules.openId4VcVerifier.getVerifiedAuthorizationResponse(
        verificationSession.id
      )
      console.log('Successfully verified presentation.', JSON.stringify(verifiedAuthorizationResponse, null, 2))

      console.log('Exiting...')
      process.exit()
    }
  }
)
```
{% endcode %}
