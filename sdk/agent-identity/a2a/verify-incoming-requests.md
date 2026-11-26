---
description: Reject any A2A request without a verified, DID-anchored proof before your executor runs.
---

# A2A: Verify incoming requests

> **Draft tutorial** — walks through `@cheqd/agent-identity-a2a`'s current, real API, the same code path the package's own cross-language conformance suite runs. Not yet externally reviewed; the API may change before a stable release.

This wraps an [A2A](https://github.com/a2aproject/A2A) server's request handler so that no request reaches your executor without a verified caller identity, and so the response proof covers the *final* result the client actually receives — not whatever the executor produced before the SDK's own event-merge and history trimming.

Verification itself is checked against [KYA-OS](https://github.com/decentralized-identity/kya-os-mcp) (`@kya-os/mcp`)'s own proof profile — see [What this builds on](../README.md#what-this-builds-on).

## Install

```bash
npm install @cheqd/agent-identity-a2a@beta
```

## 1. Build a user-builder that verifies the request proof

```typescript
import { createVerifyingUserBuilder } from '@cheqd/agent-identity-a2a';

const userBuilder = createVerifyingUserBuilder({
  // Resolve the public key behind a given `kid` — see Setup.
  resolveKey,
  // List every key a given DID currently holds, for DID-membership checks.
  resolveDidKeys,
  expectedAudience: SERVER_DID,
  // A replay cache's consume(nonce) — true the first time a nonce is seen, false after.
  consumeNonceIfFresh: nonceCache.consume,
});
```

## 2. Wrap your request handler

```typescript
import { withAgentIdentity } from '@cheqd/agent-identity-a2a';
import { DefaultRequestHandler, InMemoryTaskStore } from '@a2a-js/sdk/server';
import { NodeCryptoProvider } from '@kya-os/mcp/providers';

const handler = withAgentIdentity(
  new DefaultRequestHandler(agentCard, new InMemoryTaskStore(), executor),
  {
    serverDid: SERVER_DID,
    serverKid: `${SERVER_DID}#key-1`,
    privateKey: serverCryptoKey,
    publicKey: serverPrivateJwk.d,
    cryptoProvider: new NodeCryptoProvider(),
  },
);
```

`withAgentIdentity` is the enforcing layer: any request whose user builder didn't mark it `verified` is rejected before the executor is ever called — a missing verifier looks identical to a missing proof, by design, so you cannot accidentally run unauthenticated by forgetting to wire one in.

## 3. Mount it

```typescript
import express from 'express';
import { jsonRpcHandler } from '@a2a-js/sdk/server/express';

const app = express();
app.use(express.json());
app.use(
  jsonRpcHandler({
    requestHandler: handler,
    userBuilder: async (req) => userBuilder(req),
  }),
);
```

A request with no proof, an expired proof, a replayed nonce, or a signature that doesn't verify against the resolved key is rejected here — your executor never sees it.

## Optional: require the trust extension

If you also want the caller's `did:cheqd` linkage and status checked (not just that *some* key signed the request), pass a `TrustExtensionGate` built from [`verifyTrustExtension`](../setup.md) alongside `withAgentIdentity`'s options — see `packages/a2a/src/trust-extension.ts` in the repository for the full shape.

## Next

[Sign outgoing requests](sign-outgoing-requests.md) on the client side, if you haven't already.
