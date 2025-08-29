# Dynamic Fees with Feemarket Module

This guide explains how to use the feemarket module to implement dynamic fee calculation for standard Cosmos SDK transactions on the cheqd network. The feemarket module provides real-time gas pricing based on network congestion and demand.

> **Note**: Identity transactions (DIDs and DID-Linked Resources) have their own fixed pricing model and are not affected by the feemarket module's dynamic pricing.

## Understanding Dynamic Fees

Dynamic fees adjust transaction costs based on network conditions:

- **High Network Activity**: Gas prices increase to prioritize transactions
- **Low Network Activity**: Gas prices decrease to reduce transaction costs
- **Real-time Pricing**: Prices update continuously based on network demand

## Gas Price Endpoints

You can fetch current gas prices directly from the cheqd network APIs:

### Mainnet

```http
GET https://api.cheqd.net/feemarket/v1/gas_price/ncheq
```

### Testnet

```http
GET https://api.cheqd.network/feemarket/v1/gas_price/ncheq
```

### API Response Format

The API returns a decimal value that needs to be converted:

```json
{
  "price": "0.500000000000000000"
}
```

**Interpretation**: `0.500000000000000000 × 10^4 = 5000ncheq`

The price represents the cost per unit of gas in `ncheq` (nano CHEQ tokens).

## SDK Implementation

### Prerequisites

Ensure your SDK includes the feemarket module:

```typescript
import { 
  createCheqdSDK, 
  FeemarketModule,
  CheqdNetwork,
  AbstractCheqdSDKModule,
  type ICheqdSDKOptions 
} from '@cheqd/sdk';
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';

async function initializeSDKWithFeemarket() {
  const wallet = await DirectSecp256k1HdWallet.fromMnemonic(
    'your twelve word mnemonic phrase goes here like this example', 
    { prefix: 'cheqd' }
  );

  const options: ICheqdSDKOptions = {
    modules: [
      FeemarketModule as unknown as AbstractCheqdSDKModule
    ],
    rpcUrl: 'https://rpc.cheqd.network',
    network: CheqdNetwork.Testnet,
    wallet: wallet
  };

  const sdk = await createCheqdSDK(options);
  
  return { sdk, wallet };
}
```

### Fetching Dynamic Gas Prices

```typescript
import { FeemarketModule } from '@cheqd/sdk';
import { coin } from '@cosmjs/stargate';

async function getDynamicGasPrice(denom: string = 'ncheq') {
  const { sdk } = await initializeSDKWithFeemarket();
  const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
  
  try {
    // Fetch current dynamic gas price for the denomination
    const gasPrice = await feemarketModule.generateSafeGasPriceByDenom(denom);
    
    console.log(`💰 Current gas price for ${denom}:`);
    console.log(`   Amount: ${gasPrice.amount}`);
    console.log(`   Denom: ${gasPrice.denom}`);
    
    return gasPrice;
  } catch (error) {
    console.error('Error fetching dynamic gas price:', error);
    
    // Fallback to network API if SDK query fails
    return await fetchGasPriceFromAPI();
  }
}

// Alternative: Direct API fetch
async function fetchGasPriceFromAPI(): Promise<{ amount: string; denom: string }> {
  try {
    const response = await fetch('https://api.cheqd.network/feemarket/v1/gas_price/ncheq');
    const data = await response.json();
    
    // Convert decimal to integer format
    // Example: "0.500000000000000000" * 10^4 = 5000
    const priceDecimal = parseFloat(data.price);
    const priceInteger = Math.ceil(priceDecimal * 10000).toString();
    
    console.log(`🌐 Fetched from API: ${data.price} → ${priceInteger}ncheq`);
    
    return {
      amount: priceInteger,
      denom: 'ncheq'
    };
  } catch (error) {
    console.error('Error fetching from API:', error);
    
    // Final fallback to default
    return {
      amount: '5000',
      denom: 'ncheq'
    };
  }
}
```

## Standard Cosmos SDK Transactions with Dynamic Fees

### Token Transfer

