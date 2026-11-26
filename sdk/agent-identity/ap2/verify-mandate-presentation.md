---
description: Verify a presented mandate, its key binding, and that it covers the transaction actually being made.
---

# AP2: Verify a mandate presentation

> **Draft tutorial** — walks through `@cheqd/agent-identity-ap2`'s current, real API, the same wire format proven cross-language-conformant with the Python port in `conformance/cross-language-ap2-sd-jwt.mjs`. Not yet externally reviewed; the API may change before a stable release.

Verifying a presented [AP2](https://github.com/google-agentic-commerce/AP2) mandate is two separate checks, both required: that the presentation itself is valid (correctly signed, fresh, bound to the right audience and nonce), and that it's bound to *this* transaction, not a different one with the same mandate replayed.

## Install

```bash
npm install @cheqd/agent-identity-ap2@beta
```

## 1. Verify the presentation

```typescript
import { verifyOpenPaymentMandatePresentation } from '@cheqd/agent-identity-ap2';

const result = await verifyOpenPaymentMandatePresentation({
  token, // the compact SD-JWT-VC ~ KB-JWT string you received
  issuerPublicJwk, // the issuer's key, resolved and pinned by you — not read from the token
  expectedAudience: 'did:web:merchant.example', // your own DID
  expectedNonce: nonceYouIssued,
});
```

This checks the issuer's signature over the SD-JWT-VC, the holder's Key Binding JWT signature against the mandate's own `cnf.jwk`, and that `aud`/`nonce` match what you expected — not just that *some* signature verifies. `result.cnfJwk` is the holder key the presentation was actually bound to; compare it against whatever key you expected the holder to use, if that matters to your flow.

Use `verifyOpenCheckoutMandatePresentation` instead for a `mandate.checkout.open.1` mandate — same shape, different `vct`.

## 2. Verify it covers the transaction you're actually processing

A valid presentation proves the holder signed *some* transaction binding — not necessarily the one you think you're processing:

```typescript
import { verifyTransactionDataBinding } from '@cheqd/agent-identity-ap2';

const ok = verifyTransactionDataBinding(result.keyBinding, closedPaymentContent);
if (!ok) {
  throw new Error('transaction_data_binding_failed');
}
```

`closedPaymentContent` here must be byte-for-byte the same object (same keys, same values) the issuing side hashed in [Issue a mandate presentation](issue-mandate-presentation.md) — `verifyTransactionDataBinding` recomputes the same RFC 8785 canonical hash and compares it against what's in the presentation's `transaction_data`. A mismatch means the holder bound their signature to a different transaction than the one you're about to act on; treat it as a hard failure, not a warning.

## Next

If you also need to confirm the holder's key is authorised to spend under a DID-anchored policy (not just that the mandate chain is internally consistent), see `verifyAgentPaymentKeyCredential` and `verifySameKeyContinuity`/`verifyCnfContinuity` in the package's own README.
