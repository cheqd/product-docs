# 💰 Fee Abstraction Module - Pay in alternative denominations

The Fee Abstraction module provides comprehensive functionality for paying transaction fees in alternative tokens instead of native CHEQ tokens. This module enables cross-chain fee payment using IBC tokens from other ecosystems, making the cheqd network more accessible to users from different blockchain networks.

## Summary

Fee abstraction is a powerful feature that allows users to pay transaction fees in alternative tokens (such as IBC tokens from other chains) instead of the native CHEQ token. This functionality makes the cheqd network more user-friendly and accessible to users from different blockchain ecosystems who may not hold CHEQ tokens.

### Key Features

* **Multi-Token Fee Payment**: Pay fees using USDC, EURe, OSMO, and other IBC tokens
* **Cross-Chain Compatibility**: Support for tokens from multiple blockchain ecosystems
* **Seamless Integration**: Works with all transaction types (Cosmos SDK and identity transactions)
* **Host Zone Management**: Query and manage supported token configurations
* **Automatic Conversion**: Network handles token conversion transparently
* **Cost Optimization**: Choose the most cost-effective fee token for transactions

### Supported Transaction Types

The Fee Abstraction module works with all cheqd transaction types:

1. **Cosmos SDK Transactions**: Bank transfers, staking, governance, rewards claiming
2. **Identity Transactions**: DID creation, updates, deactivation, and DID-Linked Resource operations

## Quick Start Guides

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Using in Transactions</strong></mark></td><td>Comprehensive guide to implementing fee abstraction in both Cosmos SDK and identity transactions with practical examples for USDC, EURe, and OSMO fee payments.</td><td><a href="using-in-transactions.md">using-in-transactions.md</a></td></tr></tbody></table>

## Fee Abstraction Operations Reference

### Core Operations

| Operation            | Method                                   | Description                                 | Required Parameters         | Optional Parameters |
| -------------------- | ---------------------------------------- | ------------------------------------------- | --------------------------- | ------------------- |
| **Query Host Zones** | `feeabstraction.hostZoneConfig()`        | Get supported IBC tokens and configurations | None                        | `pagination`        |
| **Query Host Zone**  | `feeabstraction.hostZone()`              | Get specific host zone configuration        | `hostZoneId`                | None                |
| **Fee Payment**      | Transaction methods with `fee` parameter | Pay fees using alternative tokens           | `fee` object with IBC token | `memo`              |

### Transaction Integration

| Transaction Type       | Fee Abstraction Support | Example Tokens   | Implementation         |
| ---------------------- | ----------------------- | ---------------- | ---------------------- |
| **Bank Transfer**      | ✅ Full Support          | USDC, EURe, OSMO | Standard fee parameter |
| **Staking Operations** | ✅ Full Support          | USDC, EURe, OSMO | Standard fee parameter |
| **Governance**         | ✅ Full Support          | USDC, EURe, OSMO | Standard fee parameter |
| **DID Operations**     | ✅ Full Support          | USDC (preferred) | Fixed pricing model    |
| **DLR Operations**     | ✅ Full Support          | USDC (preferred) | Fixed pricing model    |

## Supported Tokens and Networks

### Primary IBC Tokens

| Token               | Symbol | Network Source | IBC Denomination                                                       | Use Cases                                  |
| ------------------- | ------ | -------------- | ---------------------------------------------------------------------- | ------------------------------------------ |
| **USD Coin**        | USDC   | Osmosis        | `ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013` | Identity transactions, stable fee payments |
| **EURe Stablecoin** | EURe   | Osmosis        | `ibc/5CB92A4BEBAAF2B79E52EE0459F2CC06D7DCB96EAB6FB00FE78D5A12D4E0B84E` | European market transactions               |
| **Osmosis**         | OSMO   | Osmosis        | `ibc/D176154B0C63D1F9C6DCFB4F70349EBF2E2B5A87A05902F57A6AE92B863E9AEC` | Cosmos SDK transactions, DeFi operations   |

### Host Zone Configuration

Each supported token has a host zone configuration that includes:

| Configuration Field | Description                         | Example Value       |
| ------------------- | ----------------------------------- | ------------------- |
| `hostZoneId`        | Unique identifier for the host zone | `"osmosis-1"`       |
| `connectionId`      | IBC connection identifier           | `"connection-0"`    |
| `ibcDenom`          | IBC token denomination              | `"ibc/D176154B..."` |
| `nativeDenom`       | Original token denomination         | `"uosmo"`           |
| `minSwapAmount`     | Minimum swap amount                 | `"1000"`            |
| `maxSwapAmount`     | Maximum swap amount                 | `"1000000000"`      |

## Fee Structure and Pricing

### Fee Calculation Methods

