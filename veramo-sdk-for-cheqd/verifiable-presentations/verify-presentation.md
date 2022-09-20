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

### 🤨 Troubleshoot verifying JWT presentations

If you belive you did everything right(created a verifiable presentation and you did not tamper it), and for some reason you're getting

```bash
Presentation could not be verified
```

#### You can fix it this way:

1. First, make sure you are using a `node` with version `16` or above.
2. Second, Change veramo cli version to `@veramo/cli@3.1.6-next.170 -g` as previous versions cause few issues in verification.

> This will override the previous version you have installed.

```bash
npm install -g @veramo/cli@3.1.6-next.170 -g
```

3. Run command below with your token.

```bash
veramo presentation verify -r <enter-the-JWT-here>
```