```typescript
import { MsgSend } from 'cosmjs-types/cosmos/bank/v1beta1/tx';

async function transferWithDynamicFees() {
  const { sdk, wallet } = await initializeSDKWithFeemarket();
  const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
  
  const sender = (await wallet.getAccounts())[0].address;
  const recipient = 'cheqd1recipient...';
  
  // Create transfer message
  const transferMsg = {
    typeUrl: '/cosmos.bank.v1beta1.MsgSend',
    value: MsgSend.fromPartial({
      fromAddress: sender,
      toAddress: recipient,
      amount: [coin('1000000', 'ncheq')] // 1 CHEQ
    })
  };

  try {
    // Get dynamic gas price
    const gasPrice = await feemarketModule.generateSafeGasPriceByDenom('ncheq');
    
    // Calculate fee based on dynamic pricing
    const estimatedGas = '200000'; // Estimated gas for transfer
    const fee = FeemarketModule.generateFeesFromGasPrice(gasPrice, sender, estimatedGas);
    
    console.log('💸 Dynamic fee calculation:');
    console.log(`   Gas Price: ${gasPrice.amount}${gasPrice.denom}`);
    console.log(`   Estimated Gas: ${estimatedGas}`);
    console.log(`   Total Fee: ${fee.amount[0].amount}${fee.amount[0].denom}`);

    const result = await sdk.signer.signAndBroadcast(
      sender,
      [transferMsg],
      fee,
      'Transfer with dynamic fee'
    );

    if (result.code === 0) {
      console.log('✅ Transfer successful with dynamic fee!');
      console.log(`   Transaction hash: ${result.transactionHash}`);
      console.log(`   Gas used: ${result.gasUsed}`);
      console.log(`   Gas wanted: ${result.gasWanted}`);
      
      // Calculate actual fee cost
      const actualFeeAmount = parseInt(fee.amount[0].amount) / 1000000000; // Convert ncheq to CHEQ
      console.log(`   Fee paid: ${actualFeeAmount} CHEQ`);
    } else {
      console.error(`❌ Transfer failed: ${result.rawLog}`);
    }
  } catch (error) {
    console.error('Error during transfer:', error);
  }
}
```

### Staking Delegation

```typescript
import { MsgDelegate } from 'cosmjs-types/cosmos/staking/v1beta1/tx';

async function delegateWithDynamicFees() {
  const { sdk, wallet } = await initializeSDKWithFeemarket();
  const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
  
  const delegator = (await wallet.getAccounts())[0].address;
  const validator = 'cheqdvaloper1validator...';
  
  // Create delegation message
  const delegateMsg = {
    typeUrl: '/cosmos.staking.v1beta1.MsgDelegate',
    value: MsgDelegate.fromPartial({
      delegatorAddress: delegator,
      validatorAddress: validator,
      amount: coin('10000000', 'ncheq') // 10 CHEQ
    })
  };

  try {
    // Get dynamic gas price
    const gasPrice = await feemarketModule.generateSafeGasPriceByDenom('ncheq');
    
    // Higher gas estimate for staking operations
    const estimatedGas = '300000';
    const fee = FeemarketModule.generateFeesFromGasPrice(gasPrice, delegator, estimatedGas);
    
    console.log('🥩 Delegation with dynamic fee:');
    console.log(`   Delegating: 10 CHEQ to ${validator}`);
    console.log(`   Dynamic fee: ${parseInt(fee.amount[0].amount) / 1000000000} CHEQ`);

    const result = await sdk.signer.signAndBroadcast(
      delegator,
      [delegateMsg],
      fee,
      'Delegation with dynamic fee'
    );

    if (result.code === 0) {
      console.log('✅ Delegation successful!');
      console.log(`   Transaction hash: ${result.transactionHash}`);
      console.log(`   Gas efficiency: ${(parseInt(result.gasUsed!) / parseInt(estimatedGas) * 100).toFixed(1)}%`);
    } else {
      console.error(`❌ Delegation failed: ${result.rawLog}`);
    }
  } catch (error) {
    console.error('Error during delegation:', error);
  }
}
```

### Claiming Staking Rewards

