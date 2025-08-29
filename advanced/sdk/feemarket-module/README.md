# Feemarket Module Overview

The Feemarket module provides dynamic fee calculation functionality for Cosmos SDK transactions on the cheqd network. This module automatically adjusts gas prices based on network congestion and demand, ensuring optimal transaction pricing and network efficiency.

## Summary

The feemarket module implements a dynamic pricing mechanism that adjusts transaction fees in real-time based on network conditions. Unlike fixed fee structures, this system responds to network demand to maintain optimal throughput while providing fair pricing for users.

### Key Features

- **Dynamic Gas Pricing**: Real-time gas price adjustments based on network congestion
- **Market-Based Fees**: Fees increase during high demand and decrease during low demand
- **Automatic Optimization**: Network automatically balances throughput and pricing
- **API Integration**: RESTful endpoints for fetching current gas prices
- **SDK Integration**: Seamless integration with cheqd SDK for automated fee calculation
- **Transaction Prioritization**: Higher fees enable faster transaction processing

### Supported Transaction Types

The Feemarket module affects standard Cosmos SDK transactions:

1. **Bank Transactions**: Token transfers, multi-send operations
2. **Staking Operations**: Delegation, undelegation, redelegation
3. **Distribution**: Reward claiming, commission withdrawal
4. **Governance**: Proposal voting, proposal submission
5. **IBC Operations**: Cross-chain transfers and acknowledgments

> **Note**: Identity transactions (DIDs and DID-Linked Resources) use fixed pricing and are **not affected** by dynamic fees.

## Quick Start Guides

<!-- markdownlint-disable MD033 -->
<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Dynamic Fees</strong></mark></td><td>Complete guide to implementing dynamic fee calculation with real-time gas pricing, API integration, and optimization strategies for standard Cosmos SDK transactions.</td><td><a href="dynamic-fees.md">dynamic-fees.md</a></td></tr></tbody></table>
<!-- markdownlint-enable MD033 -->

## Feemarket Operations Reference

### Core Operations

| Operation | Method | Description | Required Parameters | Optional Parameters |
|-----------|--------|-------------|-------------------|-------------------|
| **Get Gas Price** | API endpoint | Fetch current gas price | `denom` | None |
| **Query Params** | `feemarket.params()` | Get feemarket parameters | None | None |
| **Calculate Fee** | SDK gas estimation | Calculate dynamic fee for transaction | `messages`, `gasPrice` | `gasAdjustment` |

### Gas Price API Endpoints

| Network | Endpoint | Description | Response Format |
|---------|----------|-------------|-----------------|
| **Mainnet** | `https://api.cheqd.net/feemarket/v1/gas_price/ncheq` | Current mainnet gas price | JSON with price and denom |
| **Testnet** | `https://api.cheqd.network/feemarket/v1/gas_price/ncheq` | Current testnet gas price | JSON with price and denom |

## Dynamic Pricing Mechanics

### Price Adjustment Algorithm

| Network Condition | Gas Price Response | Adjustment Factor | User Impact |
|-------------------|-------------------|-------------------|-------------|
| **Low Congestion** | Decreased pricing | 0.1x - 0.8x base price | Lower transaction costs |
| **Normal Activity** | Base pricing | 1.0x base price | Standard transaction costs |
| **High Congestion** | Increased pricing | 1.2x - 5.0x base price | Higher costs, faster processing |
| **Network Stress** | Maximum pricing | Up to 10x base price | Premium for urgent transactions |

### Price Update Frequency

| Update Trigger | Frequency | Mechanism | Impact |
|---------------|-----------|-----------|---------|
| **Block Production** | Every block (~6 seconds) | Automatic algorithm | Real-time price updates |
| **Congestion Changes** | Immediate | Network monitoring | Responsive to demand |
| **Parameter Updates** | Governance proposals | Community voting | Long-term adjustments |

## Fee Calculation Methods

### SDK-Based Calculation

| Method | Description | Use Case | Accuracy |
|--------|-------------|----------|----------|
| **Automatic Gas Estimation** | SDK calculates gas and applies current price | Standard transactions | High |
| **Manual Gas Setting** | User specifies gas, SDK applies current price | Custom requirements | Medium |
| **Fixed Fee Override** | User provides complete fee object | Legacy compatibility | Variable |

### Gas Estimation Process

