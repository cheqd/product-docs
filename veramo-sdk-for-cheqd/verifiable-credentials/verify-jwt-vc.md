# Verify JSON/JWT Verifiable Credential using Veramo CLI

This tutorial offers step-by-step guidance on how to verify a [JSON credential](https://www.w3.org/TR/vc-data-model/#json), encoded as a [JWT (JSON Web Token)](https://www.w3.org/TR/vc-data-model/#json-web-token), a [W3C compliant proof format.](https://www.w3.org/TR/vc-data-model/#proof-formats)

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../setup-cli.md) for Veramo CLI

## Instructions

This tutorial assumes you have [already issued a JSON/JWT Verifiable Credential](README.md) so that it can be cryptographically verified as untampered.

### Call Veramo's `credential verify` function

If you have the [issued Verifiable Credential](README.md) stored in a file, say, `cred.json`, pass it to the `credential verify` function:

```bash
cat cred.json | veramo credential verify
```

Instead of piping the file like above, you can also verify the credential using the raw credential or JWT as follows:

```bash
veramo credential verify --raw
```

Then enter the JWT or entire JSON credential including the JWT.

### Check credential verification output

If the credential can be verified correctly, the response will be:

```bash
Credential verified successfully
```

Credential verification is done by decoding the JWT proof and checking if it's tampered or not. Feel free to test this yourself by tampering with the `jwt` contents in the credential:

```bash
Credential could not be verified
```

### 🤨 Troubleshoot verifying JWT credentials

If you belive you did everything right(created a verifiable credential and you did not tamper it), and for some reason you're getting

```bash
Credential could not be verified
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
veramo credential verify -r <enter-the-JWT-here>
```

## Next steps

Look into [how this credential can be shown to others](../verifiable-presentations/README.md) through Verifiable Presentations.