```typescript
import { MsgWithdrawDelegatorReward } from 'cosmjs-types/cosmos/distribution/v1beta1/tx';

async function claimRewardsWithDynamicFees() {
  const { sdk, wallet } = await initializeSDKWithFeemarket();
  const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
  
  const delegator = (await wallet.getAccounts())[0].address;
  const validators = [
    'cheqdvaloper1validator1...',
    'cheqdvaloper1validator2...',
    'cheqdvaloper1validator3...'
  ];
  
  // Create reward claim messages for multiple validators
  const claimMessages = validators.map(validator => ({
    typeUrl: '/cosmos.distribution.v1beta1.MsgWithdrawDelegatorReward',
    value: MsgWithdrawDelegatorReward.fromPartial({
      delegatorAddress: delegator,
      validatorAddress: validator
    })
  }));

  try {
    // Get dynamic gas price
    const gasPrice = await feemarketModule.generateSafeGasPriceByDenom('ncheq');
    
    // Gas scales with number of validators
    const gasPerValidator = 100000;
    const estimatedGas = (gasPerValidator * validators.length).toString();
    const fee = FeemarketModule.generateFeesFromGasPrice(gasPrice, delegator, estimatedGas);
    
    console.log('🎁 Claiming rewards with dynamic fee:');
    console.log(`   Validators: ${validators.length}`);
    console.log(`   Estimated Gas: ${estimatedGas}`);
    console.log(`   Dynamic Fee: ${parseInt(fee.amount[0].amount) / 1000000000} CHEQ`);

    const result = await sdk.signer.signAndBroadcast(
      delegator,
      claimMessages,
      fee,
      'Claim rewards with dynamic fee'
    );

    if (result.code === 0) {
      console.log('✅ Rewards claimed successfully!');
      console.log(`   Transaction hash: ${result.transactionHash}`);
      console.log(`   Claimed from ${validators.length} validators`);
      
      // Show fee efficiency
      const gasEfficiency = (parseInt(result.gasUsed!) / parseInt(estimatedGas) * 100).toFixed(1);
      console.log(`   Gas efficiency: ${gasEfficiency}%`);
    } else {
      console.error(`❌ Claim rewards failed: ${result.rawLog}`);
    }
  } catch (error) {
    console.error('Error during reward claim:', error);
  }
}
```

### Governance Voting

```typescript
import { MsgVote, VoteOption } from 'cosmjs-types/cosmos/gov/v1beta1/tx';

async function voteWithDynamicFees() {
  const { sdk, wallet } = await initializeSDKWithFeemarket();
  const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
  
  const voter = (await wallet.getAccounts())[0].address;
  const proposalId = 1; // Replace with actual proposal ID
  
  // Create vote message
  const voteMsg = {
    typeUrl: '/cosmos.gov.v1beta1.MsgVote',
    value: MsgVote.fromPartial({
      proposalId: BigInt(proposalId),
      voter: voter,
      option: VoteOption.VOTE_OPTION_YES
    })
  };

  try {
    // Get dynamic gas price
    const gasPrice = await feemarketModule.generateSafeGasPriceByDenom('ncheq');
    
    // Voting typically uses minimal gas
    const estimatedGas = '150000';
    const fee = FeemarketModule.generateFeesFromGasPrice(gasPrice, voter, estimatedGas);
    
    console.log('🗳️  Voting with dynamic fee:');
    console.log(`   Proposal ID: ${proposalId}`);
    console.log(`   Vote: YES`);
    console.log(`   Dynamic Fee: ${parseInt(fee.amount[0].amount) / 1000000000} CHEQ`);

    const result = await sdk.signer.signAndBroadcast(
      voter,
      [voteMsg],
      fee,
      'Governance vote with dynamic fee'
    );

    if (result.code === 0) {
      console.log('✅ Vote submitted successfully!');
      console.log(`   Transaction hash: ${result.transactionHash}`);
    } else {
      console.error(`❌ Vote failed: ${result.rawLog}`);
    }
  } catch (error) {
    console.error('Error during vote:', error);
  }
}
```

## Fee Optimization Strategies

### Gas Estimation