```typescript
// 1. Simulate transaction to estimate gas usage
const gasEstimate = await sdk.signer.estimateGas(signerAddress, messages);

// 2. Fetch current gas price
const gasPrice = await fetchCurrentGasPrice();

// 3. Calculate total fee
const fee = {
  amount: [coin(Math.ceil(gasEstimate * gasPrice).toString(), 'ncheq')],
  gas: gasEstimate.toString()
};
```

## Network Integration

### API Response Format

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `price` | string | Current gas price in smallest unit | `"25000000000"` |
| `denom` | string | Token denomination | `"ncheq"` |
| `formatted_price` | string | Human-readable price | `"25.0 CHEQ"` |
| `last_updated` | string | Timestamp of last update | `"2025-08-28T10:30:00Z"` |

### Error Handling

| Error Type | HTTP Status | Cause | Resolution |
|------------|-------------|-------|------------|
| **Network Unavailable** | 503 | API endpoint down | Retry with exponential backoff |
| **Invalid Denomination** | 400 | Wrong denom parameter | Use correct denomination (ncheq) |
| **Rate Limit** | 429 | Too many requests | Implement request throttling |
| **Timeout** | 504 | Network congestion | Increase timeout, retry |

## Transaction Types and Fee Impact

### Cosmos SDK Transactions

| Transaction Type | Base Gas | Dynamic Multiplier | Fee Range (CHEQ) | Priority Impact |
|-----------------|----------|-------------------|------------------|------------------|
| **Bank Transfer** | 100,000 | 0.1x - 5x | 0.01 - 0.5 | Medium |
| **Stake Delegation** | 150,000 | 0.1x - 5x | 0.015 - 0.75 | High |
| **Claim Rewards** | 80,000 | 0.1x - 5x | 0.008 - 0.4 | Low |
| **Governance Vote** | 120,000 | 0.1x - 5x | 0.012 - 0.6 | Medium |
| **IBC Transfer** | 200,000 | 0.1x - 5x | 0.02 - 1.0 | High |

### Identity Transactions (Fixed Pricing)

| Transaction Type | Fixed Fee (CHEQ) | Dynamic Fees | Fee Abstraction |
|-----------------|------------------|---------------|-----------------|
| **DID Creation** | 50 CHEQ | ❌ Not affected | ✅ Supported |
| **DID Update** | 25 CHEQ | ❌ Not affected | ✅ Supported |
| **DID Deactivation** | 10 CHEQ | ❌ Not affected | ✅ Supported |
| **Resource Creation - Image** | 10 CHEQ | ❌ Not affected | ✅ Supported |
| **Resource Creation - Default** | 5 CHEQ | ❌ Not affected | ✅ Supported |
| **Resource Creation - JSON** | 2.5 CHEQ | ❌ Not affected | ✅ Supported |

## Optimization Strategies

### Fee Management Best Practices

| Strategy | Description | Implementation | Benefit |
|----------|-------------|----------------|---------|
| **Price Monitoring** | Track gas price trends | Periodic API calls | Cost optimization |
| **Timing Optimization** | Execute during low congestion | Off-peak scheduling | Reduced fees |
| **Batch Transactions** | Group multiple operations | Transaction batching | Efficiency gains |
| **Priority Setting** | Use appropriate gas prices | Dynamic fee calculation | Optimal processing |

### Cost Optimization Techniques

| Technique | Method | Savings Potential | Complexity |
|-----------|--------|-------------------|------------|
| **Off-Peak Execution** | Schedule during low activity | 50-80% | Low |
| **Gas Limit Optimization** | Accurate gas estimation | 10-20% | Medium |
| **Transaction Batching** | Combine operations | 30-60% | High |
| **Price Threshold** | Wait for favorable prices | 40-70% | Medium |

## Advanced Features

### Fee Market Parameters

| Parameter | Description | Default Value | Governance |
|-----------|-------------|---------------|------------|
| `alpha` | Price adjustment factor | `0.5` | ✅ Modifiable |
| `beta` | Congestion sensitivity | `1.0` | ✅ Modifiable |
| `theta` | Price decay rate | `0.1` | ✅ Modifiable |
| `delta` | Maximum price change | `0.5` | ✅ Modifiable |
| `min_gas_price` | Minimum gas price floor | `25ncheq` | ✅ Modifiable |

### Network Monitoring

| Metric | Description | Monitoring Method | Use Case |
|--------|-------------|-------------------|----------|
| **Gas Price History** | Historical pricing data | API queries | Trend analysis |
| **Network Congestion** | Transaction backlog | Block analysis | Timing decisions |
| **Fee Efficiency** | Cost per transaction | Transaction tracking | Optimization |
| **Priority Success** | High-fee transaction speed | Performance metrics | Strategy validation |

