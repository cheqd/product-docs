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
npm install @cheqd/sdk @cosmjs/proto-signing@^0.36.2
npm install --save-dev typescript tsx @types/node
echo ".env" >> .gitignore
```

{% hint style="warning" %}
Install `@cosmjs/*` packages only at the version the SDK uses, as above. `@cheqd/sdk` depends on `@cosmjs/*@^0.36.2`. CosmJS is a 0.x library, so a newer version (for example 0.39) is installed as a second, incompatible copy and your `wallet` will stop type-checking with an error such as `Type '"eth_secp256k1"' is not assignable to type 'Algo'`. Pin any other `@cosmjs/*` package you need to `^0.36.2` too.
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

Generate a wallet and save its mnemonic to `.env` (`wallet.ts`):

```typescript
import { appendFileSync } from 'node:fs';
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';

const wallet = await DirectSecp256k1HdWallet.generate(24, { prefix: 'cheqd' });
const [{ address }] = await wallet.getAccounts();

// The mnemonic controls the wallet. It goes in .env, which the install step added to .gitignore.
appendFileSync('.env', `CHEQD_MNEMONIC="${wallet.mnemonic}"\n`);
console.log(`Saved the mnemonic to .env. Address: ${address}`);
```

Run it once with `npx tsx wallet.ts`. Never commit `.env`, and never paste its contents into a chat or a ticket.

Then fund the address with testnet CHEQ:

1. [Sign up for cheqd Studio](https://studio.cheqd.net/?action=register) if you have not already. New accounts start a 30-day trial that includes the faucet.
2. Open **Account** > **Balances & Faucet**, paste the address printed above and request tokens. The [Testnet Faucet](../../network/tools/testnet-faucet.md) page also covers the API.

A new account does not exist on chain until it has received tokens; until then the SDK throws `Account 'cheqd1…' does not exist on chain`.

{% hint style="info" %}
Fees are priced in USD and paid in CHEQ at the current rate, so the CHEQ amount changes with the CHEQ price. Testnet CHEQ is cheap, so request a few thousand to start.
{% endhint %}

## Connect to the network

`connect.ts` creates the `sdk` and `feePayer` that the other guides use:

```typescript
import {
  CheqdNetwork,
  DIDModule,
  ResourceModule,
  createCheqdSDK,
  createCosmosPayerWallet,
  type AbstractCheqdSDKModule,
} from '@cheqd/sdk';

process.loadEnvFile('.env'); // loads CHEQD_MNEMONIC, saved by wallet.ts

export const rpcUrl = 'https://rpc.cheqd.network'; // testnet

// createCosmosPayerWallet accepts a mnemonic (or a hex private key) and uses the 'cheqd' prefix.
const wallet = await createCosmosPayerWallet(process.env.CHEQD_MNEMONIC!);
const [{ address }] = await wallet.getAccounts();

// The fee payer is the wallet address that pays for transactions.
export const feePayer = address;

export const sdk = await createCheqdSDK({
  modules: [
    DIDModule as unknown as AbstractCheqdSDKModule,
    ResourceModule as unknown as AbstractCheqdSDKModule,
  ],
  rpcUrl,
  network: CheqdNetwork.Testnet,
  wallet,
});
```

Check that it works with `check.ts`:

```typescript
import { checkBalance } from '@cheqd/sdk';
import { feePayer, rpcUrl } from './connect.js';

console.log('Fee payer:', feePayer);
console.log('Balances:', await checkBalance(feePayer, rpcUrl));
```

Run it with `npx tsx check.ts`. You should see your address and a balance in `ncheq` (1 CHEQ = 10^9 ncheq). An empty list means the wallet has not been funded yet.

In the other guides, add `import { sdk, feePayer } from './connect.js';` at the top of your file.

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

The [cheqd SDK quickstart](https://github.com/cheqd/cheqd-sdk-quickstart) works with coding agents. Open it in your agent and ask it to create a cheqd DID on testnet:

| Agent | Instructions it reads | Skill it reads |
| --- | --- | --- |
| Claude Code | `CLAUDE.md`, which imports `AGENTS.md` | `.claude/skills/` |
| OpenAI Codex (CLI, IDE, app) | `AGENTS.md` | `.agents/skills/` |
| Cursor | `AGENTS.md` | `.agents/skills/` |
| GitHub Copilot (VS Code agent mode, CLI, cloud agent) | `AGENTS.md` | `.agents/skills/` |
| Gemini CLI | `AGENTS.md`, via `.gemini/settings.json` | `.agents/skills/` |

* `AGENTS.md` tells an agent how to use `@cheqd/sdk` safely: testnet only, never print the mnemonic, and the current SDK behaviours to work around.
* The `cheqd-sdk-quickstart` skill runs the journey and reports the DID it created.
* `node bin/create-cheqd-sdk-app.mjs <folder>` copies the scripts and agent files into a new project, without any keys.

You still create the cheqd Studio account and API key yourself: the funding step stops and prints what to do if `STUDIO_API_KEY` is missing.

{% hint style="info" %}
**Cloud agents** (Codex cloud, Copilot cloud agent, Jules) run in a remote sandbox with limited internet access. Allow `rpc.cheqd.network`, `studio-api.cheqd.net` and `resolver.cheqd.net`, and give the agent `STUDIO_API_KEY` as an environment variable. See the quickstart [README](https://github.com/cheqd/cheqd-sdk-quickstart#use-with-a-coding-agent).

The ChatGPT chat app cannot run the quickstart, because it has no shell. Use Codex instead.
{% endhint %}

## CommonJS

The package also ships a CommonJS build, so `require` works with the same install:

```javascript
const { createCheqdSDK, createCosmosPayerWallet, DIDModule, CheqdNetwork } = require('@cheqd/sdk');
```

The examples in this guide use ES modules, `import` and top-level `await`. In a CommonJS project, put the code inside an `async` function, replace `import` with `require` and drop the `!` after `process.env.CHEQD_MNEMONIC` (it is TypeScript-only). If you are not sure which module system your project uses, check for `"type": "module"` in `package.json`.

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
