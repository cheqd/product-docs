---
description: Configure DID resolution and Agent Card verification, shared by both bindings.
---

# Setup Agent Identity

> **Draft tutorial** — walks through `@cheqd/agent-identity`'s current, real API. Not yet externally reviewed; the API may change before a stable release.

Both the [A2A](a2a/sign-outgoing-requests.md) and [AP2](ap2/issue-mandate-presentation.md) bindings resolve the keys behind a DID through this package. This page sets that part up once.

## Install

```bash
npm install @cheqd/agent-identity@beta
```

## Resolve `did:cheqd` keys

`createDidCheqdResolver` builds a resolver function against a universal-resolver-shaped endpoint (the public `resolver.cheqd.net`, or your own):

```typescript
import { createDidCheqdResolver } from '@cheqd/agent-identity';

const resolveDidCheqd = createDidCheqdResolver({
  resolverUrl: 'https://resolver.cheqd.net/1.0/identifiers',
});

const document = await resolveDidCheqd('did:cheqd:testnet:z6Mk...');
```

This fails closed: a non-200 response, invalid JSON, a document missing `id`, or a document whose `id` doesn't match the DID you asked for all resolve to `null`, never throw. A deactivated DID resolves successfully but comes back with `deactivated: true` on the document — its keys are then refused downstream, not silently trusted.

If you resolve `did:cheqd` documents some other way (for example through a Credo agent's own `DidsApi`), copy `didDocumentMetadata.deactivated` onto the document yourself — the key-resolution functions below rely on that flag being present, not on re-deriving it.

## Resolve the key behind a signature

`createRetrievePublicKey` builds the callback `@a2a-js/sdk`'s `verifyAgentCardSignature` expects, resolving `did:web` and `did:key` natively and `did:cheqd` through the resolver above:

```typescript
import { createRetrievePublicKey } from '@cheqd/agent-identity';

const retrievePublicKey = createRetrievePublicKey({
  // Reject any kid whose DID doesn't match the agent you expect to be talking to.
  expectedDid: 'did:cheqd:testnet:z6Mk...',
  resolveDidCheqd,
});
```

`expectedDid` matters: without it, a signature claiming to be from *any* DID verifies as long as its own key checks out — pin it to the DID you actually expect, not just whatever the message claims.

## Next steps

* [A2A: Sign outgoing requests](a2a/sign-outgoing-requests.md) and [verify incoming requests](a2a/verify-incoming-requests.md)
* [AP2: Issue](ap2/issue-mandate-presentation.md) and [verify](ap2/verify-mandate-presentation.md) a mandate presentation