```typescript
async function estimateOptimalGas(messages: any[], signer: string) {
  const { sdk } = await initializeSDKWithFeemarket();
  
  try {
    // Simulate transaction to get accurate gas estimate
    const simResult = await sdk.signer.simulate(signer, messages, '');
    
    // Add safety margin (20% buffer)
    const safeGasLimit = Math.ceil(simResult.gasUsed * 1.2);
    
    console.log('⛽ Gas estimation:');
    console.log(`   Simulated: ${simResult.gasUsed}`);
    console.log(`   Safe limit: ${safeGasLimit}`);
    
    return safeGasLimit.toString();
  } catch (error) {
    console.error('Gas simulation failed:', error);
    
    // Fallback to conservative estimate
    return '300000';
  }
}
```

### Fee Comparison

```typescript
async function compareFeeOptions() {
  const { sdk } = await initializeSDKWithFeemarket();
  const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
  const sender = 'cheqd1sender...';
  const gasAmount = '200000';
  
  try {
    // Get dynamic price
    const dynamicPrice = await feemarketModule.generateSafeGasPriceByDenom('ncheq');
    const dynamicFee = FeemarketModule.generateFeesFromGasPrice(dynamicPrice, sender, gasAmount);
    
    // Static price comparison
    const staticPrice = { amount: '5000', denom: 'ncheq' };
    const staticFee = FeemarketModule.generateFeesFromGasPrice(staticPrice, sender, gasAmount);
    
    console.log('💰 Fee comparison:');
    console.log(`   Dynamic: ${parseInt(dynamicFee.amount[0].amount) / 1000000000} CHEQ`);
    console.log(`   Static:  ${parseInt(staticFee.amount[0].amount) / 1000000000} CHEQ`);
    
    const savings = parseInt(staticFee.amount[0].amount) - parseInt(dynamicFee.amount[0].amount);
    if (savings > 0) {
      console.log(`   💸 Save: ${savings / 1000000000} CHEQ with dynamic pricing`);
    } else {
      console.log(`   ⚠️  Dynamic fee is ${Math.abs(savings) / 1000000000} CHEQ higher`);
    }
    
    return { dynamicFee, staticFee, savings };
  } catch (error) {
    console.error('Error comparing fees:', error);
    return null;
  }
}
```

## Advanced Usage

### Batch Transaction Optimization

```typescript
async function optimizedBatchTransactions() {
  const { sdk, wallet } = await initializeSDKWithFeemarket();
  const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
  const sender = (await wallet.getAccounts())[0].address;
  
  // Multiple operations in one transaction
  const messages = [
    // Transfer tokens
    {
      typeUrl: '/cosmos.bank.v1beta1.MsgSend',
      value: MsgSend.fromPartial({
        fromAddress: sender,
        toAddress: 'cheqd1recipient1...',
        amount: [coin('500000', 'ncheq')]
      })
    },
    // Delegate tokens
    {
      typeUrl: '/cosmos.staking.v1beta1.MsgDelegate',
      value: MsgDelegate.fromPartial({
        delegatorAddress: sender,
        validatorAddress: 'cheqdvaloper1validator...',
        amount: coin('1000000', 'ncheq')
      })
    }
  ];

  try {
    // Estimate gas for batch
    const estimatedGas = await estimateOptimalGas(messages, sender);
    
    // Get dynamic pricing
    const gasPrice = await feemarketModule.generateSafeGasPriceByDenom('ncheq');
    const fee = FeemarketModule.generateFeesFromGasPrice(gasPrice, sender, estimatedGas);
    
    console.log('📦 Batch transaction:');
    console.log(`   Operations: ${messages.length}`);
    console.log(`   Total Gas: ${estimatedGas}`);
    console.log(`   Dynamic Fee: ${parseInt(fee.amount[0].amount) / 1000000000} CHEQ`);

    const result = await sdk.signer.signAndBroadcast(
      sender,
      messages,
      fee,
      'Optimized batch transaction'
    );

    if (result.code === 0) {
      console.log('✅ Batch transaction successful!');
      console.log(`   Saved gas by batching operations`);
    } else {
      console.error(`❌ Batch transaction failed: ${result.rawLog}`);
    }
  } catch (error) {
    console.error('Error in batch transaction:', error);
  }
}
```

### Fee Market Monitoring

