# 🧑‍💻🛠 Setting up Veramo SDK for cheqd

## Setting up Veramo CLI with cheqd configuration

If you're looking to use the Veramo CLI with cheqd or develop a proof-of-concept application, use the [official Veramo CLI setup guide](https://veramo.io/docs/veramo_agent/cli_tool/).

### 1. Download Veramo CLI

### 2. Generate a new local database encryption key

By default, the `did-provider-cheqd` package has a default SQLite database password, but it's a good idea to modify and change this somethinhg unique to your install.

```bash
veramo config gen-key
```

### 3. Modify `agent.yaml` configuration

## Setting up for frameworks supported by Veramo

Depending on the type of application you are looking to develop, you will need to install a different set of packages.

For other applications, see:

* [Veramo Node.js tutorial](https://veramo.io/docs/node_tutorials/node_setup_identifiers)
* [Veramo React tutorial](https://veramo.io/docs/react_tutorials/react_setup_resolver)
* [Veramo React Native tutorial](https://veramo.io/docs/react_native_tutorials/react_native_setup_identifiers)