## Integration Patterns

### Common Integration Workflows

| Workflow | Description | Implementation | Best Practice |
|----------|-------------|----------------|---------------|
| **Real-time Pricing** | Fetch prices before each transaction | API integration | Cache prices for short periods |
| **Adaptive Batching** | Batch when prices are high | Smart scheduling | Monitor price thresholds |
| **Fallback Strategy** | Use fixed fees if dynamic fails | Error handling | Maintain service availability |
| **User Choice** | Let users select fee levels | UI integration | Provide speed vs cost options |

### SDK Integration Examples

```typescript
// Initialize SDK with feemarket module
const sdk = await createCheqdSDK({
  modules: [
    FeemarketModule, // Enable dynamic fees
    // ... other modules
  ],
  network: CheqdNetwork.Mainnet,
  wallet: wallet
});

// Fetch current gas price
const gasPrice = await fetch('https://api.cheqd.net/feemarket/v1/gas_price/ncheq')
  .then(res => res.json());

// Use dynamic fee calculation
const fee = await sdk.signer.calculateFee(messages, gasPrice.price);
```

## Migration and Compatibility

### Version Compatibility

| SDK Version | Feemarket Support | Features Available | Notes |
|-------------|-------------------|-------------------|-------|
| **v4.0+** | Full dynamic fees | All features | Recommended |
| **v3.x** | Basic integration | Limited API access | Partial support |
| **v2.x** | Fixed fees only | No dynamic pricing | Legacy only |

### Migration Guidelines

When upgrading to use dynamic fees:

1. **Update SDK**: Upgrade to v4.0+ for full feemarket support
2. **Add Module**: Include `FeemarketModule` in SDK initialization
3. **Update Fee Logic**: Replace fixed fees with dynamic calculation
4. **Test Thoroughly**: Validate fee calculation across all transaction types
5. **Monitor Performance**: Track fee efficiency and user experience

## Security Considerations

### Fee Security Best Practices

| Security Aspect | Risk | Mitigation | Implementation |
|----------------|------|------------|----------------|
| **Price Manipulation** | Artificial price inflation | Use official API endpoints | Endpoint validation |
| **Overpayment Protection** | Excessive fee amounts | Implement fee caps | Max fee limits |
| **Network Attacks** | DoS via high fees | Rate limiting | Request throttling |
| **API Reliability** | Service unavailability | Fallback mechanisms | Error handling |

### Monitoring and Alerts

| Alert Type | Trigger | Action | Prevention |
|------------|---------|--------|-----------|
| **High Gas Prices** | >5x normal rates | User notification | Price threshold alerts |
| **API Failures** | Endpoint unavailable | Fallback to fixed fees | Service monitoring |
| **Transaction Failures** | Insufficient fees | Retry with higher fees | Fee adjustment logic |
| **Network Congestion** | High transaction volume | Delay non-urgent operations | Smart scheduling |

## Troubleshooting

### Common Issues

| Issue | Symptoms | Cause | Solution |
|-------|----------|-------|---------|
| **Transaction Rejected** | "Insufficient fee" error | Gas price too low | Increase gas price or retry |
| **Slow Processing** | Transaction pending | Network congestion | Wait or increase fee |
| **Fee Calculation Error** | Invalid fee amounts | API or calculation error | Fallback to fixed fees |
| **API Timeout** | No price response | Network issues | Implement retry logic |

### Debugging Tools

| Tool | Purpose | Usage | Example |
|------|---------|-------|---------|
| **Gas Price API** | Check current prices | Direct HTTP requests | Monitor price trends |
| **Transaction Simulation** | Estimate gas usage | SDK simulation | Predict transaction costs |
| **Block Explorer** | Monitor transaction status | Web interface | Verify processing times |
| **Network Status** | Check network health | Status endpoints | Identify congestion |

## Related Documentation

### SDK Setup and Configuration

- [SDK Setup Guide](../sdk-setup.md) - Initial SDK configuration and module setup
- [Fee Abstraction Guide](../fee-abstraction/README.md) - Multi-token fee payment options

### Transaction Modules

- [DID Module Overview](../did-module/README.md) - DID operations (fixed pricing)
- [Resource Module Overview](../resource-module/README.md) - DLR operations (fixed pricing)

### Network and Tools

- [Network Overview](../../../network/README.md) - cheqd network information and status
- [Block Explorer](../../../network/tools/block-explorer.md) - Transaction monitoring and analysis