```typescript
async function monitorFeeMarket() {
  const { sdk } = await initializeSDKWithFeemarket();
  const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
  
  console.log('📊 Monitoring fee market...');
  
  const monitorInterval = setInterval(async () => {
    try {
      const gasPrice = await feemarketModule.generateSafeGasPriceByDenom('ncheq');
      const timestamp = new Date().toISOString();
      
      console.log(`[${timestamp}] Gas Price: ${gasPrice.amount}${gasPrice.denom}`);
      
      // Alert if price is unusually high
      if (parseInt(gasPrice.amount) > 10000) {
        console.warn('⚠️  High network congestion detected!');
        console.log('💡 Consider waiting for lower fees or using fee abstraction');
      }
    } catch (error) {
      console.error('Error monitoring fees:', error);
    }
  }, 30000); // Check every 30 seconds
  
  // Stop monitoring after 5 minutes
  setTimeout(() => {
    clearInterval(monitorInterval);
    console.log('📊 Fee monitoring stopped');
  }, 300000);
}
```

## Best Practices

### 1. Gas Estimation

- Always simulate transactions before broadcasting
- Add 10-20% buffer to gas estimates
- Use batch transactions to optimize gas usage

### 2. Fee Management

- Monitor network congestion regularly
- Consider fee abstraction for predictable costs
- Use dynamic pricing during low-congestion periods

### 3. Error Handling

```typescript
async function robustTransactionWithDynamicFees(messages: any[], sender: string) {
  const { sdk } = await initializeSDKWithFeemarket();
  const feemarketModule = new FeemarketModule(sdk.signer, sdk.querier);
  
  let retries = 3;
  
  while (retries > 0) {
    try {
      // Get fresh gas price for each attempt
      const gasPrice = await feemarketModule.generateSafeGasPriceByDenom('ncheq');
      const estimatedGas = await estimateOptimalGas(messages, sender);
      const fee = FeemarketModule.generateFeesFromGasPrice(gasPrice, sender, estimatedGas);
      
      const result = await sdk.signer.signAndBroadcast(
        sender,
        messages,
        fee,
        'Transaction with retry logic'
      );
      
      if (result.code === 0) {
        console.log('✅ Transaction successful!');
        return result;
      } else {
        throw new Error(`Transaction failed: ${result.rawLog}`);
      }
    } catch (error) {
      retries--;
      console.warn(`⚠️  Attempt failed, ${retries} retries left:`, error);
      
      if (retries > 0) {
        // Wait before retry with exponential backoff
        await new Promise(resolve => setTimeout(resolve, (4 - retries) * 1000));
      } else {
        throw error;
      }
    }
  }
}
```

### 4. Performance Tips

- Cache gas prices for short periods (30-60 seconds)
- Use WebSocket connections for real-time price updates
- Implement circuit breakers for high-fee scenarios

## Troubleshooting

### Common Issues

1. **High Gas Fees**
   - Check network congestion
   - Consider using fee abstraction
   - Wait for off-peak hours

2. **Transaction Failures**
   - Increase gas limit
   - Verify account balance
   - Check message formatting

3. **API Failures**
   - Implement fallback pricing
   - Use multiple RPC endpoints
   - Handle network timeouts

### Error Codes

```typescript
async function handleTransactionErrors(result: any) {
  switch (result.code) {
    case 5:
      console.error('❌ Insufficient funds for gas');
      console.log('💡 Add more tokens to your account');
      break;
    case 11:
      console.error('❌ Out of gas');
      console.log('💡 Increase gas limit or optimize transaction');
      break;
    case 32:
      console.error('❌ Account not found');
      console.log('💡 Verify account address and funding');
      break;
    default:
      console.error(`❌ Transaction failed with code ${result.code}`);
      console.log(`Raw log: ${result.rawLog}`);
  }
}
```

## Next Steps

- [Implement fee abstraction for multi-token support](../fee-abstraction/using-in-transactions.md)
- [Learn about identity transaction pricing](../did-module/pricing.md)
- [Explore advanced SDK features](../sdk-setup.md)

## Getting Help

If you encounter issues with dynamic fees:

- Check the [cheqd SDK GitHub repository](https://github.com/cheqd/sdk) for updates
- Review network status at [status.cheqd.io](https://status.cheqd.io)
- Join the [cheqd Community Discord](https://discord.gg/cheqd) for support
- Consult the [Cosmos SDK documentation](https://docs.cosmos.network/) for standard transaction types