| Method                | Description                  | Transaction Types       | Token Support        |
| --------------------- | ---------------------------- | ----------------------- | -------------------- |
| **Dynamic Fees**      | Market-based fee calculation | Cosmos SDK transactions | All supported tokens |
| **Fixed Pricing**     | Predetermined fee amounts    | Identity transactions   | Primarily USDC       |
| **Manual Conversion** | User-calculated fee amounts  | All transactions        | All supported tokens |

### Fee Amount Guidelines

| Transaction Category   | Base Fee (CHEQ) | USDC Equivalent | EURe Equivalent | OSMO Equivalent |
| ---------------------- | --------------- | --------------- | --------------- | --------------- |
| **Bank Transfer**      | 5,000 ncheq     | \~5,000 units   | \~5,000 units   | \~15,000 units  |
| **DID Creation**       | 50,000 ncheq    | \~12,000 units  | \~12,000 units  | \~35,000 units  |
| **DID Update**         | 40,000 ncheq    | \~15,000 units  | \~15,000 units  | \~45,000 units  |
| **Resource Creation**  | 100,000 ncheq   | \~35,000 units  | \~35,000 units  | \~100,000 units |
| **Staking Operations** | 25,000 ncheq    | \~8,000 units   | \~8,000 units   | \~25,000 units  |

> **Note**: Fee amounts require manual conversion from CHEQ equivalent at current exchange rates. An upcoming SDK release will introduce USD fixed pricing and automated fee calculation.

## Integration Patterns

### Common Usage Patterns

| Pattern                   | Description                               | Best Practice         | Implementation              |
| ------------------------- | ----------------------------------------- | --------------------- | --------------------------- |
| **Stablecoin Preference** | Use USDC/EURe for predictable costs       | Identity transactions | Direct IBC token assignment |
| **Multi-Token Strategy**  | Different tokens for different operations | Cost optimization     | Token selection logic       |
| **Fallback Mechanism**    | CHEQ fallback if IBC fails                | High availability     | Error handling with retry   |
| **Cross-Chain Workflows** | IBC token from user's origin chain        | User experience       | Token availability checking |

### SDK Integration Examples

```typescript
// Initialize SDK with fee abstraction
const sdk = await createCheqdSDK({
  modules: [
    DIDModule,
    ResourceModule,
    FeeabstractionModule, // Enable fee abstraction
    FeemarketModule       // Enable dynamic fees
  ],
  network: CheqdNetwork.Testnet,
  wallet: wallet,
  rpcEndpoint: rpcEndpoint,
  queryClient: queryClient
});

// Query supported tokens
const hostZones = await sdk.querier.feeabstraction.hostZoneConfig({});

// Use alternative token for fees
const fee = {
  amount: [coin('12000', 'ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013')],
  gas: '500000'
};
```

## Account Management and Funding

### IBC Token Requirements

For fee abstraction to work, your account must hold the **bridged IBC representation** of tokens on the cheqd network:

| Requirement            | Description                             | How to Obtain                      |
| ---------------------- | --------------------------------------- | ---------------------------------- |
| **IBC Tokens**         | Bridged tokens on cheqd network         | Transfer via IBC from origin chain |
| **Sufficient Balance** | Enough tokens to cover transaction fees | Check balance before transactions  |
| **Valid Denomination** | Correct IBC denomination format         | Use host zone configuration        |

### Funding Workflow

1. **Origin Chain**: Hold native tokens (USDC on Cosmos Hub, OSMO on Osmosis)
2. **IBC Transfer**: Bridge tokens to cheqd network via IBC
3. **Verification**: Confirm tokens appear with IBC denomination
4. **Fee Usage**: Use IBC tokens for transaction fees

### Balance Checking

```typescript
// Check IBC token balance
const balance = await sdk.querier.cosmos.bank.v1beta1.balance({
  address: "cheqd1...",
  denom: "ibc/F663521BF1836B00F5F177680F74BFB9A8B5654A694D0D2BC249E03CF2509013" // IBC USDC
});

console.log(`Available balance: ${balance.balance?.amount} ${balance.balance?.denom}`);
```

## Advanced Configuration

### Host Zone Management

| Configuration Aspect  | Description               | Configuration Method      |
| --------------------- | ------------------------- | ------------------------- |
| **Token Addition**    | Add new IBC token support | Governance proposal       |
| **Rate Updates**      | Modify conversion rates   | Automatic market rates    |
| **Limits Management** | Update swap limits        | Module parameters         |
| **Network Updates**   | Add new source networks   | IBC channel configuration |

### Performance Optimization

