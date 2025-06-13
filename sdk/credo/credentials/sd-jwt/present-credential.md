---
description: >-
  Receive and present a SD-JWT Verifiable Credential, signed by a did:cheqd
  Decentralized Identifier (DID), using Credo and OpenID4VP.
---

# Present a Verifiable Credential

Using the OpenID4VC module, you can receive and present a OpenID compatible SD-JWT Credentials signed by a `did:cheqd` identifier. This guide walks through the flow of presentation using the **Credo Agent**.

## Prerequisites

Before you being, ensure you have:

* Basic knowledge of [Credo Agent configuration](https://docs.cheqd.io/product/sdk/credo/set-up-agent).
* [Issued a SD-JWT Verifiable Credential](issue-credential.md).

## Step 1: Configure the Holder Agent <a href="#step-2-set-up-the-issuer-agent" id="step-2-set-up-the-issuer-agent"></a>

Configure the holder with cheqd and OpenID4VC Modules

```typescript
import { Agent, DidsModule } from '@credo-ts/core';
import { agentDependencies } from '@credo-ts/node';
import { CheqdModule } from '@credo-ts/cheqd';
import { OpenId4VcHolderModule } from '@credo-ts/openid4vc';

const holder = new Agent({
  config,
  dependencies: agentDependencies,
  modules: {
    dids: new DidsModule({
      resolvers: [new CheqdDidResolver()],
    }),
    openId4VcHolderModule: new OpenId4VcHolderModule(),
  },
});
```

## Step 2: Resolve and Accept Credential Offer

{% code overflow="wrap" %}
```typescript
import { DidKey, KeyDidCreateOptions, getJwkFromKey } from '@credo-ts/core';

const resolved = await holder.modules.openId4VcHolder.resolveCredentialOffer(credentialOffer);

const credentials = await holder.modules.openId4VcHolder.acceptCredentialOfferUsingPreAuthorizedCode(
  resolved,
  {
    credentialBindingResolver: async ({
      supportedDidMethods,
      keyType,
      supportsAllDidMethods,
      supportsJwk,
      credentialFormat,
    }) => {
      if (supportsAllDidMethods || supportedDidMethods?.includes('did:key')) {
        const didResult = await holder.dids.create<KeyDidCreateOptions>({
          method: 'key',
          options: { keyType },
        });
        const didKey = DidKey.fromDid(didResult.didState.did);
        return {
          method: 'did',
          didUrl: `${didKey.did}#${didKey.key.fingerprint}`,
        };
      }
      if (supportsJwk && credentialFormat === OpenId4VciCredentialFormatProfile.SdJwtVc) {
        const key = await holder.wallet.createKey({ keyType });
        return { method: 'jwk', getJwkFromKey(key) };
      }
      throw new Error('No binding method supported.');
    },
  }
);

```
{% endcode %}

This method:

1. Resolves the offer.
2. Accepts it via **pre-authorized code flow**.
3. Selects a binding method—`did:key` (preferred) or JWK—for the SD-JWT, depending on issuer capabilities

## Step 3: Store the Credentials

```typescript
// Store the received credentials
const records: Array<W3cCredentialRecord | SdJwtVcRecord> = []
for (const credential of credentials) {
  if ('compact' in credential) {
    const record = await holder.sdJwtVc.store(credential.compact)
    records.push(record)
  } else {
    const record = await holder.w3cCredentials.storeCredential({
      credential,
    })
    records.push(record)
  }
}
```

## Step 4: Resolve and Accept Authorization Request

Once you have a credential in your wallet, you can present it by responding to a receive authorization request, which includes an OpenID4VP presentation request. This request can be generated either by the verifier module or an external OpenID4VC verifier. First, resolve the authorization request, then accept it to present the credential in your wallet.

{% code overflow="wrap" %}
```ts
// resolved credential offer contains the offer, metadata, etc..
const resolvedRequest = await holder.modules.openId4VcHolderModule.resolveSiopAuthorizationRequest(authorizationRequest)
console.log(
  'Resolved credentials for request', JSON.stringify(resolvedRequest.presentationExchange.credentialsForRequest, null, 2)
)

const presExchangeService = holder.dependencyManager.resolve(DifPresentationExchangeService)
// Automatically select credentials. In a wallet you could manually choose which credentials to return based on the "resolvedAuthorizationRequest.presentationExchange.credentialsForRequest" value
const selectedCredentials = presExchangeService.selectCredentialsForRequest(
  resolvedRequest.presentationExchange.credentialsForRequest
)

// issuer only supports pre-authorized flow for now
const authorizationResponse = await holder.modules.openId4VcHolderModule.acceptSiopAuthorizationRequest({
  authorizationRequest: resolvedRequest.authorizationRequest,
  presentationExchange: {
    credentials: selectedCredentials,
  },
})
console.log('Submitted authorization response', JSON.stringify(authorizationResponse.submittedResponse, null, 2))
```
{% endcode %}
