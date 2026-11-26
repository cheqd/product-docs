---
description: Issue an Open Payment Mandate as a compact SD-JWT-VC, bound to a holder's key.
---

# AP2: Issue a mandate presentation

> **Draft tutorial** — walks through `@cheqd/agent-identity-ap2`'s current, real API, the same wire format proven cross-language-conformant with the Python port in `conformance/cross-language-ap2-sd-jwt.mjs`. Not yet externally reviewed; the API may change before a stable release.

An [AP2](https://github.com/google-agentic-commerce/AP2) Open Payment Mandate is issued as an SD-JWT-VC, then presented joined with a Key Binding JWT that proves the holder controls the key the mandate's `cnf` names, and binds the presentation to a specific transaction. This package supplies the hashing and `cnf`-verification pieces; the SD-JWT-VC construction itself uses [`@sd-jwt/core`](https://github.com/openwallet-foundation/sd-jwt-js) directly — this package does not reimplement it.

## Install

```bash
npm install @cheqd/agent-identity-ap2@beta
```

## 1. Issue the mandate

```typescript
import { SDJwtInstance } from '@sd-jwt/core';
import { sha256Hasher } from '@cheqd/agent-identity-ap2';

const issuerInstance = new SDJwtInstance({
  hasher: sha256Hasher,
  saltGenerator: (length) => /* your random base64url generator */,
  signer: issuerSigner, // signs with the issuer's private key
  signAlg: 'EdDSA',
});

const issued = await issuerInstance.issue({
  vct: 'mandate.payment.open.1', // OPEN_PAYMENT_MANDATE_VCT
  constraints: [{ type: 'payment.amount_range', currency: 'USD', max: 5000 }],
  cnf: { jwk: holderPublicJwk }, // the holder's key, not the issuer's
});
```

`vct` must be exactly `mandate.payment.open.1` (exported as `OPEN_PAYMENT_MANDATE_VCT`) for a payment mandate, or `mandate.checkout.open.1` (`OPEN_CHECKOUT_MANDATE_VCT`) for a checkout mandate — the verifier checks this explicitly, it is not advisory metadata.

## 2. Present it, bound to a specific transaction

```typescript
import { transactionDataHash } from '@cheqd/agent-identity-ap2';

const presenterInstance = new SDJwtInstance({
  hasher: sha256Hasher,
  saltGenerator: (length) => /* same generator as above */,
  kbSigner: holderSigner, // signs with the holder's private key — the one named in cnf
  kbSignAlg: 'EdDSA',
});

const token = await presenterInstance.present(issued, undefined, {
  kb: {
    payload: {
      iat: Math.floor(Date.now() / 1000),
      aud: 'did:web:merchant.example', // the verifier you're presenting to
      nonce: serverSuppliedNonce,
      transaction_data: [transactionDataHash(closedPaymentContent)],
    },
  },
});
```

`transactionDataHash` is an RFC 8785 (JCS) canonical hash of the actual closed-payment content — not a free-form string. The verifier recomputes this hash from its own copy of the transaction and compares; the two sides must serialise the exact same object for the hashes to agree (see `docs/design-decisions.md` D19, which found and fixed exactly this kind of mismatch between the TypeScript and Python ports).

`token` is the compact string you send: the issuer's SD-JWT-VC, `~`-joined with the Key Binding JWT.

## Next

[Verify a mandate presentation](verify-mandate-presentation.md) on the receiving side.
