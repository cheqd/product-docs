# Setup cheqd SDK

This guide will walk you through installing and setting up the cheqd TypeScript SDK in your project, with support for both ESM (ES Modules) and CommonJS (CJS) module systems.

## Prerequisites

### Node.js Version Requirements

The cheqd SDK requires **Node.js version 20 or later** for optimal performance and compatibility.

- **Recommended**: Node.js v22+ (LTS)
- **Minimum**: Node.js v20+

You can check your current Node.js version by running:

```bash
node --version
```

If you need to install or upgrade Node.js, visit the [official Node.js website](https://nodejs.org/) to download the latest LTS version.

### Package Manager

You can use any of the following package managers:

- **npm** (comes with Node.js)
- **yarn**
- **pnpm**

## Installation

### Install the cheqd SDK

Install the `@cheqd/sdk` package using your preferred package manager:

#### Using npm

```bash
npm install @cheqd/sdk
```

#### Using yarn

```bash
yarn add @cheqd/sdk
```

#### Using pnpm

```bash
pnpm add @cheqd/sdk
```

## Module System Setup

The cheqd SDK supports both ESM (ES Modules) and CommonJS module systems. Follow the appropriate setup for your project.

### ESM (ES Modules) Setup

#### 1. Configure package.json

Ensure your `package.json` includes the module type:

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "node --watch index.js"
  },
  "dependencies": {
    "@cheqd/sdk": "^latest"
  }
}
```

#### 2. Basic ESM Usage

Create an `index.js` file with ESM imports:

```javascript
import { CheqdSDK, createCheqdSDK } from '@cheqd/sdk';

// Initialize the SDK
const sdk = await createCheqdSDK({
  modules: [
    // Configure your modules here
  ],
  rpcUrl: 'https://rpc.cheqd.net', // mainnet
  // rpcUrl: 'https://rpc.cheqd.network', // testnet
});

console.log('cheqd SDK initialized successfully!');
```

#### 3. TypeScript ESM Setup

For TypeScript projects, create a `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Node",
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

And update your `package.json`:

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "tsc --watch"
  },
  "dependencies": {
    "@cheqd/sdk": "^latest"
  },
  "devDependencies": {
    "typescript": "^5.0.0",
    "@types/node": "^20.0.0"
  }
}
```

Create `src/index.ts`:

```typescript
import { CheqdSDK, createCheqdSDK, CheqdNetwork } from '@cheqd/sdk';

async function main() {
  try {
    const sdk = await createCheqdSDK({
      modules: [
        // Configure your modules
      ],
      rpcUrl: 'https://rpc.cheqd.net',
      network: CheqdNetwork.Mainnet
    });

    console.log('cheqd SDK initialized successfully!');
  } catch (error) {
    console.error('Failed to initialize SDK:', error);
  }
}

main();
```

### CommonJS Setup

#### 1. Configure package.json for CommonJS

For CommonJS projects, either omit the `type` field or set it to `"commonjs"`:

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "scripts": {
    "start": "node index.js",
    "dev": "node --watch index.js"
  },
  "dependencies": {
    "@cheqd/sdk": "^latest"
  }
}
```

#### 2. Basic CJS Usage

Create an `index.js` file with CommonJS require:

```javascript
const { createCheqdSDK } = require('@cheqd/sdk');

async function main() {
  try {
    // Initialize the SDK
    const sdk = await createCheqdSDK({
      modules: [
        // Configure your modules here
      ],
      rpcUrl: 'https://rpc.cheqd.net', // mainnet
      // rpcUrl: 'https://rpc.cheqd.network', // testnet
    });

    console.log('cheqd SDK initialized successfully!');
  } catch (error) {
    console.error('Failed to initialize SDK:', error);
  }
}

main();
```

#### 3. TypeScript CJS Setup

For TypeScript CommonJS projects, create a `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "CommonJS",
    "moduleResolution": "Node",
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

And update your `package.json`:

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "tsc --watch"
  },
  "dependencies": {
    "@cheqd/sdk": "^latest"
  },
  "devDependencies": {
    "typescript": "^5.0.0",
    "@types/node": "^20.0.0"
  }
}
```

Create `src/index.ts`:

```typescript
import { CheqdSDK, createCheqdSDK, CheqdNetwork } from '@cheqd/sdk';

async function main(): Promise<void> {
  try {
    const sdk: CheqdSDK = await createCheqdSDK({
      modules: [
        // Configure your modules
      ],
      rpcUrl: 'https://rpc.cheqd.net',
      network: CheqdNetwork.Mainnet
    });

    console.log('cheqd SDK initialized successfully!');
  } catch (error) {
    console.error('Failed to initialize SDK:', error);
  }
}

main();
```

## Network Configuration

The cheqd SDK supports both mainnet and testnet networks. You can configure the network by setting the `rpcUrl` and `network` parameters when initializing the SDK. Use the enum `CheqdNetwork` to specify the network type.

### Mainnet Configuration

```javascript
const sdk = await createCheqdSDK({
  rpcUrl: 'https://rpc.cheqd.net', // or your preferred mainnet RPC endpoint
  network: CheqdNetwork.Mainnet
});
```

### Testnet Configuration

For development and testing purposes:

```javascript
const sdk = await createCheqdSDK({
  rpcUrl: 'https://rpc.cheqd.network', // or your preferred testnet RPC endpoint
  network: CheqdNetwork.Testnet
});
```

## Verification

After installation, you can verify the setup by running your project:

### For JavaScript projects

```bash
npm start
# or
yarn start
# or
pnpm start
```

### For TypeScript projects

```bash
# Build first
npm run build
# Then run
npm start

# Or for development with watch mode
npm run dev
```

You should see the message "cheqd SDK initialized successfully!" if everything is set up correctly.

## Troubleshooting

### Common Issues

#### Node.js Version Issues

If you encounter compatibility issues, ensure you're using Node.js v22 or later:

```bash
node --version
```

#### Module System Conflicts

If you get module-related errors:

1. **ESM projects**: Ensure `"type": "module"` is in your `package.json`
2. **CJS projects**: Either omit the `type` field or set `"type": "commonjs"`

#### TypeScript Configuration

For TypeScript projects, ensure your `tsconfig.json` has the correct `module` and `target` settings for your chosen module system.

### Getting Help

If you encounter issues:

1. Check the [cheqd SDK GitHub repository](https://github.com/cheqd/sdk) for the latest documentation
2. Review the [NPM package page](https://www.npmjs.com/package/@cheqd/sdk) for version-specific notes
3. Consult the [cheqd documentation](https://docs.cheqd.io/) for comprehensive guides

## Next Steps

Once you have the SDK installed and configured, you can:

- [Create a Decentralized Identifier (DID)](./did-module/create-did.md)
- [Manage DIDs](./did-module/manage-did.md)
- [Create DID-Linked Resources](./resource-module/create-resource.md)
- [Manage DID-Linked Resources](./resource-module/manage-resource.md)
- Explore the full SDK functionality in your chosen module system

The cheqd SDK provides a powerful and flexible foundation for building decentralized identity solutions on the cheqd network, with full support for modern JavaScript/TypeScript development practices.
