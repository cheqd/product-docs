---
description: >-
    Get started with the cheqd TypeScript SDK, an NPM package providing the core functionality to create and manage Decentralized Identifiers (DIDs) and DID-Linked Resources (DLRs) on the cheqd network.
---

# 📜 SDK

## Introduction

The [cheqd TypeScript SDK](https://github.com/cheqd/sdk) is an [NPM package](https://www.npmjs.com/package/@cheqd/sdk) providing the core functionality to create and manage Decentralized Identifiers (DIDs) and DID-Linked Resources (DLRs) on the cheqd network in a simple and intuitive way.

The SDK also provides interfaces to interact with the cheqd network, including sending transactions and querying data, as well as utilities for key management and cryptographic operations.

Transaction fees can alternatively be paid in IBC-transferred tokens accepted by the cheqd network through governance proposals. This allows for greater flexibility in how users can manage their transaction costs. For example, paying fees in a different token can help users avoid price volatility in the native token by using MiCA-compliant stablecoins such as USDC, EURC, etc. This is achieved through the use of fee-abstraction module over IBC.For more information on IBC transfers, see the [IBC documentation](https://ibc.cosmos.network/v8/).

The SDK is designed to be easy to use and integrate into existing applications, making it a great choice for developers looking to build decentralized identity solutions on the cheqd network, fine-tuning their applications to meet specific needs.

## Explore the SDK

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Get started with the cheqd SDK</strong></mark></td><td>Learn how to install and set up the cheqd SDK in your project.</td><td><a href="sdk-setup.md">sdk-setup.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create a Decentralized Identifier (DID)</strong></mark></td><td>Create a Decentralized Identifier (DID) using the cheqd SDK.</td><td><a href="create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Manage a Decentralized Identifier (DID)</strong></mark></td><td>Learn how to update and deactivate a Decentralized Identifier (DID) using the cheqd SDK.</td><td><a href="manage-did.md">manage-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create a DID-Linked Resource (DLR)</strong></mark></td><td>Create a DID-Linked Resource (DLR) using the cheqd SDK.</td><td><a href="create-resource.md">create-resource.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Manage a DID-Linked Resource (DLR)</strong></mark></td><td>Learn how to update and delete a DID-Linked Resource (DLR) using the cheqd SDK.</td><td><a href="manage-resource.md">manage-resource.md</a></td></tr></tbody></table>

## Explore Advanced SDK Features

The cheqd SDK provides advanced modules for comprehensive identity and transaction management. Each module supports both traditional native token fees and modern fee abstraction capabilities.

### Core Identity Modules

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DID Module</strong></mark></td><td>Complete DID lifecycle management with support for fee abstraction using IBC tokens (USDC, EURe, OSMO) and dynamic feemarket pricing for optimal transaction costs.</td><td><a href="did-module/">did-module</a></td></tr><tr><td><mark style="color:blue;"><strong>Resource Module</strong></mark></td><td>DID-Linked Resource creation and management with flexible fee payment options including fee abstraction for multi-token support and feemarket integration for cost optimization.</td><td><a href="resource-module/">resource-module</a></td></tr></tbody></table>

### Fee Management Modules

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Fee Abstraction Module</strong></mark></td><td>Pay transaction fees using alternative IBC tokens (USDC, EURe, OSMO) instead of native CHEQ tokens. Enables cross-chain fee payment and stablecoin fee strategies.</td><td><a href="fee-abstraction/">fee-abstraction</a></td></tr><tr><td><mark style="color:blue;"><strong>Feemarket Module</strong></mark></td><td>Dynamic fee calculation based on network congestion for standard Cosmos SDK transactions. Provides real-time gas pricing and fee optimization strategies.</td><td><a href="feemarket-module/">feemarket-module</a></td></tr></tbody></table>

### Module Integration Patterns

#### Fee Abstraction Integration

All SDK modules support fee abstraction, allowing you to:

- **Multi-Token Fees**: Pay transaction fees in USDC, EURe, or OSMO
- **Stablecoin Strategy**: Use MiCA-compliant stablecoins for predictable fee costs
- **Cross-Chain Compatibility**: Accept IBC-transferred tokens from connected chains
- **Governance Integration**: Support tokens approved through network governance

**Example**: Create a DID while paying fees in USDC:
```typescript
const feeInUsdc = {
  amount: [coin('12000', 'ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013')],
  gas: '360000',
  payer: feePayer
};

await sdk.createDidDocTx(signInputs, didPayload, feePayer, feeInUsdc);
```

#### Feemarket Integration

Standard Cosmos SDK transactions benefit from dynamic pricing:

- **Real-Time Pricing**: Gas prices adjust based on network demand
- **Cost Optimization**: Lower fees during network low-activity periods
- **Congestion Management**: Automatic fee adjustment for transaction prioritization
- **API Integration**: Direct access to current gas prices via network endpoints

**Example**: Token transfer with dynamic fees:
```typescript
const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
const gasPrice = await feemarketModule.generateSafeGasPriceByDenom('ncheq');
const fee = FeemarketModule.generateFeesFromGasPrice(gasPrice, sender, '200000');

await sdk.signer.signAndBroadcast(sender, [transferMsg], fee);
```

### Fee Strategy Decision Matrix

| Transaction Type | Native CHEQ | Fee Abstraction | Dynamic Feemarket | Best Use Case |
|-----------------|-------------|-----------------|-------------------|---------------|
| **DID Operations** | ✅ Fixed pricing | ✅ USDC/EURe/OSMO | ❌ Fixed model only | Fee abstraction for stablecoin predictability |
| **DID-Linked Resources** | ✅ Fixed pricing | ✅ USDC/EURe/OSMO | ❌ Fixed model only | Fee abstraction for multi-chain integration |
| **Token Transfers** | ✅ Standard | ✅ Alternative tokens | ✅ Dynamic pricing | Dynamic fees during low congestion |
| **Staking Operations** | ✅ Standard | ✅ Alternative tokens | ✅ Dynamic pricing | Fee abstraction for predictable costs |
| **Governance Voting** | ✅ Standard | ✅ Alternative tokens | ✅ Dynamic pricing | Dynamic fees for cost efficiency |

### Getting Started by Use Case

#### For Identity Applications
1. Start with [DID Module](did-module/) for identity creation
2. Add [Resource Module](resource-module/) for credential schemas
3. Integrate [Fee Abstraction](fee-abstraction/) for stablecoin payments

#### For DeFi Applications  
1. Use [Feemarket Module](feemarket-module/) for optimal transaction costs
2. Add [Fee Abstraction](fee-abstraction/) for multi-token support
3. Implement dynamic fee strategies based on market conditions

#### For Enterprise Solutions
1. Implement [Fee Abstraction](fee-abstraction/) for predictable costs
2. Use [DID Module](did-module/) for organizational identities
3. Deploy [Resource Module](resource-module/) for document management