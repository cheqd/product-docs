# Gas Simulation and Transaction Batching Optimization

This guide demonstrates advanced techniques for utilizing gas simulation to achieve better fee estimations and guarantees, as well as implementing efficient transaction batching patterns using the cheqd SDK. These techniques are based on the SDK's internal implementation and provide production-ready optimization strategies.

## Table of Contents

- [Gas Simulation Overview](#gas-simulation-overview)
- [Advanced Gas Estimation](#advanced-gas-estimation)
- [Transaction Batching Strategies](#transaction-batching-strategies)
- [Fee Optimization Patterns](#fee-optimization-patterns)
- [Production Implementation](#production-implementation)
- [Error Handling and Resilience](#error-handling-and-resilience)
- [Built-in Transaction Retry Policy](#built-in-transaction-retry-policy)

## Gas Simulation Overview

Gas simulation allows you to accurately estimate transaction costs before execution, providing several key benefits:

### Core Benefits

| Benefit | Description | Use Case |
|---------|-------------|----------|
| **Cost Prediction** | Accurate fee estimation before transaction execution | User experience, cost planning |
| **Failure Prevention** | Early detection of insufficient gas scenarios | Transaction reliability |
| **Optimal Gas Setting** | Avoid over/under-provisioning gas | Cost optimization |
| **Batch Optimization** | Efficient grouping of multiple transactions | Throughput maximization |

### Simulation vs Real Execution

| Aspect | Simulation | Real Execution |
|--------|------------|----------------|
| **Network State** | Current state snapshot | State at execution time |
| **Gas Consumption** | Estimated usage | Actual usage |
| **Side Effects** | None (read-only) | Permanent state changes |
| **Cost** | Free | Consumes actual fees |

## Advanced Gas Estimation

### Basic Gas Simulation

The SDK provides a `simulate()` method for accurate gas estimation:

```typescript
import { CheqdSigningStargateClient } from '@cheqd/sdk';

// Initialize signer with endpoint for simulation
const signer = await CheqdSigningStargateClient.connectWithSigner(
  rpcEndpoint,
  wallet,
  { 
    registry: createDefaultCheqdRegistry(),
    endpoint: rpcEndpoint // Required for simulation
  }
);

// Simulate single transaction
async function simulateTransaction(
  signerAddress: string,
  messages: EncodeObject[],
  memo?: string
): Promise<number> {
  try {
    const gasUsed = await signer.simulate(signerAddress, messages, memo);
    console.log(`Estimated gas usage: ${gasUsed}`);
    return gasUsed;
  } catch (error) {
    console.error('Simulation failed:', error);
    throw error;
  }
}
```

### Enhanced Gas Estimation with Safety Margins

```typescript
interface GasEstimationOptions {
  safetyMargin: number;      // Multiplier for gas estimation (e.g., 1.3 = 30% buffer)
  maxGasLimit: number;       // Maximum allowed gas per transaction
  fallbackGas: number;       // Default gas if simulation fails
}

class AdvancedGasEstimator {
  private signer: CheqdSigningStargateClient;
  private options: GasEstimationOptions;

  constructor(signer: CheqdSigningStargateClient, options?: Partial<GasEstimationOptions>) {
    this.signer = signer;
    this.options = {
      safetyMargin: 1.3,
      maxGasLimit: 30_000_000,
      fallbackGas: 200_000,
      ...options
    };
  }

  async estimateGasWithSafety(
    signerAddress: string,
    messages: EncodeObject[],
    memo?: string
  ): Promise<{ estimatedGas: number; recommendedGas: number; safeGas: number }> {
    try {
      // Perform simulation
      const estimatedGas = await this.signer.simulate(signerAddress, messages, memo);
      
      // Apply safety margin
      const recommendedGas = Math.ceil(estimatedGas * this.options.safetyMargin);
      
      // Ensure within limits
      const safeGas = Math.min(recommendedGas, this.options.maxGasLimit);
      
      return {
        estimatedGas,
        recommendedGas,
        safeGas
      };
    } catch (error) {
      console.warn('Gas simulation failed, using fallback:', error);
      return {
        estimatedGas: this.options.fallbackGas,
        recommendedGas: this.options.fallbackGas,
        safeGas: this.options.fallbackGas
      };
    }
  }

  async calculateOptimalFee(
    signerAddress: string,
    messages: EncodeObject[],
    gasPrice: GasPrice,
    memo?: string
  ): Promise<DidStdFee> {
    const { safeGas } = await this.estimateGasWithSafety(signerAddress, messages, memo);
    
    return {
      amount: [coin(
        Math.ceil(safeGas * parseFloat(gasPrice.amount.toString())).toString(),
        gasPrice.denom
      )],
      gas: safeGas.toString(),
      payer: signerAddress
    };
  }
}
```

### Bulk Gas Estimation for Multiple Transactions

```typescript
interface BulkEstimationResult {
  totalGas: number;
  individualGas: number[];
  averageGas: number;
  maxGas: number;
  minGas: number;
}

async function estimateBulkGas(
  signer: CheqdSigningStargateClient,
  signerAddress: string,
  messageGroups: EncodeObject[][],
  memo?: string
): Promise<BulkEstimationResult> {
  // Simulate all transactions in parallel
  const gasEstimates = await Promise.all(
    messageGroups.map(messages => 
      signer.simulate(signerAddress, messages, memo).catch(error => {
        console.warn(`Simulation failed for batch:`, error);
        return 200_000; // Fallback gas
      })
    )
  );

  const totalGas = gasEstimates.reduce((sum, gas) => sum + gas, 0);
  
  return {
    totalGas,
    individualGas: gasEstimates,
    averageGas: Math.ceil(totalGas / gasEstimates.length),
    maxGas: Math.max(...gasEstimates),
    minGas: Math.min(...gasEstimates)
  };
}
```

## Transaction Batching Strategies

The SDK provides an intelligent `batchMessages()` method that optimally groups transactions based on gas limits:

### Core Batching Implementation

```typescript
interface MessageBatch {
  batches: EncodeObject[][];  // Groups of messages
  gas: number[];              // Gas usage per batch
}

// The SDK's batchMessages method automatically:
// 1. Simulates each individual message
// 2. Groups messages to stay within gas limits
// 3. Returns optimized batches with gas estimates

async function createOptimizedBatches(
  signer: CheqdSigningStargateClient,
  signerAddress: string,
  messages: EncodeObject[],
  maxGasLimit: number = 30_000_000,
  memo?: string
): Promise<MessageBatch> {
  return await signer.batchMessages(messages, signerAddress, memo, maxGasLimit);
}
```

### Advanced Batching Strategies

#### 1. Priority-Based Batching

```typescript
interface PriorityMessage {
  message: EncodeObject;
  priority: 'high' | 'medium' | 'low';
  estimatedGas?: number;
}

class PriorityBatcher {
  private signer: CheqdSigningStargateClient;
  private maxGasLimit: number;

  constructor(signer: CheqdSigningStargateClient, maxGasLimit: number = 30_000_000) {
    this.signer = signer;
    this.maxGasLimit = maxGasLimit;
  }

  async createPriorityBatches(
    signerAddress: string,
    priorityMessages: PriorityMessage[],
    memo?: string
  ): Promise<{ 
    highPriorityBatches: MessageBatch;
    mediumPriorityBatches: MessageBatch;
    lowPriorityBatches: MessageBatch;
  }> {
    // Group by priority
    const grouped = {
      high: priorityMessages.filter(pm => pm.priority === 'high').map(pm => pm.message),
      medium: priorityMessages.filter(pm => pm.priority === 'medium').map(pm => pm.message),
      low: priorityMessages.filter(pm => pm.priority === 'low').map(pm => pm.message)
    };

    // Batch each priority group
    const [highPriorityBatches, mediumPriorityBatches, lowPriorityBatches] = await Promise.all([
      this.signer.batchMessages(grouped.high, signerAddress, memo, this.maxGasLimit),
      this.signer.batchMessages(grouped.medium, signerAddress, memo, this.maxGasLimit),
      this.signer.batchMessages(grouped.low, signerAddress, memo, this.maxGasLimit)
    ]);

    return {
      highPriorityBatches,
      mediumPriorityBatches,
      lowPriorityBatches
    };
  }
}
```

#### 2. Smart Batching with Gas Optimization

```typescript
interface BatchingMetrics {
  totalMessages: number;
  totalBatches: number;
  averageMessagesPerBatch: number;
  gasUtilization: number;
  estimatedCostSaving: number;
}

class SmartBatcher {
  private signer: CheqdSigningStargateClient;
  
  constructor(signer: CheqdSigningStargateClient) {
    this.signer = signer;
  }

  async createSmartBatches(
    signerAddress: string,
    messages: EncodeObject[],
    options: {
      maxGasLimit?: number;
      targetUtilization?: number; // Target gas utilization percentage
      memo?: string;
    } = {}
  ): Promise<{ batches: MessageBatch; metrics: BatchingMetrics }> {
    const {
      maxGasLimit = 30_000_000,
      targetUtilization = 0.85, // Use 85% of max gas limit
      memo
    } = options;

    // Adjust max gas based on target utilization
    const adjustedMaxGas = Math.floor(maxGasLimit * targetUtilization);

    // Create batches
    const batches = await this.signer.batchMessages(
      messages,
      signerAddress,
      memo,
      adjustedMaxGas
    );

    // Calculate metrics
    const totalGas = batches.gas.reduce((sum, gas) => sum + gas, 0);
    const averageGasPerBatch = totalGas / batches.batches.length;
    const gasUtilization = averageGasPerBatch / adjustedMaxGas;
    
    // Estimate cost savings (vs individual transactions)
    const individualTxCost = messages.length * 50_000; // Base transaction cost
    const batchedTxCost = batches.batches.length * 50_000;
    const estimatedCostSaving = ((individualTxCost - batchedTxCost) / individualTxCost) * 100;

    const metrics: BatchingMetrics = {
      totalMessages: messages.length,
      totalBatches: batches.batches.length,
      averageMessagesPerBatch: messages.length / batches.batches.length,
      gasUtilization,
      estimatedCostSaving
    };

    return { batches, metrics };
  }
}
```

#### 3. Transaction Type-Aware Batching

```typescript
interface TypedMessage {
  message: EncodeObject;
  type: 'did' | 'resource' | 'cosmos' | 'governance';
  complexity: 'simple' | 'complex';
}

class TypeAwareBatcher {
  private signer: CheqdSigningStargateClient;

  constructor(signer: CheqdSigningStargateClient) {
    this.signer = signer;
  }

  private categorizeMessage(message: EncodeObject): TypedMessage {
    const { typeUrl } = message;
    
    let type: TypedMessage['type'];
    let complexity: TypedMessage['complexity'] = 'simple';

    if (typeUrl.includes('did')) {
      type = 'did';
      complexity = 'complex'; // DID operations are typically more complex
    } else if (typeUrl.includes('resource')) {
      type = 'resource';
      complexity = 'complex'; // Resource operations can be large
    } else if (typeUrl.includes('gov')) {
      type = 'governance';
      complexity = 'simple';
    } else {
      type = 'cosmos';
      complexity = 'simple';
    }

    return { message, type, complexity };
  }

  async createTypedBatches(
    signerAddress: string,
    messages: EncodeObject[],
    memo?: string
  ): Promise<{
    didBatches: MessageBatch;
    resourceBatches: MessageBatch;
    cosmosBatches: MessageBatch;
    governanceBatches: MessageBatch;
  }> {
    // Categorize messages
    const typedMessages = messages.map(msg => this.categorizeMessage(msg));

    // Group by type
    const grouped = {
      did: typedMessages.filter(tm => tm.type === 'did').map(tm => tm.message),
      resource: typedMessages.filter(tm => tm.type === 'resource').map(tm => tm.message),
      cosmos: typedMessages.filter(tm => tm.type === 'cosmos').map(tm => tm.message),
      governance: typedMessages.filter(tm => tm.type === 'governance').map(tm => tm.message)
    };

    // Different gas limits based on complexity
    const gasLimits = {
      did: 20_000_000,       // Lower limit for complex DID operations
      resource: 15_000_000,   // Even lower for resource operations
      cosmos: 30_000_000,     // Full limit for simple operations
      governance: 25_000_000  // Moderate limit for governance
    };

    // Batch each type with appropriate gas limits
    const [didBatches, resourceBatches, cosmosBatches, governanceBatches] = await Promise.all([
      grouped.did.length > 0 ? this.signer.batchMessages(grouped.did, signerAddress, memo, gasLimits.did) : { batches: [], gas: [] },
      grouped.resource.length > 0 ? this.signer.batchMessages(grouped.resource, signerAddress, memo, gasLimits.resource) : { batches: [], gas: [] },
      grouped.cosmos.length > 0 ? this.signer.batchMessages(grouped.cosmos, signerAddress, memo, gasLimits.cosmos) : { batches: [], gas: [] },
      grouped.governance.length > 0 ? this.signer.batchMessages(grouped.governance, signerAddress, memo, gasLimits.governance) : { batches: [], gas: [] }
    ]);

    return {
      didBatches,
      resourceBatches,
      cosmosBatches,
      governanceBatches
    };
  }
}
```

## Fee Optimization Patterns

### Dynamic Fee Calculation with Simulation

```typescript
interface OptimizedFeeStrategy {
  strategy: 'conservative' | 'balanced' | 'aggressive';
  gasMultiplier: number;
  maxGasPrice?: GasPrice;
  fallbackFee?: DidStdFee;
}

class FeeOptimizer {
  private signer: CheqdSigningStargateClient;
  private feemarketModule?: FeemarketModule;

  constructor(signer: CheqdSigningStargateClient, feemarketModule?: FeemarketModule) {
    this.signer = signer;
    this.feemarketModule = feemarketModule;
  }

  async optimizeFee(
    signerAddress: string,
    messages: EncodeObject[],
    strategy: OptimizedFeeStrategy,
    memo?: string
  ): Promise<DidStdFee> {
    try {
      // 1. Simulate to get gas estimate
      const estimatedGas = await this.signer.simulate(signerAddress, messages, memo);
      
      // 2. Apply strategy-based multiplier
      const adjustedGas = Math.ceil(estimatedGas * strategy.gasMultiplier);
      
      // 3. Get current gas price (dynamic or fallback)
      let gasPrice: GasPrice;
      
      if (this.feemarketModule) {
        try {
          gasPrice = await this.feemarketModule.generateGasPrice('ncheq');
        } catch (error) {
          console.warn('Failed to get dynamic gas price, using fallback');
          gasPrice = strategy.maxGasPrice || GasPrice.fromString('25000ncheq');
        }
      } else {
        gasPrice = strategy.maxGasPrice || GasPrice.fromString('25000ncheq');
      }
      
      // 4. Calculate fee
      const feeAmount = Math.ceil(adjustedGas * parseFloat(gasPrice.amount.toString()));
      
      return {
        amount: [coin(feeAmount.toString(), gasPrice.denom)],
        gas: adjustedGas.toString(),
        payer: signerAddress
      };
      
    } catch (error) {
      console.error('Fee optimization failed:', error);
      return strategy.fallbackFee || {
        amount: [coin('500000', 'ncheq')],
        gas: '200000',
        payer: signerAddress
      };
    }
  }

  // Predefined strategies
  static readonly strategies = {
    conservative: {
      strategy: 'conservative' as const,
      gasMultiplier: 1.5, // 50% buffer
    },
    balanced: {
      strategy: 'balanced' as const,
      gasMultiplier: 1.3, // 30% buffer
    },
    aggressive: {
      strategy: 'aggressive' as const,
      gasMultiplier: 1.1, // 10% buffer
    }
  };
}
```

### Batch Fee Optimization

```typescript
interface BatchFeeResult {
  totalFee: DidStdFee;
  individualFees: DidStdFee[];
  savings: {
    absolute: number;
    percentage: number;
  };
}

async function optimizeBatchFees(
  signer: CheqdSigningStargateClient,
  signerAddress: string,
  batches: MessageBatch,
  gasPrice: GasPrice,
  strategy: OptimizedFeeStrategy
): Promise<BatchFeeResult> {
  const individualFees: DidStdFee[] = [];
  let totalGasUsed = 0;
  let totalFeeAmount = 0;

  // Calculate fees for each batch
  for (let i = 0; i < batches.batches.length; i++) {
    const batchGas = batches.gas[i];
    const adjustedGas = Math.ceil(batchGas * strategy.gasMultiplier);
    const feeAmount = Math.ceil(adjustedGas * parseFloat(gasPrice.amount.toString()));
    
    const batchFee: DidStdFee = {
      amount: [coin(feeAmount.toString(), gasPrice.denom)],
      gas: adjustedGas.toString(),
      payer: signerAddress
    };
    
    individualFees.push(batchFee);
    totalGasUsed += adjustedGas;
    totalFeeAmount += feeAmount;
  }

  // Calculate total fee
  const totalFee: DidStdFee = {
    amount: [coin(totalFeeAmount.toString(), gasPrice.denom)],
    gas: totalGasUsed.toString(),
    payer: signerAddress
  };

  // Calculate savings vs individual transactions
  const messagesCount = batches.batches.reduce((sum, batch) => sum + batch.length, 0);
  const individualTxFee = 50000; // Base transaction fee
  const potentialIndividualCost = messagesCount * individualTxFee;
  const actualBatchCost = batches.batches.length * individualTxFee;
  const absoluteSavings = potentialIndividualCost - actualBatchCost;
  const percentageSavings = (absoluteSavings / potentialIndividualCost) * 100;

  return {
    totalFee,
    individualFees,
    savings: {
      absolute: absoluteSavings,
      percentage: percentageSavings
    }
  };
}
```

## Production Implementation

### Complete Implementation Example

```typescript
import { 
  CheqdSigningStargateClient, 
  createCheqdSDK, 
  FeemarketModule,
  createDefaultCheqdRegistry 
} from '@cheqd/sdk';

class ProductionTransactionManager {
  private signer: CheqdSigningStargateClient;
  private gasEstimator: AdvancedGasEstimator;
  private smartBatcher: SmartBatcher;
  private feeOptimizer: FeeOptimizer;

  constructor(
    signer: CheqdSigningStargateClient,
    feemarketModule?: FeemarketModule
  ) {
    this.signer = signer;
    this.gasEstimator = new AdvancedGasEstimator(signer);
    this.smartBatcher = new SmartBatcher(signer);
    this.feeOptimizer = new FeeOptimizer(signer, feemarketModule);
  }

  async executeOptimizedBatch(
    signerAddress: string,
    messages: EncodeObject[],
    options: {
      strategy?: OptimizedFeeStrategy;
      maxGasLimit?: number;
      memo?: string;
      dryRun?: boolean;
    } = {}
  ): Promise<{
    results?: DeliverTxResponse[];
    batches: MessageBatch;
    fees: BatchFeeResult;
    metrics: BatchingMetrics;
    gasEstimates: BulkEstimationResult;
  }> {
    const {
      strategy = FeeOptimizer.strategies.balanced,
      maxGasLimit = 30_000_000,
      memo,
      dryRun = false
    } = options;

    console.log(`Processing ${messages.length} messages with ${strategy.strategy} strategy`);

    // 1. Create optimized batches
    const { batches, metrics } = await this.smartBatcher.createSmartBatches(
      signerAddress,
      messages,
      { maxGasLimit, memo }
    );

    console.log(`Created ${batches.batches.length} optimized batches`);

    // 2. Get bulk gas estimates
    const gasEstimates = await estimateBulkGas(
      this.signer,
      signerAddress,
      batches.batches,
      memo
    );

    // 3. Get current gas price and optimize fees
    const gasPrice = await this.getGasPrice();
    const fees = await optimizeBatchFees(
      this.signer,
      signerAddress,
      batches,
      gasPrice,
      strategy
    );

    console.log(`Total estimated cost: ${fees.totalFee.amount[0].amount} ${fees.totalFee.amount[0].denom}`);
    console.log(`Estimated savings: ${fees.savings.percentage.toFixed(2)}%`);

    // 4. Execute transactions (unless dry run)
    let results: DeliverTxResponse[] | undefined;
    
    if (!dryRun) {
      results = [];
      
      for (let i = 0; i < batches.batches.length; i++) {
        const batch = batches.batches[i];
        const batchFee = fees.individualFees[i];
        
        try {
          console.log(`Executing batch ${i + 1}/${batches.batches.length} with ${batch.length} messages`);
          
          const result = await this.signer.signAndBroadcast(
            signerAddress,
            batch,
            batchFee,
            memo
          );
          
          results.push(result);
          console.log(`Batch ${i + 1} executed successfully: ${result.transactionHash}`);
          
        } catch (error) {
          console.error(`Batch ${i + 1} failed:`, error);
          throw new Error(`Batch execution failed at batch ${i + 1}: ${error}`);
        }
      }
    }

    return {
      results,
      batches,
      fees,
      metrics,
      gasEstimates
    };
  }

  private async getGasPrice(): Promise<GasPrice> {
    if (this.feeOptimizer['feemarketModule']) {
      try {
        return await this.feeOptimizer['feemarketModule'].generateGasPrice('ncheq');
      } catch (error) {
        console.warn('Dynamic gas price unavailable, using fallback');
      }
    }
    return GasPrice.fromString('25000ncheq');
  }

  // Health check method
  async validateSetup(): Promise<{
    simulationWorking: boolean;
    batchingWorking: boolean;
    feeCalculationWorking: boolean;
    gasPrice: GasPrice;
  }> {
    const testMessage: EncodeObject = {
      typeUrl: '/cosmos.bank.v1beta1.MsgSend',
      value: {
        fromAddress: 'cheqd1test',
        toAddress: 'cheqd1test2',
        amount: [coin('1', 'ncheq')]
      }
    };

    let simulationWorking = false;
    let batchingWorking = false;
    let feeCalculationWorking = false;
    let gasPrice: GasPrice;

    try {
      // Test simulation (will fail but should not throw for wrong reason)
      await this.signer.simulate('cheqd1test', [testMessage]);
      simulationWorking = true;
    } catch (error) {
      simulationWorking = error.message.includes('account') || error.message.includes('not found');
    }

    try {
      // Test batching
      await this.signer.batchMessages([testMessage], 'cheqd1test');
      batchingWorking = true;
    } catch (error) {
      batchingWorking = error.message.includes('account') || error.message.includes('not found');
    }

    try {
      gasPrice = await this.getGasPrice();
      feeCalculationWorking = true;
    } catch (error) {
      gasPrice = GasPrice.fromString('25000ncheq');
      feeCalculationWorking = false;
    }

    return {
      simulationWorking,
      batchingWorking,
      feeCalculationWorking,
      gasPrice
    };
  }
}

// Example usage
async function main() {
  const signer = await CheqdSigningStargateClient.connectWithSigner(
    'https://rpc.cheqd.net',
    wallet,
    { 
      registry: createDefaultCheqdRegistry(),
      endpoint: 'https://rpc.cheqd.net'
    }
  );

  const sdk = await createCheqdSDK({
    modules: [FeemarketModule],
    network: CheqdNetwork.Mainnet,
    wallet: wallet,
    rpcEndpoint: 'https://rpc.cheqd.net'
  });

  const txManager = new ProductionTransactionManager(signer, sdk.modules.feemarket);

  // Validate setup
  const health = await txManager.validateSetup();
  console.log('Setup validation:', health);

  // Execute optimized batch
  const result = await txManager.executeOptimizedBatch(
    signerAddress,
    messages,
    {
      strategy: FeeOptimizer.strategies.balanced,
      dryRun: false // Set to true for testing
    }
  );

  console.log('Execution completed:', {
    batchCount: result.batches.batches.length,
    totalMessages: result.metrics.totalMessages,
    gasUtilization: result.metrics.gasUtilization,
    costSavings: result.fees.savings.percentage
  });
}
```

## Error Handling and Resilience

### Robust Error Handling

```typescript
interface SimulationError {
  type: 'network' | 'account' | 'gas' | 'validation' | 'unknown';
  message: string;
  recoverable: boolean;
}

class ResilientSimulator {
  private signer: CheqdSigningStargateClient;
  private retryConfig: {
    maxRetries: number;
    baseDelay: number;
    maxDelay: number;
  };

  constructor(
    signer: CheqdSigningStargateClient,
    retryConfig = { maxRetries: 3, baseDelay: 1000, maxDelay: 5000 }
  ) {
    this.signer = signer;
    this.retryConfig = retryConfig;
  }

  private classifyError(error: any): SimulationError {
    const message = error.message || error.toString();
    
    if (message.includes('account') && message.includes('not found')) {
      return { type: 'account', message, recoverable: false };
    } else if (message.includes('network') || message.includes('connection')) {
      return { type: 'network', message, recoverable: true };
    } else if (message.includes('gas') || message.includes('limit')) {
      return { type: 'gas', message, recoverable: false };
    } else if (message.includes('invalid') || message.includes('malformed')) {
      return { type: 'validation', message, recoverable: false };
    } else {
      return { type: 'unknown', message, recoverable: true };
    }
  }

  async simulateWithRetry(
    signerAddress: string,
    messages: EncodeObject[],
    memo?: string
  ): Promise<{ gasUsed: number; attempts: number; errors: SimulationError[] }> {
    const errors: SimulationError[] = [];
    let lastError: SimulationError | null = null;
    
    for (let attempt = 1; attempt <= this.retryConfig.maxRetries; attempt++) {
      try {
        const gasUsed = await this.signer.simulate(signerAddress, messages, memo);
        return { gasUsed, attempts: attempt, errors };
      } catch (error) {
        const classifiedError = this.classifyError(error);
        errors.push(classifiedError);
        lastError = classifiedError;
        
        // Don't retry if error is not recoverable
        if (!classifiedError.recoverable) {
          break;
        }
        
        // Don't retry on last attempt
        if (attempt === this.retryConfig.maxRetries) {
          break;
        }
        
        // Exponential backoff
        const delay = Math.min(
          this.retryConfig.baseDelay * Math.pow(2, attempt - 1),
          this.retryConfig.maxDelay
        );
        
        console.warn(`Simulation attempt ${attempt} failed, retrying in ${delay}ms:`, classifiedError.message);
        await new Promise(resolve => setTimeout(resolve, delay));
      }
    }
    
    throw new Error(`Simulation failed after ${this.retryConfig.maxRetries} attempts. Last error: ${lastError?.message}`);
  }

  async batchSimulateWithFallback(
    signerAddress: string,
    messageGroups: EncodeObject[][],
    memo?: string,
    fallbackGas: number = 200_000
  ): Promise<{
    gasEstimates: number[];
    successfulSimulations: number;
    failedSimulations: number;
    totalFallbacks: number;
  }> {
    const gasEstimates: number[] = [];
    let successfulSimulations = 0;
    let failedSimulations = 0;
    let totalFallbacks = 0;

    for (const messages of messageGroups) {
      try {
        const { gasUsed } = await this.simulateWithRetry(signerAddress, messages, memo);
        gasEstimates.push(gasUsed);
        successfulSimulations++;
      } catch (error) {
        console.warn(`Simulation failed for batch, using fallback gas:`, error);
        gasEstimates.push(fallbackGas);
        failedSimulations++;
        totalFallbacks++;
      }
    }

    return {
      gasEstimates,
      successfulSimulations,
      failedSimulations,
      totalFallbacks
    };
  }
}
```

### Circuit Breaker Pattern

```typescript
interface CircuitBreakerState {
  failures: number;
  lastFailure?: Date;
  state: 'closed' | 'open' | 'half-open';
}

class SimulationCircuitBreaker {
  private state: CircuitBreakerState = {
    failures: 0,
    state: 'closed'
  };
  
  private readonly threshold: number;
  private readonly resetTimeout: number;
  private readonly fallbackGas: number;

  constructor(
    threshold: number = 5,
    resetTimeout: number = 60000, // 1 minute
    fallbackGas: number = 200_000
  ) {
    this.threshold = threshold;
    this.resetTimeout = resetTimeout;
    this.fallbackGas = fallbackGas;
  }

  async executeWithCircuitBreaker<T>(
    operation: () => Promise<T>,
    fallback: () => T
  ): Promise<T> {
    // Check if circuit should reset
    if (this.state.state === 'open' && this.shouldReset()) {
      this.state.state = 'half-open';
    }

    // If circuit is open, use fallback immediately
    if (this.state.state === 'open') {
      console.warn('Circuit breaker is open, using fallback');
      return fallback();
    }

    try {
      const result = await operation();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      
      if (this.state.state === 'open') {
        console.warn('Circuit breaker opened, using fallback');
        return fallback();
      }
      
      throw error;
    }
  }

  private shouldReset(): boolean {
    return this.state.lastFailure &&
      Date.now() - this.state.lastFailure.getTime() > this.resetTimeout;
  }

  private onSuccess(): void {
    this.state.failures = 0;
    this.state.state = 'closed';
  }

  private onFailure(): void {
    this.state.failures++;
    this.state.lastFailure = new Date();
    
    if (this.state.failures >= this.threshold) {
      this.state.state = 'open';
      console.warn(`Circuit breaker opened after ${this.state.failures} failures`);
    }
  }

  getState(): CircuitBreakerState {
    return { ...this.state };
  }
}
```

## Built-in Transaction Retry Policy

The cheqd SDK includes a sophisticated retry mechanism in the `broadcastTx` method that automatically handles transient network failures and ensures reliable transaction delivery. Understanding this built-in functionality helps you design more robust applications.

### Retry Policy Overview

The `CheqdSigningStargateClient.broadcastTx()` method implements an enhanced retry policy with the following features:

| Feature | Description | Default Value |
|---------|-------------|---------------|
| **Max Retries** | Maximum number of broadcast attempts | 3 |
| **Timeout** | Maximum wait time for transaction inclusion | 60,000 ms (60s) |
| **Poll Interval** | Frequency of checking for transaction inclusion | 3,000 ms (3s) |
| **Backoff Delay** | Brief delay between retry attempts | 1,000 ms (1s) |

### How the Retry Policy Works

```typescript
// Built into the SDK - no additional code needed
const result = await signer.broadcastTx(
  txBytes,
  60_000,    // timeoutMs
  3_000,     // pollIntervalMs
  3          // maxRetries
);
```

**Retry Flow:**

1. **Initial Broadcast**: Attempts to broadcast transaction using `broadcastTxSync`
2. **Transaction Tracking**: Records transaction hash for monitoring
3. **Polling Phase**: Continuously checks for transaction inclusion in blocks
4. **Failure Handling**: On broadcast failure, implements exponential backoff
5. **Retry Logic**: Retries up to `maxRetries` times with same signed transaction
6. **Double-Spend Prevention**: Reuses identical signed transaction bytes across retries

### Retry Scenarios

The retry policy handles several common failure scenarios:

```typescript
// Example scenarios handled automatically:

// 1. Network congestion - retry broadcast
try {
  const result = await signer.signAndBroadcast(address, messages, 'auto');
  // SDK automatically retries on temporary network issues
} catch (error) {
  if (error.name === 'TimeoutError') {
    console.log('Transaction hash:', error.txHash); // Available even on timeout
  }
}

// 2. Node synchronization issues - poll longer
// The SDK continues polling until timeout or success

// 3. Temporary RPC failures - retry with backoff
// Built-in 1-second backoff between retry attempts
```

### Error Handling and Transaction Tracking

The retry policy provides enhanced error information:

```typescript
try {
  const result = await signer.broadcastTx(txBytes, 60000, 3000, 3);
  console.log('Transaction successful:', result.transactionHash);
} catch (error) {
  if (error.name === 'TimeoutError') {
    // Even on timeout, transaction hash is available
    console.log('Transaction may still be processed:', error.txHash);
    
    // You can continue monitoring manually if needed
    const finalResult = await signer.getTx(error.txHash);
  } else if (error.name === 'BroadcastTxError') {
    // Transaction was rejected during CheckTx
    console.error('Transaction rejected:', error.log);
  }
}
```

### Integration with Gas Simulation

The retry policy works seamlessly with gas simulation and fee calculation:

```typescript
// Complete example with gas simulation and retry policy
async function executeTransactionWithRetries(
  signer: CheqdSigningStargateClient,
  address: string,
  messages: EncodeObject[]
): Promise<DeliverTxResponse> {
  // 1. Simulate for accurate gas estimation
  const gasEstimate = await signer.simulate(address, messages, '');
  const gasWithMargin = Math.round(gasEstimate * 1.3);
  
  // 2. Calculate fees based on simulation
  const fee = calculateDidFee(gasWithMargin, signer.gasPrice);
  
  // 3. Sign transaction
  const txRaw = await signer.sign(address, messages, fee, '');
  const txBytes = TxRaw.encode(txRaw).finish();
  
  // 4. Broadcast with built-in retry policy
  return signer.broadcastTx(
    txBytes,
    90_000,  // Extended timeout for complex transactions
    2_000,   // More frequent polling
    5        // More retries for critical operations
  );
}
```

### Production Configuration

For production deployments, you can tune the retry parameters based on your requirements:

```typescript
// Conservative configuration for critical transactions
const conservativeResult = await signer.broadcastTx(
  txBytes,
  120_000,  // 2-minute timeout
  2_000,    // Poll every 2 seconds
  5         // 5 retry attempts
);

// Fast configuration for less critical operations  
const fastResult = await signer.broadcastTx(
  txBytes,
  30_000,   // 30-second timeout
  5_000,    // Poll every 5 seconds  
  2         // 2 retry attempts
);
```

### Monitoring Retry Behavior

You can implement monitoring to track retry patterns:

```typescript
class TransactionMonitor {
  private retryStats = {
    attempts: [] as number[],
    timeouts: 0,
    successes: 0
  };

  async monitoredBroadcast(
    signer: CheqdSigningStargateClient,
    txBytes: Uint8Array
  ): Promise<DeliverTxResponse> {
    const startTime = Date.now();
    
    try {
      const result = await signer.broadcastTx(txBytes, 60000, 3000, 3);
      
      this.retryStats.successes++;
      const duration = Date.now() - startTime;
      console.log(`Transaction succeeded in ${duration}ms`);
      
      return result;
    } catch (error) {
      if (error.name === 'TimeoutError') {
        this.retryStats.timeouts++;
        console.warn('Transaction timeout, but may still be processed');
      }
      throw error;
    }
  }
  
  getStats() {
    return { ...this.retryStats };
  }
}
```

### Key Benefits

The built-in retry policy provides several advantages:

1. **Reliability**: Automatic handling of transient network issues
2. **Transaction Safety**: Prevents double-spending through transaction reuse
3. **Visibility**: Maintains transaction hash tracking even on failures
4. **Configurability**: Tunable parameters for different use cases
5. **Integration**: Works seamlessly with gas simulation and fee calculation

This retry mechanism significantly improves the reliability of transaction broadcasting without requiring additional application-level retry logic.

## Best Practices Summary

### Gas Simulation Best Practices

1. **Always Use Safety Margins**: Apply 20-50% buffer to simulated gas estimates
2. **Handle Simulation Failures**: Implement fallback gas values for production systems
3. **Batch Simulations**: Simulate multiple transactions in parallel for efficiency
4. **Monitor Gas Usage**: Track actual vs estimated gas consumption
5. **Network-Specific Limits**: Respect network gas limits and consensus parameters

### Batching Best Practices

1. **Optimal Batch Size**: Target 80-90% of max gas limit for efficiency
2. **Transaction Prioritization**: Group high-priority transactions together
3. **Type-Aware Batching**: Consider transaction complexity when batching
4. **Error Isolation**: Design batches to minimize failure propagation
5. **Progress Monitoring**: Implement progress tracking for large batch operations

### Production Deployment

1. **Comprehensive Testing**: Test all scenarios in development environments
2. **Monitoring and Alerting**: Track gas usage, failure rates, and cost optimization
3. **Graceful Degradation**: Implement fallback strategies for all failure modes
4. **Performance Metrics**: Monitor transaction throughput and cost effectiveness
5. **Regular Optimization**: Continuously tune parameters based on network conditions

This comprehensive approach to gas simulation and transaction batching will provide optimal performance, cost efficiency, and reliability for production cheqd SDK implementations.
