# Setup the cheqd SDK

This guide takes you from an empty folder to a connected cheqd SDK with a funded testnet wallet, ready to [create a DID](did-module/create-did.md). It uses TypeScript and ES modules; a CommonJS note is at the end.

{% hint style="info" %}
**Want a working project instead?** The [cheqd SDK quickstart](https://github.com/cheqd/cheqd-sdk-quickstart) runs the whole journey on testnet with one script per step: create a wallet, fund it from the [Testnet Faucet](../../network/tools/testnet-faucet.md), then create, resolve, update and deactivate a DID and create a DID-Linked Resource. It also includes instructions for coding agents. See [Use with a coding agent](#use-with-a-coding-agent).
{% endhint %}

## Prerequisites

* **Node.js 22 or later.** `@cheqd/sdk` declares `"engines": { "node": ">=22.0.0" }`. Check with `node --version`.
* A package manager: npm, yarn or pnpm.

## Install

```bash
mkdir my-cheqd-app && cd my-cheqd-app
npm init -y
npm pkg set type=module
npm install @cheqd/sdk
npm install --save-dev typescript tsx @types/node
```

{% hint style="warning" %}
Do not install `@cosmjs/*` packages separately unless you need them. `@cheqd/sdk` depends on `@cosmjs/*@^0.36.2`. CosmJS is a 0.x library, so a newer version (for example 0.39) is installed as a second, incompatible copy and your `wallet` will stop type-checking with an error such as `Type '"eth_secp256k1"' is not assignable to type 'Algo'`. If you need one, pin it: `npm install @cosmjs/proto-signing@^0.36.2`.
{% endhint %}

## Choose a network

|                                 | RPC endpoint                | Chain ID          |
| ------------------------------- | --------------------------- | ----------------- |
| **Testnet** (use this to start) | `https://rpc.cheqd.network` | `cheqd-testnet-6` |
| **Mainnet**                     | `https://rpc.cheqd.net`     | `cheqd-mainnet-1` |

{% hint style="info" %}
The two domains differ by one word: `.network` is testnet and `.net` is mainnet. Always set `network` to match the RPC URL you use (`CheqdNetwork.Testnet` or `CheqdNetwork.Mainnet`). To check which chain an endpoint serves, run `curl -s https://rpc.cheqd.network/status` and read `node_info.network`.
{% endhint %}

## Create a wallet and fund it

Every transaction is paid for by a **fee payer**: a standard Cosmos wallet with a `cheqd1…` address. This is separate from the keys that control your DIDs, which you create later.

Generate a mnemonic and print the address (`wallet.ts`):

```typescript
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';

const wallet = await DirectSecp256k1HdWallet.generate(24, { prefix: 'cheqd' });
const [{ address }] = await wallet.getAccounts();

console.log(`Mnemonic (store it safely, never commit it): ${wallet.mnemonic}`);
console.log(`Address: ${address}`);
```

This needs `npm install @cosmjs/proto-signing@^0.36.2` (the version the SDK uses). Save the mnemonic in an environment variable such as `CHEQD_MNEMONIC`, and keep it out of source control.

Then fund the address with testnet CHEQ: see [Testnet Faucet](../../network/tools/testnet-faucet.md). A new account does not exist on chain until it has received tokens; until then the SDK throws `Account 'cheqd1…' does not exist on chain`.

{% hint style="info" %}
Fees are priced in USD and paid in CHEQ at the current rate, so the CHEQ amount changes with the CHEQ price. Testnet CHEQ is cheap, so request a few thousand to start.
{% endhint %}

## Connect to the network

`connect.ts`:

```typescript
import {
  CheqdNetwork,
  DIDModule,
  ResourceModule,
  checkBalance,
  createCheqdSDK,
  createCosmosPayerWallet,
  type AbstractCheqdSDKModule,
} from '@cheqd/sdk';

const rpcUrl = 'https://rpc.cheqd.network'; // testnet

// createCosmosPayerWallet accepts a mnemonic (or a hex private key) and uses the 'cheqd' prefix.
const wallet = await createCosmosPayerWallet(process.env.CHEQD_MNEMONIC!);
const [{ address }] = await wallet.getAccounts();

const sdk = await createCheqdSDK({
  modules: [
    DIDModule as unknown as AbstractCheqdSDKModule,
    ResourceModule as unknown as AbstractCheqdSDKModule,
  ],
  rpcUrl,
  network: CheqdNetwork.Testnet,
  wallet,
});

console.log('cheqd SDK initialised for', address);
console.log('Balances:', await checkBalance(address, rpcUrl));
```

Run it with `npx tsx connect.ts`. You should see your address and a balance in `ncheq` (1 CHEQ = 10^9 ncheq).

{% hint style="info" %}
The `as unknown as AbstractCheqdSDKModule` cast is needed for the types to line up with the current SDK version.
{% endhint %}

## Next steps

* [Create a DID](did-module/create-did.md)
* [Manage a DID](did-module/manage-a-did.md)
* [Create a DID-Linked Resource](resource-module/create-resource.md)
* [Manage a DID-Linked Resource](resource-module/manage-resource.md)
* [Pay fees with other tokens](fee-abstraction/README.md)

## Use with a coding agent

The [cheqd SDK quickstart](https://github.com/cheqd/cheqd-sdk-quickstart) is set up for coding agents that read `AGENTS.md`, and for Claude Code:

* `AGENTS.md` tells an agent how to use `@cheqd/sdk` safely: testnet only, never print the mnemonic, and the current SDK behaviours to work around.
* A Claude Code skill (`.claude/skills/cheqd-sdk-quickstart`) runs the journey and reports the DID it created.
* `node bin/create-cheqd-sdk-app.mjs <folder>` copies the scripts and agent files into a new project, without any keys.

You still create the cheqd Studio account and API key yourself: the agent stops at the funding step and prints what to do.

## CommonJS

The package also ships a CommonJS build, so `require` works with the same install:

```javascript
const { createCheqdSDK, createCosmosPayerWallet, DIDModule, CheqdNetwork } = require('@cheqd/sdk');
```

The rest of the setup is the same. If you are not sure which module system your project uses, check for `"type": "module"` in `package.json`.

## Troubleshooting

| Symptom                                                 | Cause and fix                                                                                                                                                       |
| ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account 'cheqd1…' does not exist on chain`             | The fee-payer wallet has never received tokens. Fund it (see above).                                                                                                |
| Type error mentioning `eth_secp256k1` or `Algo`         | A second `@cosmjs/*` copy is installed. Remove it or pin it to `^0.36.2`.                                                                                           |
| `missing properties from type 'AbstractCheqdSDKModule'` | Add the `as unknown as AbstractCheqdSDKModule` cast to each module.                                                                                                 |
| `Cannot read properties of undefined (reading 'sdk')`   | You called a transaction method on `sdk` with fewer arguments than its full signature. Pass every optional argument, using `undefined` for the ones you do not need. |
| Module or `import` errors                               | Make sure `"type": "module"` is in `package.json` (ES modules) or use `require` (CommonJS).                                                                         |
| Wrong network                                           | Check the RPC URL and `network` match: `.network` is testnet, `.net` is mainnet.                                                                                    |

For anything else, see the [cheqd SDK repository](https://github.com/cheqd/sdk) and the [`@cheqd/sdk` package page](https://www.npmjs.com/package/@cheqd/sdk).
