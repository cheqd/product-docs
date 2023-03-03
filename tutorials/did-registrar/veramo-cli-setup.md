# 👉 Setting up Veramo CLI

If you're looking to use the Veramo CLI with cheqd or develop a proof-of-concept application, use the [official Veramo CLI setup guide](https://veramo.io/docs/veramo_agent/cli_tool/).

## Step 1: Install requisite packages

_Node version recommended `Nodev16`_. You can install Node [here](https://nodejs.org/en/download/)

### 1.1. Install Veramo CLI

This step is exactly [as described in Veramo CLI docs](https://veramo.io/docs/veramo_agent/cli_tool/):

```bash
npm i @veramo/cli@latest -g
```

_Note:_ Depending on your system permissions, you might be prompted for additional permissions. Add `sudo` to the beginning of the command in case that happens.

Verify the installation was correct. Command below should output latest version of veramo you installed.

```bash
veramo -v
x.x.x
```

## Step 2: Generate agent.yml file

```bash
veramo config
```