| Optimization         | Method                     | Benefit                | Implementation        |
| -------------------- | -------------------------- | ---------------------- | --------------------- |
| **Token Caching**    | Cache host zone configs    | Reduced query overhead | Local storage         |
| **Fee Estimation**   | Pre-calculate fee amounts  | Better UX              | Fee calculation utils |
| **Batch Operations** | Group similar transactions | Cost efficiency        | Transaction batching  |
| **Error Handling**   | Graceful fallback to CHEQ  | High availability      | Try-catch patterns    |

## Security Considerations

### Fee Payment Security

| Security Aspect          | Risk                         | Mitigation                       | Implementation         |
| ------------------------ | ---------------------------- | -------------------------------- | ---------------------- |
| **Token Validation**     | Invalid IBC denominations    | Validate against host zones      | Pre-transaction checks |
| **Balance Verification** | Insufficient funds           | Check balance before transaction | Balance queries        |
| **Rate Manipulation**    | Unfavorable conversion rates | Monitor rate changes             | Rate comparison        |
| **Network Security**     | IBC channel security         | Use established channels         | Channel verification   |

### Best Security Practices

1. **Validate Denominations**: Always verify IBC denominations against host zone configs
2. **Check Balances**: Confirm sufficient token balance before transactions
3. **Monitor Rates**: Be aware of token conversion rates and market conditions
4. **Use Established Channels**: Stick to well-tested IBC channels
5. **Implement Fallbacks**: Have CHEQ token fallback for critical operations

## Error Handling and Troubleshooting

### Common Error Scenarios

| Error Type                 | Cause                       | Symptoms                | Resolution                     |
| -------------------------- | --------------------------- | ----------------------- | ------------------------------ |
| **Insufficient Funds**     | Not enough IBC tokens       | Transaction rejection   | Fund account with IBC tokens   |
| **Invalid Denomination**   | Wrong IBC denom format      | Fee calculation failure | Use correct IBC denomination   |
| **Host Zone Unavailable**  | Network connectivity issues | Query failures          | Check network status, retry    |
| **Rate Calculation Error** | Market volatility           | Unexpected fee amounts  | Recalculate with current rates |
| **IBC Channel Issues**     | Cross-chain problems        | Token transfer failures | Wait for channel recovery      |

### Error Response Handling

```typescript
try {
  const result = await sdk.signer.signAndBroadcast(
    signerAddress,
    [message],
    feeInIbcToken,
    'Transaction with fee abstraction'
  );
} catch (error) {
  if (error.message.includes('insufficient funds')) {
    console.log('Please fund your account with IBC tokens');
    // Implement funding logic or fallback to CHEQ
  } else if (error.message.includes('invalid denom')) {
    console.log('Invalid IBC denomination, checking host zones...');
    // Query and validate denominations
  }
  // Other error handling...
}
```

### Debugging Tools

| Tool                       | Purpose                | Usage              | Example                   |
| -------------------------- | ---------------------- | ------------------ | ------------------------- |
| **Host Zone Queries**      | Check supported tokens | `hostZoneConfig()` | Validate token support    |
| **Balance Queries**        | Verify account funding | `bank.balance()`   | Check available funds     |
| **Transaction Simulation** | Test before broadcast  | `simulate()`       | Predict transaction costs |
| **Network Status**         | Check IBC connectivity | Network monitoring | Verify cross-chain status |

## Migration and Compatibility

### SDK Version Support

| SDK Version | Fee Abstraction Features           | Compatibility Notes         |
| ----------- | ---------------------------------- | --------------------------- |
| **v4.0+**   | Full fee abstraction, dynamic fees | Current recommended version |
| **v3.x**    | Basic fee abstraction              | Limited token support       |
| **v2.x**    | Native fees only                   | No fee abstraction          |

### Migration Guidelines

When upgrading to use fee abstraction:

1. **Update SDK Version**: Upgrade to v4.0+ for full feature support
2. **Add Modules**: Include `FeeabstractionModule` in SDK initialization
3. **Fund Accounts**: Bridge IBC tokens to user accounts
4. **Update Fee Logic**: Replace native fee calculation with IBC tokens
5. **Test Thoroughly**: Validate all transaction types with new fee structure

## Related Documentation

### SDK Setup and Configuration

* [SDK Setup Guide](../sdk-setup.md) - Initial SDK configuration and module setup
* [Dynamic Fees Guide](../feemarket-module/dynamic-fees.md) - Market-based fee calculation

### Transaction Modules

* [DID Module Overview](../did-module/) - DID operations with fee abstraction
* [Resource Module Overview](../resource-module/) - DLR operations with fee abstraction

### Network and Infrastructure

* [Network Overview](../../../network/cheqd/) - cheqd network information and endpoints
* [Block Explorer](../../../network/tools/block-explorer.md) - Transaction monitoring and verification
