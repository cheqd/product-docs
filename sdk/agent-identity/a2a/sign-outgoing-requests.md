---
description: Add a DID-anchored, per-request proof to every SendMessage call.
---

# A2A: Sign outgoing requests

> **Draft tutorial** — walks through `@cheqd/agent-identity-a2a`'s current, real API, the same code path the package's own cross-language conformance suite runs. Not yet externally reviewed; the API may change before a stable release.

This adds a `CallInterceptor` to an [A2A](https://github.com/a2aproject/A2A) client ([`@a2a-js/sdk`](https://github.com/a2aproject/a2a-js)) that signs every outgoing `SendMessage` with a holder-of-key proof (`org.kya-os/proof.v1`) before it leaves the client, and verifies the server's response proof on the way back — failing the call itself if that check fails, not just recording a result for you to remember to check.

## Install

```bash
npm install @cheqd/agent-identity-a2a@beta
```

## 1. Build a signer for your client's key

```typescript
import { ed25519SignerFromJwk } from '@kya-os/mcp/card';

const CLIENT_DID = 'did:web:client.example';
const clientKid = `${CLIENT_DID}#key-1`;

const signer = await ed25519SignerFromJwk({
  did: CLIENT_DID,
  kid: clientKid,
  privateJwk: clientPrivateJwk, // your Ed25519 private JWK
});
```

## 2. Create the proof interceptor

You need the server's public key pinned ahead of time — the response-proof check does not do a DID-membership lookup for you, by design (see `docs/design-decisions.md`); resolve and pin it yourself, for example with [`createRetrievePublicKey`](../setup.md):

```typescript
import { createProofInterceptor } from '@cheqd/agent-identity-a2a';
import { NodeCryptoProvider, SystemClockProvider, MemoryNonceCacheProvider, RuntimeFetchProvider } from '@kya-os/mcp/providers';

const EXT_URI = 'https://kya-os.org/a2a/ext/proof/v1';
const SERVER_DID = 'did:web:server.example';

const { interceptor, state } = createProofInterceptor({
  signer,
  clientDid: CLIENT_DID,
  serverDid: SERVER_DID,
  serverPublicJwk: { ...serverPublicJwk, kid: `${SERVER_DID}#key-1` },
  extensionUri: EXT_URI,
  cryptoProvider: new NodeCryptoProvider(),
  clockProvider: new SystemClockProvider(),
  nonceCacheProvider: new MemoryNonceCacheProvider(),
  fetchProvider: new RuntimeFetchProvider(),
});
```

`state.lastResponseCheck` holds the result of the most recent response-proof verification, for logging — not for deciding whether to trust the result. A failed check already threw out of `sendMessage()` by the time you'd read it.

> `MemoryNonceCacheProvider` is single-process, development-only — a real deployment needs a cache that survives a restart. `@cheqd/agent-identity`'s `FileNonceCache` is one option; see its README.

## 3. Wire it into the client

```typescript
import { ClientFactory, ClientFactoryOptions, JsonRpcTransportFactory } from '@a2a-js/sdk/client';

const factory = new ClientFactory(
  ClientFactoryOptions.createFrom(ClientFactoryOptions.default, {
    transports: [new JsonRpcTransportFactory()],
    clientConfig: { interceptors: [interceptor] },
  }),
);

const client = await factory.createFromAgentCard(agentCard);
```

## 4. Send a message

No change to how you call the SDK — the interceptor does the work:

```typescript
const task = await client.sendMessage({
  tenant: '',
  message: {
    messageId: 'm1',
    role: Role.ROLE_USER,
    parts: [{ content: { $case: 'text', value: 'hello' }, mediaType: 'text/plain' }],
  },
});
```

If the server's response proof fails to verify, this call rejects — there is no success path where an unverified response reaches your code.

## Next

[Verify incoming requests](verify-incoming-requests.md) on the server side.
