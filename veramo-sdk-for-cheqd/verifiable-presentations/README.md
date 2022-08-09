# Create Verifiable Presentations using Veramo CLI

This tutorial offers step-by-step guidance on how to create a [Verifiable Presentation](https://w3c-ccg.github.io/vp-request-spec/).

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup-cli.md) for Veramo CLI

## Background

A Verifiable Presentation is a way a credential holder can generate cryptographicall-verifiable proof that they are in posession of the identity keys (e.g., `did:key`) the credential was issued to.

This is important because [just the Verifiable Credential and its JWT proof](../verifiable-credentials/verify-jwt-vc.md), on its own, is susceptible to [*replay attacks*](https://en.wikipedia.org/wiki/Replay_attack) where intercepted Verifiable Credentials could be played back.

The process of creating a *Verifiable Presentation* is one way of addressing these security concerns.

## Instructions

### 1. Start generating Verifiable Presentation

Begin the process of creating a Verifiable Presentation using:

```bash
veramo presentation create
```

### 2. Choose holder/subject identity key to sign the presentation

Specify the subject identity key to be used for signing Verifiable Presentation, e.g., chosen holder `did:key`.

### 3. Specify the `Tag` for the Verifiable Presentation

Hit *Enter* to accept the default at this step

### 4. Specify the verifier ID

Select the DID of the verifier from the options presented. In a very simplistic scenario, the verifier could be the same as the issuer, e.g., a scenario where the holder returns a period of time to show they were issued a credential.

### 5. Specify presentation type

This can be the same as the Verifiable Credential, or a sub-section of it. Choose *Enter* for default.

### 6. Select the credential(s) to be included in the presentation

You can select one or more Verifiable Credentials to bundle up into the Verifiable Presentation.

### 7. View the Verifiable Presentation

After these choices are made, the Verifiable Presentation will be displayed in JSON format along with JWT proof.

You can save this output for future steps.

## Next steps

In a real-world usage scenario, the Verifiable Presentation output generated above would be electronically (e.g., as API messages) or non-electronically (e.g., encoded to a QR code) shared with the verifier/recipient.

Verifiers/recipients would then be able to [verify the Verifiable Presentation](verify-presentation.md) to check cryptographically the contents are untampered.
