# Verify a Verifiable Presentation using Veramo CLI

This tutorial offers step-by-step guidance on how to verify a [Verifiable Presentation](https://w3c-ccg.github.io/vp-request-spec/).

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup-cli.md) for Veramo CLI

## Instructions

The cryptographic verification in this tutorial will be based on JWT proof of a [previously-created Verifiable Presentation](README.md).

### 1. Call Veramo's `presentation verify` function

If you have the generated Verifiable Presentation stored, pass it to the `presentation verify` function:

```bash
cat pres.txt | veramo presentation verify
```

### 2. Check verification output

If the verification is successful, you'll receive this response:

```bash
Presentation verified successfully 
```
